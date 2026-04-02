---
name: struggle-cli-create-workspace-record
description: Create new workspace wiki nodes with the required fields. Use when a user asks to add wiki content and you need reliable commands with explicit remote/workspace context and content handling.
version: 0.1.0
---

# struggle-cli-create-workspace-record

## What this skill is for

根据用户意图创建一个 workspace wiki 节点，并确保必填字段完整。

## Required inputs

- `remoteName`
- `workspaceName`
- 标题 `title`
- 状态 `status`（`draft|active|archived`）
- 正文（`--content` 或 `--content-file`）
- 可选 `--parent-id <wikiId>` 与 `--sort-order <number>`
- 可选：`--tag <tag>`（可重复）

## Command patterns

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

1. 先确认目标是新增 wiki 节点，而不是回看历史或编辑已有节点。
2. 组织最小必填字段：`title + status + content`。
3. 通过 `wiki tree` 决定挂载位置（`parent-id` 和 `sort-order`）。
4. 正文较长时先写文件，使用 `--content-file`。
5. 执行 create。
6. 用 `--json` 读取返回并提取 id；必要时立刻 `get` 回读验证。

## Common mistakes to avoid

- 漏传 `--remote` 或 `--workspace`。
- wiki 未设置父节点导致节点挂错位置。
- 创建后不回读，导致 id/内容确认缺失。
- 把“想回看历史事件”的需求误实现成新建记录；这类需求应优先看 hub 的 `activity`。

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
