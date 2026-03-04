# 方案A（最稳健）：Tahoe-100M 上的 GRN-模块驱动 Unknown Masking（scGPT-like 预训练）

本方案的核心原则是：**尽量保持 scGPT 原始预训练目标与数据分布不变**，只在**数据侧（collator）**修改 *unknown 基因的选择策略*，以获得最稳定的训练行为。

---

## 目标

使用 **Tahoe-100M** 从零训练一个 **scGPT-like** 模型，并引入 **GRN 模块驱动的 unknown masking**：

对每个细胞（cell）：
1. 随机选一个 TF（优先从该 cell 中出现的 TF 里选）。
2. 获取该 TF 的 targets 列表（模块 module）。
3. 将 module targets 与该 cell 的基因集合取交集。
4. 将交集基因优先设为 **unknown**（module-first）。
5. 再从剩余基因中随机抽取一些设为 unknown，补齐到常规 unknown 比例。

---

## 前置条件 / 一致性要求

- ✅ GRN 已经在 **Tahoe token 空间**：`TF_id`、`Target_id` 均为 Tahoe gene 的 `token_id`（整数）。
- ✅ 无法映射到 Tahoe token_id 的 GRN 边应提前过滤掉。
- ✅ 构建映射结构：
  - `tf_to_targets: Dict[int, List[int]]`（targets **去重**，可选排序）

---

## A1. 细胞输入构造（与 Tahoe/scGPT 对齐）

- Tahoe-100M 为稀疏格式：每个 cell 的序列为 `[CLS] + nonzero_gene_ids`  
  - Tahoe 每条记录的第一个 token 可当作 `CLS`。
- 表达值：
  - 推荐 `log1p(counts)`（更稳）
  - `CLS` 的 value 置为 `0`
- 截断（truncation）：
  - 若 nonzero genes > `max_len`（建议 1200），则**随机采样**到 `max_len`
  - 这更贴近 scGPT 原始分布（稳定性更好）

---

## A2. unknown 总量（保持 scGPT 训练分布）

- unknown 比例从 `{0.25, 0.50, 0.75}` 随机采样（或先用固定 `0.5` 做基线）。
- 设 `N` 为该 cell 的非 CLS 基因数。
- 计算：
  - `K_total = round(frac * N)`
  - 约束 `K_total >= 1`

---

## A3. TF 选择策略（必须 prefer-in-cell）

- **优先从 cell 内出现的 TF 中选**：
  - `TF_candidates = cell_genes ∩ TF_set`
  - 若非空，则从 `TF_candidates` 中**均匀随机**选一个 TF
- 兜底策略：
  - 若 `TF_candidates` 为空，则从全体 `TF_set` 中均匀随机选 TF
- 建议记录 `tf_pick`（方便 W&B 分析）

---

## A4. 模块优先（module-first）unknown 选择

- 获取模块：
  - `module = tf_to_targets[tf_pick]`
- 与 cell 基因取交集：
  - `module_in_cell = module ∩ cell_gene_set`
- 建议 **keep_tf_known=True**（TF 本身不 mask）：
  - 若 `tf_pick ∈ module_in_cell`，将其从 `module_in_cell` 中移除
- 模块规模上限（稳定性保护）：
  - 若 `|module_in_cell| > cap`，随机下采样到 `cap`
  - 推荐：`cap = 256`
- 模块不得超过总 unknown 预算：
  - 若 `|module_in_cell| > K_total`，随机下采样到 `K_total`
- 初始化：
  - `unknown_set = module_in_cell`

---

## A5. 稳定性增强：模块命中为空时重抽 TF（强烈建议）

实际中 `module_in_cell` 可能为 0（模块 targets 在该 cell 中一个都不出现）。为减少“无模块信号”的退化样本：

- 若 `|module_in_cell| == 0`：
  - 按同样规则（prefer-in-cell）**重抽 TF**
- 推荐：
  - `max_resample = 3`
- 若重抽后仍为 0，则接受空模块并继续（避免死循环）

> 可选更强版本（初期不建议）：若 `|module_in_cell| < 2` 也重抽。

---

## A6. 随机补齐 unknown 到 K_total

- 设：
  - `remaining = cell_gene_set \ unknown_set`
- 从 `remaining` 随机抽取：
  - `K_total - |unknown_set|` 个基因加入 `unknown_set`
- 若 `keep_tf_known=True`：
  - 确保随机补齐不会把 `tf_pick` 加入 unknown
- 最终兜底：
  - 若 `unknown_set` 仍为空，强制随机选 1 个基因作为 unknown

---

## A7. 模型输入/输出格式（pcpt/gen 分段）

构造两段输入：

### 感知段（pcpt / known）
- 内容：`CLS + known genes`
- 提供：表达值（expression values）

### 生成段（gen / unknown）
- 内容：仅包含 unknown genes
- 输入不提供表达值（模型只能看到 gene identity）
- 训练标签：保存 `gen_values`（unknown 的真实表达）用于 loss

Padding / mask：
- 统一 pad mask 语义（例如 `True = PAD`），保证 pcpt 与 gen 一致。

---

## A8. 训练流程（核心不变）

- 两次 forward（scGPT-like）：
  1. **gene-prompt**：预测 unknown → 得 `loss1` 与 `cell_emb`
  2. **cell-prompt**：注入 `cell_emb` 再预测 → 得 `loss2`
- 总 loss：
  - `loss = loss1 + loss2`
- loss 只在 unknown（gen）位置计算（masked MSE）

---

## A9. 基于你当前统计的推荐默认值

结合你已验证的命中分布：

- `prefer_in_cell = True` ✅（必选）
- `keep_tf_known = True`
- `cap = 256`
- `unknown_fracs = {0.25, 0.50, 0.75}`（或先固定 0.5）
- `max_resample = 3`（条件：hit==0）
- `max_len = 1200`，截断为随机采样

---

## A10. W&B 监控指标（仅 rank0 写）

最低必须：
- `train/loss`, `val/loss`
- `train/module_unknown_n`（模块 unknown 数）
- `train/total_unknown_n`（总 unknown 数）
- `train/module_zero_rate`（模块命中为 0 的比例）

建议增加：
- `train/resample_used_rate`（重抽 TF 的比例）
- `train/tf_bin`（可选：TF 度数桶分布）
- `train/module_hit`（模块交集大小）

---

## 预期现象（Sanity Checks）

- GRN 模块信号存在但不会破坏训练分布（稳定优先）。
- 即使模块命中为空，随机 unknown 仍保证每个样本有有效 loss。
- `cap` 保证极端大模块不会主导计算与梯度。
- TF 重抽机制能显著降低 `module_zero_rate`。

---
