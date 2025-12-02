# C2S
## 预训练数据
人类免疫系统的跨组织单细胞图谱：该图谱包含大概 36 万个免疫细胞，来自 12 个供体、16 个不同组织（血液、淋巴结、骨髓、肠道、肺、肝脏等），每个细胞都有 scRNA-seq 表达矩阵和经过 CellTypist 注释的精细免疫细胞类型标签（[Cross-tissue immune cell analysis reveals tissue-specific features in humans](https://www.tissueimmunecellatlas.org/)）
## 微调数据
- 免疫组织：[Cross-tissue immune cell analysis reveals tissue-specific features in humans](https://www.tissueimmunecellatlas.org/)
- 细胞因子刺激:两套scRNA-seq 扰动数据（[Causal identification of single-cell experimental perturbation effects with CINEMA-OT](https://doi.org/10.5061/dryad.4xgxd25g1)）
- 多组织：[CellxGene](https://cellxgene.cziscience.com/datasets)
## 评估数据
- 单细胞 PBMC 数据：组合干扰素/细胞因子刺激 PBMC 数据（[Causal identification of single-cell experimental perturbation effects with CINEMA-OT](https://doi.org/10.5061/dryad.4xgxd25g1)）
- L1000：大规模 bulk 转录组，不是单细胞；每个样本是某个细胞系在不同药物、剂量、时间点处理下的基因表达签名。在c2s中测试模型在药物/基因扰动签名上的理解能力（[L1000 bulk RNA data](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE70138&utm_source=chatgpt.com)）
- GTEx：经典的人体多组织 bulk RNA-seq 图谱，几十种组织、上千个样本。在c2s中用于测试C2S在bulk数据上做组织分类/表型预测的能力([GTEx](https://www.gtexportal.org/home/datasets?utm_source=chatgpt.com))
# C2S-Scale
## 预训练数据
使用来自[CellxGene](https://cellxgene.cziscience.com/)和[Human Cell Atlas (HCA)](https://www.humancellatlas.org/)的800多个公共scRNA-seq 数据集，总共超过 5700 万个细胞（人 + 小鼠）
## 微调数据
1. Cell type annotation & cell generation
- [跨组织免疫细胞数据集](https://www.tissueimmunecellatlas.org/)
- [人肺类器官数据集](https://cellxgene.cziscience.com/collections/e9cf4e8d-05ed-4d95-b550-af35ca219390)
2. Spatial niche 任务
- [CosMx Human Liver 空间转录组数据](https://nanostring.com/products/cosmx-spatial-molecular-imager/ffpe-dataset/human-liver-rna-ffpe-dataset/)
3. 干预 / 药物反应
- [Dong et al. Cytokine 单细胞数据](https://singlecell.broadinstitute.org/single_cell/study/SCP1845/cross-tissue-immune-cell-analysis-reveals-tissue-specific-features-in-humans)
- [L1000 Bulk 药物反应数据](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE92742)
# CellWhisperer
## 预训练数据
1. [GEO 通过 ARCHS4 统一重算的 bulk](https://medical-epigenomics.org/papers/schaefer2025cellwhisperer/data/datasets/archs4_geo/)：从 GEO（经 ARCHS4 重处理）取了 705,430 个 human RNA-seq 样本（主要是 bulk，也包括一些已做 pseudo-bulk 的数据）。每个样本是一条基因表达向量+一段自然语言文本说明。
2. [CELLxGENE Census：scRNA→pseudo-bulk + 文本](https://medical-epigenomics.org/papers/schaefer2025cellwhisperer/data/datasets/cellxgene_census/)：使用 CZ CELLxGENE Census 汇总的人类 scRNA-seq 数据，把同一 cell type/组织 / 条件 的细胞先 聚合成 pseudo-bulk（减少噪音，也方便和 bulk 数据统一），最终得到 376,983 条 pseudo-bulk 转录组，同样配上自然语言描述。
## 评估数据集
1. 细胞类型 / 组织 / 疾病 zero-shot 预测
- [人类多器官单细胞图谱：](https://medical-epigenomics.org/papers/schaefer2025cellwhisperer/data/datasets/tabula_sapiens/)48 万个细胞，24 个器官，详细 cell type 注释。
- [ImmGen 人类免疫细胞 bulk RNA-seq：](https://medical-epigenomics.org/papers/schaefer2025cellwhisperer/data/datasets/immgen/)42 条 bulk 转录组，5 类免疫细胞，人工注释很好。
- [Asian Immune Diversity Atlas (AIDA)：](https://medical-epigenomics.org/papers/schaefer2025cellwhisperer/data/datasets/aida/)100万PBMC，来自474名东亚/东南亚/南亚个体，21–34种免疫细胞类型，用来测试在非欧洲人群上的免疫细胞类型zero-shot预测。
- [Human Diseases dataset：](https://medical-epigenomics.org/papers/schaefer2025cellwhisperer/data/datasets/human_disease/)14,112条疾病相关bulk/pseudo-bulk转录组，覆盖229种疾病亚型，这些样本特意排除在训练集之外，只用来评估疾病、组织 zero-shot预测和gene-set识别性能。
2. 专门任务 / benchmark 数据
- [人胰腺scRNA meta-analysis数据集：](https://medical-epigenomics.org/papers/schaefer2025cellwhisperer/data/datasets/pancreas/)从多个研究中整合而来，含16,382个单细胞表达谱，cell type很相近、batch effect 比较重，是一个难的integration benchmark，用来评估 CellWhisperer embedding 在数据整合、batch 校正中的表现。
- [人胚胎发育 scRNA-seq（Human Development dataset）：](https://medical-epigenomics.org/papers/schaefer2025cellwhisperer/data/datasets/development/)作者从6篇文献中整理 95,092 个胚胎期单细胞，3–38天post-fertilization。
# LangCell
## 预训练数据
[scLibrary](https://huggingface.co/datasets/Toycat/scLibrary)：约 2750 万条 细胞-文本配对，全部来自 CELLxGENE 数据库，只选 人类（human），只保留 10x Genomics 测序协议的 scRNA-seq 数据
## 评估数据集
1. [外周血 PBMC 数据](https://www.10xgenomics.com/datasets/10-k-peripheral-blood-mononuclear-cells-pbm-cs-from-a-healthy-donor-single-indexed-3-1-standard-4-0-0?utm_source=chatgpt.com)
2. [人脑数据集](https://cellxgene.cziscience.com/collections/283d65eb-dd53-496d-adb7-7570c7caa443?utm_source=chatgpt.com)
3. [多组织数据集](https://figshare.com/articles/dataset/Tabula_Sapiens_release_1_0/14267219?utm_source=chatgpt.com)
