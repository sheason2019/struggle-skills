---
name: struggle-cli-update-workspace-record
description: Update existing workspace records safely under optimistic concurrency constraints. Use when changing title/status/tags/content/logged-at for wiki/worklog/handbook records and you must fetch latest revision before issuing `update --revision`.
---

# struggle-cli-update-workspace-record

## What this skill is for

在不破坏 revision 契约的前提下安全更新 workspace 记录。

## Required inputs

- `remoteName`
- `workspaceId`
- 资源类型与 `recordId`
- 最新 `revision`
- 新正文（`--content` 或 `--content-file`）
- 可选：`--title`、`--status`、`--tag`
- `worklog` 可选：`--logged-at`
- `wiki` 可选：`--parent-id`、`--sort-order`

## Command patterns

```bash
struggle <resource> get <id> --remote <remoteName> --workspace <workspaceName> --json
```

```bash
# handbook/worklog/wiki generic update
struggle <resource> update <id> \
  --revision <n> \
  (--content <text> | --content-file <path>) \
  [--title <title>] [--status <draft|active|archived>] [--tag <tag>]... \
  --remote <remoteName> --workspace <workspaceName> [--json]
```

```bash
# worklog extra field
struggle worklog update <id> \
  --revision <n> \
  (--content <text> | --content-file <path>) \
  [--title <title>] [--status <draft|active|archived>] [--logged-at <datetime>] [--tag <tag>]... \
  --remote <remoteName> --workspace <workspaceName> [--json]
```

```bash
# wiki tree adjustment
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

## Common mistakes to avoid

- 跳过读取步骤，直接猜 revision。
- update 漏传 `--revision`。
- update 不传正文（当前命令要求正文输入）。
- revision 冲突后直接重试旧命令而不先刷新记录。
- wiki 调整节点时忘记同步 `sort-order`，导致顺序错乱。

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

```bash
struggle worklog get wl_1024 --remote prod --workspace design-workspace --json
struggle worklog update wl_1024 \
  --revision 3 \
  --logged-at 2026-03-26T10:00:00Z \
  --content-file ./notes/wl-login-timeout-v2.md \
  --remote prod \
  --workspace design-workspace \
  --json
```

```bash
struggle handbook get hb_42 --remote prod --workspace design-workspace --json
struggle handbook update hb_42 \
  --revision 6 \
  --title "工程规范手册 v2" \
  --status active \
  --content-file ./notes/handbook-eng-v2.md \
  --remote prod \
  --workspace design-workspace \
  --json
```
