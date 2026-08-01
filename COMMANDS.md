# 知识库可运行命令清单

> 本文档列出你可以在这个知识库目录下对 Claude Code 使用的全部命令，分两类：
> 
> 1. **自定义命令**——针对本知识库工作流专门定义的，需要你先建好命令文件才能用；
> 2. **内置命令**——Claude Code 自带的，不用配置，随时可用。
> 
> 使用前提：在终端 `cd` 到知识库根目录后运行 `claude` 进入交互会话，再输入命令。

---

## 一、自定义命令（需要先创建）

自定义命令本质是 `.claude/commands/` 目录下的一个 Markdown 文件，文件名即命令名。建好之后，在交互会话里输入 `/命令名` 即可触发。以下是配合前面 `CLAUDE.md` 中五大职责设计的完整命令集，可以直接复制创建。

```bash
mkdir -p .claude/commands
```

### 1. `/process-inbox` —— 整理收件箱

创建 `.claude/commands/process-inbox.md`：

```markdown
---
description: 处理 00_Inbox 中的所有笔记，原子化并归档
allowed-tools: Read, Write, Edit, Glob
---
按照 CLAUDE.md「职责一」的规则，处理 00_Inbox/ 下所有笔记：
1. 提炼核心观点，重写为原子化笔记（一篇只讲一个概念）
2. 判断领域，移动到 03_Knowledge/ 对应子目录
3. 添加 2-3 个精准标签
4. 建立至少 3 个相关双向链接（找不到则如实说明）
5. 处理完的原始文件移动到 00_Inbox/已处理/
6. 更新 INDEX.md 的「统计概览」和「最近更新日志」
最后列出本次处理清单：原文件名 → 新文件名 → 所属领域 → 标签
```

**用法**：`/process-inbox`　建议每天执行一次。

---

### 2. `/update-moc` —— 更新主题地图

创建 `.claude/commands/update-moc.md`：

```markdown
---
description: 为达到阈值的主题创建或更新 MOC 文件
allowed-tools: Read, Write, Edit, Glob
---
按照 CLAUDE.md「职责二」的规则：
1. 检查 03_Knowledge/ 下各子文件夹的笔记数量
2. 对笔记数 ≥ 5 篇且尚无 MOC，或已有 MOC 但落后于最新笔记的主题，创建/更新对应的 `MOC - 主题名.md`
3. MOC 内容包含：核心理论列表（附简介与链接）、应用场景、相关主题链接
4. 只做增量补充，不删除用户已手动添加的内容
5. 更新 INDEX.md 的「目录结构现状」「领域索引」「最近更新日志」
```

**用法**：`/update-moc`　可在 `/process-inbox` 之后顺手执行，或每周固定跑一次。

---

### 3. `/weekly-review` —— 每周知识网络体检

创建 `.claude/commands/weekly-review.md`：

```markdown
---
description: 生成每周知识网络分析报告
allowed-tools: Read, Write, Glob, Grep
---
按照 CLAUDE.md「职责四」的规则，生成本周知识网络体检报告，包含：
1. 孤立笔记清单（无入链也无出链）
2. 核心枢纽节点（被链接次数最多的笔记 Top 10）
3. 标签使用统计（过度使用/几乎未用的标签，给出合并或清理建议）
4. 知识缺口分析（基于现有网络推测缺失的关联内容）
报告以 Markdown 表格保存到 01_Daily/YYYY-MM-DD-周报.md
同时更新 INDEX.md 的「统计概览」「标签体系一览」「待处理事项」「维护记录」
```

**用法**：`/weekly-review`　建议每周固定一天执行。

---

### 4. `/draft-article` —— 知识输出成稿

创建 `.claude/commands/draft-article.md`：

```markdown
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
```

**用法**：`/draft-article 认知大模型的高效学习方法`

---

### 5. `/update-index` —— 单独同步总览文档

创建 `.claude/commands/update-index.md`（当你只想刷新 INDEX.md，不想跑完整流程时用）：

```markdown
---
description: 重新扫描知识库并同步 INDEX.md 全部章节
allowed-tools: Read, Write, Glob, Grep
---
重新扫描整个知识库，更新 INDEX.md 中的：
基本信息（最后更新时间）、统计概览、目录结构现状、领域索引、标签体系一览。
不改动「最近更新日志」「待处理事项」「维护记录」中的历史记录，只做追加。
```

**用法**：`/update-index`

---

### 6. `/new-note` —— 快速新建规范笔记

创建 `.claude/commands/new-note.md`：

