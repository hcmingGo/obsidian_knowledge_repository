---
created: 2026-08-01
tags: [知识管理/自动化, type/笔记]
status: 已完成
source: 知乎《超详细版：Obsidian + Claude Code 搭建个人知识库实践指南》https://zhuanlan.zhihu.com/p/2029950530726924559（原文的 `claude run process_inbox` 等命令是伪代码，实际 CLI 不支持，此处为核实后的真实用法）
---

# ClaudeCode自定义命令机制

Claude Code CLI 没有内置的 `run`/`ask`/`generate` 子命令（网传的 `claude run process_inbox`、`claude generate "..." --format article` 均为作者自造的伪代码）。要把重复工作变成可复用命令，实际有两种方式：

## 方式一：自定义 Slash Command（交互式会话用，日常推荐）

在知识库根目录下创建 `.claude/commands/` 文件夹，每个 Markdown 文件对应一个可复用的提示词模板，文件名即命令名。文件用 YAML frontmatter 声明 `description`、`allowed-tools`，正文写清楚执行步骤。之后在交互会话里输入 `/命令名` 即可触发。

较新版本的 Claude Code 把自定义命令并入了 Skills 体系，写成 `.claude/skills/<name>/SKILL.md` 效果一样，还能支持 AI 自主判断何时调用；两种写法目前都兼容。

## 方式二：无人值守模式（Headless / `-p` 参数，适合定时任务）

用 `-p`（`--print`）参数做非交互式调用，配合系统定时任务（cron / 计划任务）后台自动跑，例如每天定时清理 Inbox：

```bash
claude -p "执行 Inbox 整理" --allowedTools Read,Write,Edit,Glob
```

**注意事项**：headless 模式下没有交互确认，务必先在交互模式里把对应命令调试稳定，再放到定时任务里；同时用 `--allowedTools` 限定工具范围，减少误操作风险，不要图省事用 `--dangerously-skip-permissions`。

## 相关笔记

- [[项目记忆文件设计]] —— 自定义命令的执行规则来自 CLAUDE.md 定义的职责
- [[个人知识库日常工作流循环]] —— 这些命令在日常循环中被实际触发的场景
- [[个人知识库长期维护建议]] —— 权限限定在知识库目录内的必要性
