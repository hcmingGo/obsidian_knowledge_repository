---
description: 基于知识库内容生成文章/报告初稿
argument-hint: [主题]
allowed-tools: Read, Write, Glob, Grep
---
按照 CLAUDE.md「职责五」的规则，围绕主题「$ARGUMENTS」：
1. 检索 03_Knowledge/ 中相关笔记及对应 MOC
2. 提取关键观点、案例、数据，按逻辑顺序组织成初稿
3. 初稿末尾附引用笔记清单（[[笔记标题]] 形式）
4. 保存到 04_Projects/ 对应项目文件夹，不覆盖原始笔记
5. 更新 INDEX.md 的「最近更新日志」
