---
name: struggle-cli-manage-remotes
description: Manage Struggle CLI remotes safely through list/show/add/remove workflows. Use when setting up a new environment, switching remotes, diagnosing remote misconfiguration, or cleaning unused remotes before workspace resource operations.
---

# struggle-cli-manage-remotes

## What this skill is for

指导 agent 安全地新增、查看、核对、移除 remote，确保后续 workspace 资源操作使用正确 remote。

## Required inputs

- `remoteName`
- 新增 remote 时：`url`、`token`、作用域（`--global` 或 `--project`）
- 移除 remote 时：作用域（`--global` 或 `--project`）

## Command patterns

```bash
struggle remote list [--json]
struggle remote show <name> [--json]

struggle remote add <name> --url <url> --token <token> (--global|--project) [--force] [--json]
struggle remote remove <name> (--global|--project) [--json]
```

## Workflow

1. 先 `remote list` 查看现状。
2. 如要使用某个 remote，先 `remote show <name>` 核对 URL/sourceScope/tokenMasked。
3. 不存在则 `remote add`，并显式选择 `--global` 或 `--project`。
4. 新增后再次 `remote show` 验证。
5. remote 可用后，优先读取 workspace 的 wiki 树与关键节点，再进入后续写操作。
6. 仅在确认不再需要时执行 `remote remove`，再 `remote list` 复核。

## Common mistakes to avoid

- `remote add/remove` 同时传 `--global` 和 `--project`（必须二选一）。
- 未先 `remote show` 就直接用该 remote 操作 workspace 资源。
- 在错误作用域删除 remote（例如应删 project 却删 global）。
- 忽略 `--json` 导致后续流程无法稳定解析。

## Examples

```bash
struggle remote list --json
```

```bash
struggle remote add prod --url https://hub.example.com --token <token> --project --json
struggle remote show prod --json
```

```bash
struggle remote remove old-prod --project --json
struggle remote list --json
```
