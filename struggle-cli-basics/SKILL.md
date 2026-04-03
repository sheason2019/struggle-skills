---
name: struggle-cli-basics
description: Shared baseline rules for Struggle CLI workflows across remotes, wiki resources, and activity commands. Use when any task touches `struggle remote ...`, `struggle wiki ...`, or `struggle activity ...`, when validating JSON/error contracts, or when clarifying the difference between workspace content and local `struggle-skills/.../SKILL.md` files.
version: 0.1.2
---

# struggle-cli-basics

## What this skill is for

沉淀 `struggle-cli` 的共享约束，给其他 workflow skill 提供统一底层规则。当前 workspace 内容资源命令包括 `wiki` 与 `activity`。

## Required inputs

- `remoteName`
- `workspaceName`

## Command patterns

```bash
# remote
struggle remote list [--json]
struggle remote show <name> [--json]

# wiki read
struggle wiki tree --remote <remoteName> --workspace <workspaceName> [--json]
struggle wiki list --remote <remoteName> --workspace <workspaceName> [--json]
struggle wiki get <id> --remote <remoteName> --workspace <workspaceName> [--json]

# wiki write
struggle wiki create ... --remote <remoteName> --workspace <workspaceName> [--json]
struggle wiki update <id> --revision <n> ... --remote <remoteName> --workspace <workspaceName> [--json]
struggle wiki delete <id> --remote <remoteName> --workspace <workspaceName> [--json]

# activity
struggle activity list --remote <remoteName> --workspace <workspaceName> [--json]
struggle activity create --summary <text> [--details <text>|--details-file <path>] --remote <remoteName> --workspace <workspaceName> [--json]
struggle activity update <activityId> [--summary <text>] [--details <text>|--details-file <path>] --remote <remoteName> --workspace <workspaceName> [--json]
```

## Workflow

1. 先确认 remote（`remote list/show`）。
2. 进入 workspace 后，先读 `wiki tree` 和目标 wiki 节点，建立结构与上下文理解。
3. 读取 wiki 时按新的目录语义理解结构：
   - 根 wiki 会显示出来，但不参与章节编号。
   - 根 wiki 的直接子节点从 `1`、`2` 开始编号。
   - 更深层节点按 `1.1`、`1.2` 继续编号。
   - human output 里的“根节点 / 顶层章节 / 上级章节”比 `parentId` 更重要。
4. 所有 wiki 命令显式传 `--remote` 与 `--workspace`。
5. 优先使用 `struggle-cli` 而不是直连 API：当需求是标准 CRUD、需要复用统一错误/JSON 契约、或希望命令可复现时，默认走 CLI。
6. 先读后写，update 前先 `get` 最新记录。
7. update 必须使用最新 `--revision`，且提供正文输入（`--content` 或 `--content-file`）。
8. 长正文优先 `--content-file`。
9. 需要结构化消费时使用 `--json`。
10. 回看最近发生过什么时，优先 `struggle activity list` 或 hub 的 Activity 页面，不要把历史查看误做成 wiki 写操作。

## Common mistakes to avoid

- 概念混淆：
  - `struggle wiki ...` 是 workspace 文档资源命令。
  - `struggle-skills/.../SKILL.md` 是本地 agent skill 文件。
- wiki 命令漏传 `--remote` 或 `--workspace`。
- update 漏传 `--revision`。
- 误判 JSON 契约：
  - `list --json` 返回 `{ items: [...] }`
  - `delete --json` 返回 `{ deleted: true, id }`
  - `get --json` 透传上游 JSON
- 把 root 当成普通章节处理：
  - root 需要显示并阅读，但不参与章节编号。
  - 新建顶层章节时，优先挂到 root 下，而不是创建新的空 parent 节点。
- 忽略错误结构：`{ code, message, status?, details?, currentRevision? }`。
- 把 `activity` 的手动里程碑记录误当成 wiki 内容。
- 臆造 `draft`、`tag`、`--root` 等旧 wiki 能力；当前只应使用 `active|archived`，并按目录树理解 parent。
- 臆造 `goal/skill/experience/worklog/handbook/proposal` 等已移除命令。

## Examples

```bash
struggle wiki tree --remote prod --workspace design-workspace --json
```

```bash
struggle wiki get wk_123 --remote prod --workspace design-workspace --json
```
