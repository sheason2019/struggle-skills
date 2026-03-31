---
name: struggle-cli-read-workspace-context
description: Read workspace context from wikis, worklogs, and handbooks before analysis or write actions. Use when summarizing current state, checking whether a record exists, collecting revision values for updates, or grounding recommendations in actual workspace data.
---

# struggle-cli-read-workspace-context

## What this skill is for

在分析、总结、改写或写操作前，先读取 workspace 当前上下文，避免脱离现状给出建议或执行变更。

## Required inputs

- `remoteName`
- `workspaceId`
- 资源类型与目标 id（仅 `get` 时需要）

## Command patterns

```bash
# list
struggle wiki list --remote <remoteName> --workspace <workspaceName> [--json]
struggle worklog list --remote <remoteName> --workspace <workspaceName> [--json]
struggle handbook list --remote <remoteName> --workspace <workspaceName> [--json]

# tree
struggle wiki tree --remote <remoteName> --workspace <workspaceName> [--json]

# get
struggle wiki get <wikiId> --remote <remoteName> --workspace <workspaceName> [--json]
struggle worklog get <worklogId> --remote <remoteName> --workspace <workspaceName> [--json]
struggle handbook get <handbookId> --remote <remoteName> --workspace <workspaceName> [--json]
```

## Workflow

1. 先确认 remote。
2. 按任务范围读取列表（单资源或三类资源全读）。
3. 需要理解知识结构时优先读取 `wiki tree`。
4. 锁定目标记录后执行 `get` 读取完整内容。
5. 程序化分析或后续自动流程使用 `--json`。
6. 把读取结论作为后续 create/update/delete 的唯一依据。

## Common mistakes to avoid

- 只读一条记录就下全局结论，跳过列表上下文。
- 写操作前不读取目标记录详情。
- 忘记 `--json` 导致解析不稳定。
- 跳过 `wiki tree`，导致父子节点关系理解错误。

## Examples

```bash
struggle wiki tree --remote prod --workspace design-workspace --json
struggle wiki list --remote prod --workspace design-workspace --json
struggle worklog list --remote prod --workspace design-workspace --json
struggle handbook list --remote prod --workspace design-workspace --json
```

```bash
struggle wiki get wk_2048 --remote prod --workspace design-workspace --json
```

```bash
struggle handbook get hb_42 --remote prod --workspace design-workspace --json
```
