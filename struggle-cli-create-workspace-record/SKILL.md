---
name: struggle-cli-create-workspace-record
description: Create new workspace records with correct resource selection and required fields. Use when a user asks to add a goal, skill, experience, worklog, handbook, or proposal and you need reliable create commands with explicit remote/workspace context and content handling.
---

# struggle-cli-create-workspace-record

## What this skill is for

根据用户意图创建一条 workspace 记录，并确保资源类型选择与必填字段完整。

## Required inputs

- `remoteName`
- `workspaceId`
- 目标资源类型：`goal|skill|experience|worklog|handbook|proposal`
- 标题 `title`
- 状态 `status`（`draft|active|archived`）
- 正文（`--content` 或 `--content-file`）
- 若资源是 `worklog`：还需要 `--logged-at <datetime>`
- 可选：`--tag <tag>`（可重复）

## Command patterns

```bash
# goal / skill / experience / handbook / proposal
struggle <resource> create \
  --title <title> \
  --status <draft|active|archived> \
  (--content <text> | --content-file <path>) \
  [--tag <tag>]... \
  --remote <remoteName> \
  --workspace <workspaceName> \
  [--json]
```

```bash
# worklog
struggle worklog create \
  --title <title> \
  --status <draft|active|archived> \
  --logged-at <datetime> \
  (--content <text> | --content-file <path>) \
  [--tag <tag>]... \
  --remote <remoteName> \
  --workspace <workspaceName> \
  [--json]
```

## Workflow

1. 先判断记录类型：目标用 `goal`，方法沉淀用 `skill`，经验复盘用 `experience`，时间线记录用 `worklog`，知识规范用 `handbook`，方案提案用 `proposal`。
2. 组织最小必填字段：`title + status + content`（worklog 额外 `logged-at`）。
3. 正文较长时先写文件，使用 `--content-file`。
4. 执行 create。
5. 用 `--json` 读取返回并提取 id；必要时立刻 `get` 回读验证。

## Common mistakes to avoid

- 资源类型选错（例如把 worklog 记到 experience）。
- 漏传 `--remote` 或 `--workspace`。
- worklog create 漏传 `--logged-at`。
- 正文过长仍用 `--content`，导致命令不可维护。
- 创建后不回读，导致 id/内容确认缺失。

## Examples

```bash
struggle goal create \
  --title "Q2 发布质量门禁" \
  --status active \
  --tag release --tag quality \
  --content-file ./notes/goal-q2-gate.md \
  --remote prod \
  --workspace design-workspace \
  --json
```

```bash
struggle worklog create \
  --title "修复登录超时" \
  --status active \
  --logged-at 2026-03-26T09:30:00Z \
  --content-file ./notes/wl-login-timeout.md \
  --tag backend \
  --remote prod \
  --workspace design-workspace \
  --json
```

```bash
struggle handbook create \
  --title "工程规范手册 v1" \
  --status draft \
  --tag engineering --tag guide \
  --content-file ./notes/handbook-eng-v1.md \
  --remote prod \
  --workspace design-workspace \
  --json
```

```bash
struggle proposal create \
  --title "登录链路重构提案" \
  --status draft \
  --tag auth --tag architecture \
  --content-file ./notes/proposal-auth-refactor.md \
  --remote prod \
  --workspace design-workspace \
  --json
```
