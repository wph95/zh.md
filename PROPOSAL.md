# Proposal: 中文技术文档的写作风格检查与自动化管理工具

- Author(s):    [wph95](https://github.com/WPH95), [yikeke](https://github.com/yikeke)
- Last updated:  2021-01-10
- Discussion at: https://github.com/WPH95/zh.md/issues

## Abstract

本提案提供一套侧重中文技术文档的写作风格检查与自动化管理解决方案，包括：

- **完整的中文文档写作风格指南：** 为文档作者和审校者提供统一的文档写作参考规范
- **中文文档分析工具和中文文档风格检测工具：** 可检测存量或增量文档中的风格问题
- **基于 GitHub 的文档管理 bot：** 可以自动建立 issue 或 PR 修复存量或增量文档中的风格问题
- **基于机器学习辅助文档写作：**可以辅助生成具有统一风格的文档

可明显降低文档的审查工作量、提高文档整体质量，调动工程师积极参与文档内容建设。

本提案适用于任何需要专业技术文档的项目，具有**普及性**。

## Background

随着国内 ToB/开源项目飞速发展，对技术文档的专业性、可读性的需求日益提升。目前市面上没有一份完整清晰的中文风格指南，更没有相应配套的自动化辅助方案。

开创性地建立一种完整科学的中文技术文档写作流程是一件非常有价值的事情！

目前，开源项目的技术文档管理存在写作低效、管理成本高（需要专人进行繁琐的格式或拼写检查）等问题，并缺乏系统化、自动化的流程。

PingCAP 作为国内领先的开源软件公司，旗下文档管理已初步做到 Docs as Code（文档代码化）。但在自动化测试（尤其是中文文档的 Lint 方面）还有很大的提升空间。

同时，文档管理主要依赖头部维护者持续的工作，需要更自动化的方式来提高工程师的协作效率，帮助他们高效、持续地输出文档。

## Proposal

总体计划可分为以下几个部分：

1. 一套更友好的文档贡献流程指南与风格指南

   对现有的 PingCAP 文档的贡献流程和风格指南进行优化，提升其可读性。

   比如：现有的风格指南是 PDF 格式，不利于写文档时候查阅。可以配置在网页端，方便外部贡献者查阅。

2. 一套中文文档分析与检测工具

   基于 AST（抽象语法树）和分词，系统全面地对文档进行扫描与诊断，评估文档质量并对其进行优化和修复。

   基于文档分析结果，使用统计学/NLP 等工具，辅助作者写出符合风格规范的文档。

   使用第三方 API，对文本进行中英文错误检测。

3. 一个基于 Github 的文档自动化管理 bot

   将一些重复性的文档管理工作进行自动化，使文档更新的路径更短、效率更高。

   比如：对 pingcap/docs-cn repo 进行定期检查/push 触发，如果发现文档更新，自动创建相应的 issue 和 PR，并 assign 给合适的人，提醒其来对应 repo 工作。

## Rationale

1. 中文文档写作风格指南的缺失。

   对于英文技术文档，国外已经有相当成熟、完整的英文风格指南可供参考。而在国内，类似的成体系的中文文档风格指南少之又少。

   - 英文技术文档风格指南
     - [微软风格指南](https://docs.microsoft.com/en-us/style-guide/welcome/)
     - [谷歌开发者风格指南](https://developers.google.com/style/)
     - [《芝加哥写作风格手册》](https://www.chicagomanualofstyle.org/) (非技术)
   - 中文技术文档风格指南
     - [PingCAP 中文文档风格指南](https://github.com/pingcap/docs-cn/blob/master/resources/pingcap-style-guide-zh.pdf)
     - [中文技术文档的写作规范](https://github.com/ruanyf/document-style-guide) 
     - [LeanCloud 文案风格指南](https://open.leancloud.cn/copywriting-style-guide/)
     - [余光中：怎样改进英式中文？](https://open.leancloud.cn/improve-chinese/)

2. 目前针对纯文本的 linter，主要有以下两种：

   - 文档格式的 linter，例如 remark.js、markdownlint 等工具

   - 英文语言的 linter，着重在检测风格和拼写上，例如 vale、textlint 等工具

   当前市面上针对中文文档的 linter，还处于早期阶段，尚无成熟的方案。

   相较于英文文档，中文文档的 linter 语法要复杂很多。

   本提案设计了一套用于处理中文文档的分析工具，首先是语境 -> 实例 -> 字的 AST。然后针对标点符号、中文词汇（重组回句子后进行分词）、英语词汇做跳链索引。相较于利用正则表达式制定规则的传统方案，有数量级的性能提升，同时保证了高扩展性。

3. 机器学习与统计学辅助文档写作的探索

   主要研究方向通过机器学习辅助技术文档写作，降低错误率，提高正规性和统一性。

   - 对科技文档的 NLP 与统计研究，尚未找到相应的文献。

     因此放大范围，探索了近几年发展迅速的基于 NLP 的风格迁移研究。本提案将基于 AAAI 2020 的一篇论文 《**A Dataset for Low-Resource Stylized Sequence-to-Sequence Generation**》对文本进行正规礼貌风格的迁移。

   - 日志聚类是一种成熟的应用于日志领域的功能。本提案将尝试运用一些聚类算法，辅助生成一些常用模板，帮助作者写出风格统一、简单清晰的文档。

## Compatibility and Migration Plan

第一阶段：针对 pingcap/docs-cn repo 的存量和增量文档进行自动化检测与优化。

第二阶段：扫描 PingCAP 旗下所有的 repo，自动生成 issue 和 PR 来修复检查出的问题。

第三阶段：将工具封装，让任意开源项目均可开箱即用，同时在风格检查方面，具备极强的可定制性。

## Implementation

1. 对已有的中文风格文档进行筛选整理与分配。

2. Doc Bot

   基于 probot/Github Action 进行扩展，实现以下功能。

   - 定期/通过 tag 触发，上游文档库（例如英文文档库是中文文档库的上游）的文件发生变动后，自动生成 issue，分配给相应的人。
   - 在自动生成的 issue 中，支持 `/autocreate | /ac`，通过 Deepl API，自动生成翻译后的初步文件，创建 PR。
   - 可对文档已有未检测的存量进行扫描，发现问题后自动生成 issue 和 fix PR。

3. zhLint

   使用已有的成熟的 Markdown lint 对内容进行检测。

   通过 AST/分词等方式对 Markdown 文件进行解析，对 9 个高频的错误进行检测与自动修复

   1. 禁用词

      例如：报道各种事实特别是产品、商品时不使用“最佳”、“最好”、“最著名”、“最新技术”、“最高水平”、“最先进水平”等具有强烈评价色彩的词语。

   2. 空格

   3. 中文标点符号

      参见 [zh.md 常见中文标点符号](http://zh.md/%E6%A0%87%E7%82%B9%E7%AC%A6%E5%8F%B7/%E5%B8%B8%E7%94%A8%E4%B8%AD%E6%96%87%E6%A0%87%E7%82%B9%E7%AC%A6%E5%8F%B7.html)

   4. 禁止使用不规范的缩略语

      例如，用“16c32g”表示“16 核、32 GB”；用“10w”表示 10 万；用 3k 表示 3000

   5. 单位符号

   6. 避免“一逗到底”冗长段落现象：检测一个段落里连续出现8 个逗号后报警

   7. 中文句子句末均以中文标点符号结尾

   8. 中英文括号的使用

   9. 斜杠

      一般技术文档中不鼓励使用 /，因为它可以表示「或」，也可以表示「和」。如果检测到 / 两边是中文，则报警

4. 机器学习辅助技术文档写作

   1. 基于 pingcap/docs 与 pingcap/docs-cn repo，遵循 [hugging face Dateset 协议](https://huggingface.co/datasets)建立语料数据集。
   2. 使用 hugging face 上已有的 model 进行测试。
   3. 使用常用日志模式匹配方式对语料进行处理和分析。例：使用 [sequence](https://github.com/zentures/sequence)
   4. 使用 《**A Dataset for Low-Resource Stylized Sequence-to-Sequence Generation**》 论文进行尝试。

## Testing Plan

检测工具使用风格指南中的案例作为测试内容。

## Open issues (if applicable)

N/A
