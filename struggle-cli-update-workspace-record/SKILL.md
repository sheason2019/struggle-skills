---
name: struggle-cli-update-workspace-record
description: Update existing workspace records safely under optimistic concurrency constraints. Use when changing title/status/tags/content/logged-at for goal/skill/experience/worklog/handbook/proposal records and you must fetch latest revision before issuing `update --revision`.
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
- 可选：`--title`、`--status`、`--tag`（worklog 还可 `--logged-at`）

## Command patterns

```bash
struggle <resource> get <id> --remote <remoteName> --workspace <workspaceName> --json
```

```bash
# goal/skill/experience/handbook/proposal
struggle <resource> update <id> \
  --revision <n> \
  (--content <text> | --content-file <path>) \
  [--title <title>] [--status <draft|active|archived>] [--tag <tag>]... \
  --remote <remoteName> --workspace <workspaceName> [--json]
```

```bash
# worklog
struggle worklog update <id> \
  --revision <n> \
  (--content <text> | --content-file <path>) \
  [--title <title>] [--status <draft|active|archived>] [--logged-at <datetime>] [--tag <tag>]... \
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
- 在错误 workspace 下更新到同名 id。

## Examples

```bash
struggle goal get g_1 --remote prod --workspace design-workspace --json
struggle goal update g_1 \
  --revision 8 \
  --title "Q2 发布质量门禁（修订）" \
  --status active \
  --content-file ./notes/goal-q2-gate-v2.md \
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

```bash
struggle proposal get p_17 --remote prod --workspace design-workspace --json
struggle proposal update p_17 \
  --revision 2 \
  --status active \
  --content-file ./notes/proposal-auth-refactor-v2.md \
  --remote prod \
  --workspace design-workspace \
  --json
```
