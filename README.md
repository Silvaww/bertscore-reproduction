# bertscore-reproduction

**论文名称：** BERTScore: Evaluating Text Generation with BERT  
**论文来源：** ICLR 2020  
**项目类型：** 自然语言处理（NLP）课程论文复现  
**当前阶段：** 中期实验与阶段性成果整理

## 一、项目简介

本项目旨在复现论文《BERTScore: Evaluating Text Generation with BERT》的部分核心实验，研究基于预训练语言模型的语义评价指标 BERTScore 在机器翻译质量评价任务中的有效性。

传统机器翻译评价指标 BLEU 主要依赖候选译文与参考译文之间的 n-gram 重合程度，因此可能难以充分衡量用词不同但语义相近的译文。

BERTScore 利用预训练语言模型生成上下文相关的 Token 表示，通过余弦相似度和贪心匹配，计算候选译文与参考译文之间的语义相似程度，并得到 Precision、Recall 和 F1 分数。

本项目以 WMT18 机器翻译评价数据为基础，重点研究以下问题：

1. BERTScore 与人工评价结果的相关性是否优于传统 BLEU 指标？
2. BERTScore 在系统级和句级评价中分别具有怎样的表现？
3. IDF（逆文档频率）加权机制是否能够改善 BERTScore 的评价效果？

考虑课程项目的计算资源和时间限制，本研究采用缩规模复现方案，重点验证原论文核心结论在所选数据子集上的适用性。

## 二、原论文与复现目标

### 2.1 原论文

Zhang, T., Kishore, V., Wu, F., Weinberger, K. Q., & Artzi, Y. (2020).

*BERTScore: Evaluating Text Generation with BERT.*

International Conference on Learning Representations (ICLR 2020).

### 2.2 核心复现内容

本项目主要参考原论文的两个实验：

**实验一：系统级相关性分析（Table 1）**

- 以机器翻译系统为评价单位。
- 计算各系统的自动评价分数。
- 使用 Pearson 相关系数衡量自动评价结果与系统级人工评分之间的线性相关程度。
- 对比 BLEU 与 BERTScore F1 的表现。

**实验二：句级相关性分析（Table 4）**

- 以具体译文之间的人工偏好比较为基础。
- 使用 Kendall τ-like 相关系数评价自动指标与人工判断的一致性。
- 比较 BLEU 与 BERTScore F1 对译文质量相对排序的判断能力。

此外，本项目设计了 IDF 加权消融实验，以分析词频加权机制对 BERTScore 表现的影响。

## 三、数据集与实验设置

### 3.1 数据来源

本项目采用 WMT18 Metrics Task 中的 newstest2018 中文到英文（zh-en）机器翻译评价数据。

原始数据包含：

- 参考译文：3,981 条
- 翻译系统：14 个
- 人工偏好比较记录：33,357 条

### 3.2 数据筛选与抽样

为降低计算资源需求，本研究采用固定随机种子的缩规模抽样方案。

具体步骤如下：

1. 筛选具有人工比较记录且全部翻译系统输出完整的 SID。
2. 获得符合筛选条件的 3,364 个 SID。
3. 使用 NumPy 随机数生成器，固定随机种子为 42。
4. 从有效 SID 中无放回抽取 500 个。
5. 保留每个 SID 对应的全部 14 个系统译文及相关人工比较记录。

最终数据规模如下：

| 项目 | 数量 |
|---|---:|
| 选取 SID | 500 |
| 翻译系统 | 14 |
| 候选译文 | 7,000 |
| 人工偏好比较记录 | 4,976 |
| 随机种子 | 42 |

### 3.3 数据文件说明

| 文件名 | 内容 |
|---|---|
| `selected_sids.csv` | 抽取的 500 个 SID |
| `translations_500.csv` | 14 个系统对应的 7,000 条候选译文 |
| `human_pairs_500.csv` | 抽样范围内的人工偏好比较记录 |
| `manifest.json` | 数据抽样及实验配置元信息 |
| `data_source.md` | 数据来源与相关说明 |

**说明：** 本研究属于缩规模复现。由于实验采用部分原文样本及筛选后的数据子集，所得结果不能直接视为原论文全量实验数值的严格复现。

## 四、实验设计

### 4.1 BLEU 与 BERTScore 对照实验

本项目选择两种评价指标：

- **BLEU：** 基于词汇匹配的传统机器翻译评价指标。
- **BERTScore F1：** 基于上下文语义表示的文本生成评价指标。

两种指标使用相同的候选译文及参考译文，以确保实验具有可比性。

### 4.2 IDF 加权消融实验

为分析 IDF 加权机制的影响，本研究设置两组实验：

| 实验组 | 评价指标 | IDF 加权 |
|---|---|---|
| A | BERTScore F1 | 关闭 |
| B | BERTScore F1 | 开启 |

