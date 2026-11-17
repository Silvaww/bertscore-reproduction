# 数据来源与固定样本记录

数据集：WMT18 Metrics Task newstest2018 zh-en
官方来源页：https://www.statmt.org/wmt18/metrics-task.html
人工评分文件：RR-seglevel.csv
固定样本ID文件：selected_sids.csv
随机种子：42

原始规模：

- 参考译文：3981 条
- 翻译系统：14 个
- zh-en 原始人工比较：33357 条
- 符合筛选条件的 SID：3364 个

抽样后规模：

- 抽取 SID：500 个
- 候选译文：7000 条
- 对应人工比较：4976 条

试跑10条SID：
20, 24, 39, 56, 69, 71, 72, 80, 84, 97

10条试跑记录（Windows 10 环境）：

- 环境：Windows 10，Python 3.12.14，Ryzen 7 5800H CPU
- 工具：SacreBLEU 2.5.1，BERTScore 0.3.13
- 样本：10 对 WMT18 中译英
- 测速：预热后各测 5 次，每批（10条）耗时中位数
- 耗时：SacreBLEU 1.09 ms/批；BERTScore 481.22 ms/批
- 峰值内存：34.6 MiB / 800.0 MiB
- 试跑分数：语料级 BLEU 27.23；BERTScore 平均 F1 0.9173（DistilBERT 第5层，关闭IDF）
- 问题与解决：网络访问受限、依赖目录读取权限；下载并SHA-256校验权重，复制依赖到可读目录后跑通
- 结论：两项指标均跑通；正式实验用500 SID/7000条译文
  本项目已生成的成果文件：

selected_sids.csv、translations_500.csv、human_pairs_500.csv、manifest.json

记录日期：
2026-11-14
