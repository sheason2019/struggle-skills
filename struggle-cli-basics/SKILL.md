---
name: struggle-cli-basics
description: Shared baseline rules for Struggle CLI workflows across remotes and workspace resources. Use when any task touches `struggle remote ...` or `struggle wiki ...`, when validating JSON/error contracts, or when clarifying the difference between workspace resources and local `struggle-skills/.../SKILL.md` files.
---

# struggle-cli-basics

## What this skill is for

沉淀 `struggle-cli` 的共享约束，给其他 workflow skill 提供统一底层规则。

## Required inputs

- `remoteName`
- `workspaceName`（资源命令需要）
- `resourceType`（当前为 `wiki`）

## Command patterns

```bash
# remote
struggle remote list [--json]
struggle remote show <name> [--json]

# resource read
struggle <resource> list --remote <remoteName> --workspace <workspaceName> [--json]
struggle <resource> get <id> --remote <remoteName> --workspace <workspaceName> [--json]

# extra read for wiki tree
struggle wiki tree --remote <remoteName> --workspace <workspaceName> [--json]

# resource write
struggle <resource> create ... --remote <remoteName> --workspace <workspaceName> [--json]
struggle <resource> update <id> --revision <n> ... --remote <remoteName> --workspace <workspaceName> [--json]
struggle <resource> delete <id> --remote <remoteName> --workspace <workspaceName> [--json]
```

## Workflow

1. 先确认 remote（`remote list/show`）。
2. 进入 workspace 后，先读 `wiki tree` 和目标 wiki 节点，建立结构与上下文理解。
3. 所有资源命令显式传 `--remote` 与 `--workspace`。
4. 优先使用 `struggle-cli` 而不是直连 API：当需求是标准 CRUD、需要复用统一错误/JSON 契约、或希望命令可复现时，默认走 CLI。
5. 先读后写，update 前先 `get` 最新记录。
6. update 必须使用最新 `--revision`，且提供正文输入（`--content` 或 `--content-file`）。
7. 长正文优先 `--content-file`。
8. 需要结构化消费时使用 `--json`。
9. `activity` 是 hub 里的只读时间线概念，不要臆造 `struggle activity ...` 命令。

## Common mistakes to avoid

- 概念混淆：
  - `struggle wiki ...` 是 workspace 文档资源命令。
  - `struggle-skills/.../SKILL.md` 是本地 agent skill 文件。
- 资源命令漏传 `--remote` 或 `--workspace`。
- update 漏传 `--revision`。
- 误判 JSON 契约：
  - `list --json` 返回 `{ items: [...] }`
  - `delete --json` 返回 `{ deleted: true, id }`
  - `get --json` 透传上游 JSON
- 忽略错误结构：`{ code, message, status?, details?, currentRevision? }`。
- 把 hub 的 `activity` 误当作当前 CLI 资源。

## Examples

```bash
struggle wiki tree --remote prod --workspace design-workspace --json
```

```bash
struggle wiki get wk_123 --remote prod --workspace design-workspace --json
```
