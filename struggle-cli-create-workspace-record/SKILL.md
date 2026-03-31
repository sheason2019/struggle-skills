---
name: struggle-cli-create-workspace-record
description: Create new workspace records with correct resource selection and required fields. Use when a user asks to add a wiki/worklog/handbook record and you need reliable create commands with explicit remote/workspace context and content handling.
---

# struggle-cli-create-workspace-record

## What this skill is for

根据用户意图创建一条 workspace 记录，并确保资源类型选择与必填字段完整。

## Required inputs

- `remoteName`
- `workspaceId`
- 目标资源类型：`wiki|worklog|handbook`
- 标题 `title`
- 状态 `status`（`draft|active|archived`）
- 正文（`--content` 或 `--content-file`）
- 若资源是 `worklog`：还需要 `--logged-at <datetime>`
- 若资源是 `wiki`：可选 `--parent-id <wikiId>` 与 `--sort-order <number>`
- 可选：`--tag <tag>`（可重复）

## Command patterns

```bash
# wiki / handbook
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

```bash
# wiki with tree placement
struggle wiki create \
  --title <title> \
  --status <draft|active|archived> \
  (--content <text> | --content-file <path>) \
  [--parent-id <wikiId>] [--sort-order <number>] \
  [--tag <tag>]... \
  --remote <remoteName> \
  --workspace <workspaceName> \
  [--json]
```

## Workflow

1. 先判断记录类型：知识节点用 `wiki`，时间线记录用 `worklog`，入职/规范手册用 `handbook`。
2. 组织最小必填字段：`title + status + content`（worklog 额外 `logged-at`）。
3. 如果是 wiki，先通过 `wiki tree` 决定挂载位置（`parent-id` 和 `sort-order`）。
4. 正文较长时先写文件，使用 `--content-file`。
5. 执行 create。
6. 用 `--json` 读取返回并提取 id；必要时立刻 `get` 回读验证。

## Common mistakes to avoid

- 资源类型选错（例如把手册写到 worklog）。
- 漏传 `--remote` 或 `--workspace`。
- worklog create 漏传 `--logged-at`。
- wiki 未设置父节点导致节点挂错位置。
- 创建后不回读，导致 id/内容确认缺失。

## Examples

```bash
struggle wiki create \
  --title "登录模块知识地图" \
  --status active \
  --tag auth --tag architecture \
  --parent-id wk_root \
  --sort-order 20 \
  --content-file ./notes/wiki-auth-map.md \
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