两组实验保持预训练模型、编码层、测试样本及其他评分参数一致，仅改变 IDF 加权设置。

计划使用完整 WMT18 zh-en 参考译文集合估计统一的 IDF 权重，减少小样本导致的估计偏差。

### 4.3 评价指标

**系统级：Pearson 相关系数**

计算各翻译系统的自动评价分数与人工系统级评分之间的线性相关程度。

**句级：Kendall τ-like 相关系数**

基于人工偏好比较记录，衡量自动评价指标对候选译文优劣关系的判断与人工判断的一致性。

**统计分析：Bootstrap**

计划以 SID 为单位进行成组重采样，重复 1,000 次，计算相关性差值的 95% 置信区间，以评估指标间的差异。

## 五、环境配置与初步实验

### 5.1 试跑环境

目前已完成小规模环境测试，具体配置如下：

| 配置项 | 实验设置 |
|---|---|
| 操作系统 | Windows 10 |
| Python | 3.12.14 |
| 处理器 | AMD Ryzen 7 5800H |
| SacreBLEU | 2.5.1 |
| BERTScore | 0.3.13 |
| 预训练模型 | DistilBERT |
| 编码层 | 第 5 层 |
| IDF 加权 | 关闭 |
| 测试样本 | 10 对中英翻译样本 |

当前已使用 DistilBERT 完成环境验证。正式实验计划优先采用 RoBERTa-large，具体配置将根据计算资源确定。

### 5.2 初步运行结果

在 10 对 WMT18 中译英样本上，已成功运行 BLEU 和 BERTScore。

| 指标 | 测试结果 |
|---|---:|
| 语料级 BLEU | 27.23 |
| BERTScore 平均 F1 | 0.9173 |
| SacreBLEU 每批耗时中位数 | 1.09 ms |
| BERTScore 每批耗时中位数 | 481.22 ms |
| SacreBLEU 进程峰值内存 | 34.6 MiB |
| BERTScore 进程峰值内存 | 800.0 MiB |

耗时数据为预热后各运行 5 次得到的中位数，每批包含 10 条样本。

实验过程中曾遇到网络访问受限和依赖目录权限问题，随后通过模型权重下载与校验、调整依赖读取目录等方式解决，最终完成两项指标的运行测试。

**注意：以上结果仅用于验证实验环境和指标计算流程，不代表正式的系统级或句级相关性实验结果。**

## 六、阶段性进展

- [x] 完成 BERTScore 原论文阅读与核心实验分析
- [x] 确定系统级和句级复现目标
- [x] 完成 WMT18 zh-en 数据整理与有效样本筛选
- [x] 完成 500 SID 缩规模抽样
- [x] 整理实验数据与配置文件
- [x] 完成 SacreBLEU 和 BERTScore 环境试跑
- [x] 完成中期实验报告
- [ ] 完成正式规模的 BLEU 与 BERTScore 计算
- [ ] 计算系统级 Pearson 相关系数
- [ ] 计算句级 Kendall τ-like 相关系数
- [ ] 完成 IDF 加权消融实验
- [ ] 完成 Bootstrap 统计分析
- [ ] 对比原论文结果并撰写最终复现报告

## 七、项目文件说明

```text
bertscore-reproduction/
├── data/
│   ├── selected_sids.csv
│   ├── translations_500.csv
│   ├── human_pairs_500.csv
│   └── manifest.json
├── docs/
│   ├── 文献研究与实验设计.docx
│   ├── 成员二_环境配置与资源试跑.docx
│   └── G27中期报告.docx
├── src/                  # 后续补充实验代码
├── results/              # 后续补充实验结果
├── data_source.md
├── README.md
└── .gitignore
```

其中 `src/` 和 `results/` 为计划补充的目录，当前以实际已上传文件为准。

## 八、复现说明

当前仓库主要整理项目中期成果，包括实验设计、数据准备及初步环境测试结果。

后续将逐步补充：

- 数据预处理与抽样代码
- BLEU 和 BERTScore 计算代码
- 相关性分析脚本
- IDF 消融实验代码
- 环境依赖与运行说明
- 完整实验结果及复现报告

在上述内容补齐之前，本仓库暂不构成完整的端到端可运行复现流程。

## 九、参考文献

[1] Zhang, T., Kishore, V., Wu, F., Weinberger, K. Q., & Artzi, Y. (2020). *BERTScore: Evaluating Text Generation with BERT*. ICLR 2020.

[2] WMT18 Metrics Shared Task — newstest2018 machine translation evaluation dataset.

[3] Papineni, K., Roukos, S., Ward, T., & Zhu, W. J. (2002). *BLEU: a Method for Automatic Evaluation of Machine Translation*. ACL 2002.
