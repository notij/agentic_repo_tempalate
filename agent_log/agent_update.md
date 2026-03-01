# agent_update

## 核心输入

- `git status`：当前改动面（新增/修改/删除文件）
- `git diff`：关键改动内容与意图
- `agent_log/agent_read.md`：当前项目抽象基线
- `agent_log/worklog/YYYY/MM.md`：按月维护的工作记录文件（不存在则创建）

## 固定规则（不得擅自修改）

- `agent_log/agent_read.md` 顶部“固定规则（给 Agent）”属于稳定规则区，默认不得修改。
- 本文件中的流程规则默认不得删除或弱化。
- 若确需改规则，必须由用户明确提出后再调整。

## 必做步骤（严格执行）

1. 读取并理解当前 `agent_log/agent_read.md` 结构与内容。
2. 执行 `git status`，列出所有改动文件。
3. 通过 `git diff` 识别“实际发生了什么变化”（模块职责变化、文件新增删除、流程变化）。
4. 比较“当前真实状态”与 `agent_log/agent_read.md` 的差异。
5. 更新 `agent_log/agent_read.md`，保证其反映最新状态。
6. 在当前 branch 的每次 commit 前，先记录“当前本地最新 commit（用于回溯）”，再基于 `git diff` 将本次将要提交的更新内容写入 `agent_log/worklog/YYYY/MM.md`，按日期追加记录。
7. 结束。无需输出阶段总结模板，无需写进度汇报。

## 更新 `agent_log/agent_read.md` 的规则

- 保持原有文档风格与层级结构（优先二级目录，再补一级模块）。
- 只写稳定且有价值的抽象，不写噪音细节。
- 对 `memory/*` 保持低优先级说明：视作历史存档，默认无需解析。
- 若有新增模块/目录，必须补充“模块职责 + 包含文件”。
- 若有文件删除或迁移，必须同步清单，避免过期信息。
- 若 `git diff` 显示功能/文件被移除，`agent_log/agent_read.md` 只删除原有对应记录；不要新增“已移除某功能”的新条目说明（该限制仅作用于 `agent_log/agent_read.md`，不作用于 worklog）。
- 若主流程改变（如入口、调用链、命令行为），必须更新“当前模块关系（简述）”。
- 若无实质变化，不修改 `agent_log/agent_read.md`（避免无意义改动）。

## 当 `agent_log/agent_read.md` 为空时（初始化提示词）

如果 `agent_log/agent_read.md` 为空或仅有标题，先按以下骨架创建，再填内容：

```text
# 项目模块说明（给后续 Agent）

读取任务说明（给 Agent）：你需要先阅读本文件，以快速理解整个 repo 的功能、模块边界与文件职责，用于工作交接与上下文续接。
执行方式：默认仅基于本文件与 `agent_log/worklog` 完成交接，不要求进入具体代码文件；仅当任务明确要求实现/排障时再读源码。
执行约束：每次执行 commit 前，必须先按 `agent_log/agent_update.md` 的规则完成更新任务。

## 模块总览
- `模块A`：一句话职责
- `模块B`：一句话职责

## 模块详情
### `模块A` 模块
- 模块作用：...
- 子文件夹说明：...
- 功能对应文件：
  - `功能1` -> `path/to/file`
  - `功能2` -> `path/to/file`

### `模块B` 模块
- 模块作用：...
- 子文件夹说明：...
- 功能对应文件：
  - `功能X` -> `path/to/file`

## 程序入口与主调用链
- 入口文件：`...`
- 主链路：
  1. ...
  2. ...

## 其他关键文件
- `README.md`：...
- `agent_log/agent_update.md`：...
- `agent_log/worklog/YYYY/MM.md`：...
```

初始化填写要求：

- 每个模块至少写 1 条“模块作用”与 2 条“功能对应文件”。
- 文件路径必须真实存在于仓库。
- `memory/*` 只写“历史存档，默认低优先级”，避免展开细节。
- 首次创建完成后，再按常规规则进行增量更新。

## 更新 `agent_log/worklog/YYYY/MM.md` 的规则（当前 branch 每次 commit 前执行）

- 目录结构固定为：`agent_log/worklog/YYYY/MM.md`（例如 `agent_log/worklog/2026/02.md`）。
- 若当月文件不存在，先创建并写入月标题。
- 每次记录按“日期”分段追加，不覆盖历史内容。
- 若当日日期标题已存在（如 `## YYYY-MM-DD`），禁止再新增同日期标题；只在该日期段落下继续追加本次记录。
- 必须记录“当前本地最新 commit（hash）”与当前 branch 名，便于后续 `git checkout` 回溯。
- 内容基于本次提交前的 `git diff`，只记录本次实际改动，不写空泛描述。
- 建议简洁格式：
  - 改动概
  - 变更文件列表
  - 关键行为变化（如有）
  - 风险或待办（可选）

推荐模板：

```text
## YYYY-MM-DD

- branch: <branch_name>
- latest_local_commit: <latest_local_commit_sha_before_this_commit>
- 改动概述：...
- 变更文件：
  - `path/a`
  - `path/b`
- 关键行为变化：...
- 备注：...
```

同日二次以上提交追加模板（不要重复日期标题）：

```text
- 本次 commit 前更新：
  - latest_local_commit: <latest_local_commit_sha_before_this_commit>
  - 改动概述：...
  - 变更文件：...
  - 关键行为变化：...
```

## 禁止事项

- 不要把阶段进度、执行过程、临时排查结论写入 `agent_log/agent_read.md`。
- 不要追加“本轮完成了什么”的流水账内容。
- 不要为凑字数重复已有模块描述。
- 不要在 `agent_log/agent_read.md` 中为删除改动新增“移除了什么功能”的说明性条目（worklog 可正常记录删除历史）。
- 不要在当前 branch 执行 commit 前漏记 worklog（除非本次无代码改动且用户明确允许）。
- 不要在未获用户明确授权时修改 `agent_log/agent_read.md` 顶部固定规则或本文件规则条款。
- 不要擅自更新 `agent_log/agent_update.md`文件

## 质量检查清单（提交前自检）

- `agent_log/agent_read.md` 中是否仍有与 `git status` 不一致的内容？
- 新增/删除的关键文件是否已同步到对应模块？
- 模块职责是否反映了当前代码行为，而不是旧行为？
- 是否避免把 `memory/*` 写成必须深入阅读的模块？
- 文档是否保持精简，可被下一位 Agent 快速读完,并且理解整个架构？
- 当前 branch 本次 commit 前，是否已记录 latest_local_commit 并把本次 diff 摘要追加到当月 worklog？
- 当天已有日期标题时，是否避免重复新增同日期标题？

