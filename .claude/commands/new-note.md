---
description: 按模板在指定领域新建一篇规范笔记
argument-hint: [标题] [领域]
allowed-tools: Read, Write, Glob
---
在 03_Knowledge/$2/ 下新建笔记「$1.md」：
1. 套用 05_Templates/ 中对应类型的模板
2. 添加 YAML frontmatter（created、tags、status）
3. 提示我补充正文内容，暂不建立链接（正文写完后可另跑 /process-inbox 逻辑补链接）
