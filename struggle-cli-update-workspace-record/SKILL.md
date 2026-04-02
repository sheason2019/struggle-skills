---
name: struggle-cli-update-workspace-record
description: Update workspace wiki nodes safely under optimistic concurrency constraints. Use when changing title, status, tags, content, or wiki placement and you must fetch the latest revision before issuing `update --revision`.
version: 0.1.0
---

# struggle-cli-update-workspace-record

## What this skill is for

在不破坏 revision 契约的前提下安全更新 workspace wiki 节点。

## Required inputs

- `remoteName`
- `workspaceName`
- `wikiId`
- 最新 `revision`
- 新正文（`--content` 或 `--content-file`）
- 可选：`--title`、`--status`、`--tag`
- `wiki` 可选：`--parent-id`、`--sort-order`

## Command patterns

```bash
struggle wiki get <id> --remote <remoteName> --workspace <workspaceName> --json
```

```bash
# wiki update
struggle wiki update <id> \
  --revision <n> \
  (--content <text> | --content-file <path>) \
  [--title <title>] [--status <draft|active|archived>] [--parent-id <wikiId>] [--sort-order <number>] [--tag <tag>]... \
  --remote <remoteName> --workspace <workspaceName> [--json]
```

## Workflow

1. 先 `get --json` 读取当前记录。
2. 取最新 `revision` 与当前内容。
3. 仅修改目标字段，其他字段保持不变。
4. 执行 `update --revision <latest>`。
5. 若失败且包含 revision 冲突信息，重新 `get` 最新记录后再更新。
6. 更新后 `get` 一次确认结果。
7. 如果需求只是“回看最近发生了什么”，不要改记录，回 hub 的 `activity` 时间线确认。

## Common mistakes to avoid

- 跳过读取步骤，直接猜 revision。
- update 漏传 `--revision`。
- update 不传正文（当前命令要求正文输入）。
- revision 冲突后直接重试旧命令而不先刷新记录。
- wiki 调整节点时忘记同步 `sort-order`，导致顺序错乱。
- 把历史审计需求误当作记录更新需求。

## Examples

```bash
struggle wiki get wk_1 --remote prod --workspace design-workspace --json
struggle wiki update wk_1 \
  --revision 8 \
  --title "登录模块知识地图（修订）" \
  --status active \
  --parent-id wk_auth \
  --sort-order 30 \
  --content-file ./notes/wiki-auth-map-v2.md \
  --remote prod \
  --workspace design-workspace \
  --json
```