```markdown
---
description: 按模板在指定领域新建一篇规范笔记
argument-hint: [标题] [领域]
allowed-tools: Read, Write, Glob
---
在 03_Knowledge/$1/ 下新建笔记「$0.md」：
1. 套用 05_Templates/ 中对应类型的模板
2. 添加 YAML frontmatter（created、tags、status）
3. 提示我补充正文内容，暂不建立链接（正文写完后可另跑 /process-inbox 逻辑补链接）
```

**用法**：`/new-note 强化学习基础 大模型`

---

### 7. `/quarterly-audit` —— 季度深度审查

创建 `.claude/commands/quarterly-audit.md`：

```markdown
---
description: 季度级知识体系审查，给出结构调整建议
allowed-tools: Read, Glob, Grep
---
从更高维度审视整个知识库：
1. 哪些领域发展快、哪些停滞
2. 目录结构是否需要调整（新增/合并/废弃领域文件夹）
3. 是否有新的 MOC 需要建立
4. CLAUDE.md 是否需要更新
只输出书面建议列表，不自动执行任何结构性改动，等待用户确认。
建议追加到 INDEX.md 的「维护记录」。
```

**用法**：`/quarterly-audit`　每季度或每半年跑一次。

---

## 二、常用内置命令（无需配置，随时可用）

这些是 Claude Code 自带的命令，在管理知识库时比较常用的部分：

|命令|作用|使用场景|
|---|---|---|
|`/init`|扫描当前目录，自动生成/更新 `CLAUDE.md` 骨架|第一次在新知识库里跑，或想让 AI 重新梳理一份基础版 CLAUDE.md 时用|
|`/memory`|直接打开并编辑 `CLAUDE.md`（记忆文件）|想手动调整规则、补充新领域说明时用|
|`/clear`|清空当前会话的对话历史|切换到完全不同的任务（比如刚整理完 Inbox，接下来要写文章）时用，避免旧上下文干扰|
|`/compact [说明]`|压缩较早的对话内容，保留摘要|同一个任务里对话变长、token 快用完时用|
|`/cost`|查看当前会话的用量/花费统计|关心成本时查看|
|`/model`|切换使用的模型|简单任务（如整理 Inbox）用更快的模型，写长文章用更强的模型|
|`/permissions`|查看/调整当前会话的工具权限|确认 AI 是否被限制在知识库目录内|
|`/add-dir`|增加额外可访问的工作目录|需要同时处理知识库之外的文件（比如导入旧笔记）时用|
|`/doctor`|检查 Claude Code 安装和配置健康状况|命令不生效、行为异常时排查|
|`/help`|列出当前会话所有可用命令（含你自定义的）|忘记命令名字时查|

---

## 三、无人值守 / 定时自动化命令

如果想让某些命令按固定时间自动跑（比如每天自动整理 Inbox），用 `-p`（headless）参数配合系统定时任务，不需要打开交互界面。

```bash
# 单次执行 —— 相当于在交互会话里输入 /process-inbox
cd /path/to/知识库
claude -p "/process-inbox" --allowedTools Read,Write,Edit,Glob
```

写入 crontab 示例（每天 23:00 自动整理 Inbox，每周日 20:00 自动跑周报）：

```
0 23 * * *  cd /path/to/知识库 && claude -p "/process-inbox" --allowedTools Read,Write,Edit,Glob >> ~/kb-log.txt 2>&1
0 20 * * 0  cd /path/to/知识库 && claude -p "/weekly-review" --allowedTools Read,Write,Glob,Grep >> ~/kb-log.txt 2>&1
```

注意事项：

- headless 模式没有交互确认，务必先在交互会话里把命令调试稳定，确认不会误删/误改文件，再放进定时任务。
- 用 `--allowedTools` 限定权限范围，不要用 `--dangerously-skip-permissions` 图省事。
- 定时任务的输出建议重定向到日志文件，方便事后排查。

---

## 四、命令速查表

|我想做什么|输入什么|
|---|---|
|整理今天丢进收件箱的内容|`/process-inbox`|
|某个主题笔记攒够了，想要一份索引|`/update-moc`|
|每周看看知识库健康状况|`/weekly-review`|
|基于笔记写一篇文章初稿|`/draft-article 主题名`|
|只是想把总览文档刷新一下|`/update-index`|
|新建一篇规范笔记|`/new-note 标题 领域`|
|每季度审视一次整体结构|`/quarterly-audit`|
|重新生成/校准 CLAUDE.md|`/init` 或 `/memory`|
|切换任务，清掉旧上下文|`/clear`|
|查看这次话费了多少 token|`/cost`|
|忘了有哪些命令|`/help`|

---

_建议把本文档保存为知识库根目录下的 `COMMANDS.md`，和 `CLAUDE.md`、`INDEX.md` 放在一起，形成三件套：CLAUDE.md 定规则，INDEX.md 看现状，COMMANDS.md 查怎么操作。_