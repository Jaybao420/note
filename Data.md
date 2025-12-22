# C2S
## 预训练数据
人类免疫系统的跨组织单细胞图谱：该图谱包含大概 36 万个免疫细胞，来自 12 个供体、16 个不同组织（血液、淋巴结、骨髓、肠道、肺、肝脏等），每个细胞都有 scRNA-seq 表达矩阵和经过 CellTypist 注释的精细免疫细胞类型标签（[Cross-tissue immune cell analysis reveals tissue-specific features in humans](https://www.tissueimmunecellatlas.org/)）
| cell_id                       | cell_type                              | tissue | batch_condition | organism     | assay     | sex    |
| ----------------------------- | -------------------------------------- | ------ | --------------- | ------------ | --------- | ------ |
| Pan_T7935490_AAACCTGCAAATTGCC | CD4-positive helper T cell             | ileum  | A29             | Homo sapiens | 10x 5' v1 | female |
| Pan_T7935490_AAACGGGCATCTGGTA | CD8-positive, alpha-beta memory T cell | ileum  | A29             | Homo sapiens | 10x 5' v1 | female |
| Pan_T7935490_AAACGGGTCTTGCATT | CD8-positive, alpha-beta memory T cell | ileum  | A29             | Homo sapiens | 10x 5' v1 | female |
| Pan_T7935490_AAAGCAATCATCGCTC | CD8-positive, alpha-beta memory T cell | ileum  | A29             | Homo sapiens | 10x 5' v1 | female |
| Pan_T7935490_AAAGTAGCAGTCACTA | gamma-delta T cell                     | ileum  | A29             | Homo sapiens | 10x 5' v1 | female |

| var_id       | gene_name   | ensembl_id      |
| ------------ | ----------- | --------------- |
| MIR1302-2HG  | MIR1302-2HG | ENSG00000243485 |
| FAM138A      | FAM138A     | ENSG00000237613 |
| OR4F5        | OR4F5       | ENSG00000186092 |
| RP11-34P13   | RP11-34P13  | ENSG00000238009 |
| RP11-34P13-1 | RP11-34P13  | ENSG00000239945 |
| RP11-34P13-2 | RP11-34P13  | ENSG00000239906 |
| RP11-34P13-3 | RP11-34P13  | ENSG00000241860 |
| RP11-34P13-4 | RP11-34P13  | ENSG00000241599 |

| 项                 | 值                                          |
| ----------------- | ------------------------------------------ |
| 类型                | `anndata._core.sparse_dataset._CSRDataset` |
| 形状（cells × genes） | `(29773, 36503)`                           |

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
| experiment | geo_id     | accession | sample_type | sample_type_confidence | mapped_ontology_ids                               | mapped_ontology_terms                             | real_value_property_ids | real_value_property_terms | sra_uid | biosample_uid | biosample_title                    | raw_biosample_metadata                            | organism          | biosample    | database | accession_type | sample    | study     | bioproject  | geo_metadata                         |
| ---------- | ---------- | --------- | ----------- | ---------------------: | ------------------------------------------------- | ------------------------------------------------- | ----------------------- | ------------------------- | ------: | ------------: | ---------------------------------- | ------------------------------------------------- | ----------------- | ------------ | -------- | -------------- | --------- | --------- | ----------- | ------------------------------------ |
| SRX185895  | GSM1000981 | SRS362050 | cell_line   |                    1.0 | EFO:0005952, EFO:0007352, CVCL:1879, EFO:00059... | non-Hodgkins lymphoma, lymphatic system diseas... | NaN                     | NaN                       |  243394 |       1163911 | OCI-LY1_48hrs_mRNAseq_3x_siNT_R1   | source_name: Human DLBCL cel line; treatment: ... | Homo sapiens;9606 | SAMN01163911 | sra      | experiment     | SRS362050 | SRP007525 | PRJNA138597 | treatment: siNT,cell line: OCI-LY1   |
| SRX185896  | GSM1000982 | SRS362051 | cell_line   |                    1.0 | EFO:0005952, EFO:0007352, CVCL:1879, EFO:00059... | non-Hodgkins lymphoma, lymphatic system diseas... | NaN                     | NaN                       |  243395 |       1163912 | OCI-LY1_48hrs_mRNAseq_3x_siNT_R2   | source_name: Human DLBCL cel line; treatment: ... | Homo sapiens;9606 | SAMN01163912 | sra      | experiment     | SRS362051 | SRP007525 | PRJNA138597 | treatment: siNT,cell line: OCI-LY1   |
| SRX185897  | GSM1000983 | SRS362052 | cell_line   |                    1.0 | EFO:0005952, EFO:0007352, CVCL:1879, EFO:00059... | non-Hodgkins lymphoma, lymphatic system diseas... | NaN                     | NaN                       |  243396 |       1163913 | OCI-LY1_48hrs_mRNAseq_3x_siNT_R3   | source_name: Human DLBCL cel line; treatment: ... | Homo sapiens;9606 | SAMN01163913 | sra      | experiment     | SRS362052 | SRP007525 | PRJNA138597 | treatment: siNT,cell line: OCI-LY1   |
| SRX185898  | GSM1000984 | SRS362053 | cell_line   |                    1.0 | EFO:0005952, EFO:0007352, CVCL:1879, EFO:00059... | non-Hodgkins lymphoma, lymphatic system diseas... | NaN                     | NaN                       |  243397 |       1163914 | OCI-LY1_48hrs_mRNAseq_3x_siBCL6_R1 | source_name: Human DLBCL cel line; treatment: ... | Homo sapiens;9606 | SAMN01163914 | sra      | experiment     | SRS362053 | SRP007525 | PRJNA138597 | treatment: siBCL6,cell line: OCI-LY1 |
| SRX185899  | GSM1000985 | SRS362054 | cell_line   |                    1.0 | EFO:0005952, EFO:0007352, CVCL:1879, EFO:00059... | non-Hodgkins lymphoma, lymphatic system diseas... | NaN                     | NaN                       |  243398 |       1163915 | OCI-LY1_48hrs_mRNAseq_3x_siBCL6_R2 | source_name: Human DLBCL cel line; treatment: ... | Homo sapiens;9606 | SAMN01163915 | sra      | experiment     | SRS362054 | SRP007525 | PRJNA138597 | treatment: siBCL6,cell line: OCI-LY1 |

| var_index (ensembl_gene_id) | biotype        | gene_name |
| --------------------------- | -------------- | --------- |
| ENSG00000000003             | protein_coding | TSPAN6    |
| ENSG00000000005             | protein_coding | TNMD      |
| ENSG00000000419             | protein_coding | DPM1      |
| ENSG00000000457             | protein_coding | SCYL3     |
| ENSG00000000460             | protein_coding | C1orf112  |
| ENSG00000000938             | protein_coding | FGR       |
| ENSG00000000971             | protein_coding | CFH       |
| ENSG00000001036             | protein_coding | FUCA2     |

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
