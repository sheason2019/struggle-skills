---
name: struggle-cli-read-workspace-context
description: Read workspace context from goals, skills, experiences, worklogs, handbooks, and proposals before analysis or write actions. Use when summarizing current state, checking whether a record exists, collecting revision values for updates, or grounding recommendations in actual workspace data.
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
struggle goal list --remote <remoteName> --workspace <workspaceName> [--json]
struggle skill list --remote <remoteName> --workspace <workspaceName> [--json]
struggle experience list --remote <remoteName> --workspace <workspaceName> [--json]
struggle worklog list --remote <remoteName> --workspace <workspaceName> [--json]
struggle handbook list --remote <remoteName> --workspace <workspaceName> [--json]
struggle proposal list --remote <remoteName> --workspace <workspaceName> [--json]

# get
struggle goal get <goalId> --remote <remoteName> --workspace <workspaceName> [--json]
struggle skill get <skillId> --remote <remoteName> --workspace <workspaceName> [--json]
struggle experience get <experienceId> --remote <remoteName> --workspace <workspaceName> [--json]
struggle worklog get <worklogId> --remote <remoteName> --workspace <workspaceName> [--json]
struggle handbook get <handbookId> --remote <remoteName> --workspace <workspaceName> [--json]
struggle proposal get <proposalId> --remote <remoteName> --workspace <workspaceName> [--json]
```

## Workflow

1. 先确认 remote。
2. 按任务范围读取列表（单资源或六类资源全读）。
3. 锁定目标记录后执行 `get` 读取完整内容。
4. 程序化分析或后续自动流程使用 `--json`。
5. 把读取结论作为后续 create/update/delete 的唯一依据。

## Common mistakes to avoid

- 只读一条记录就下全局结论，跳过列表上下文。
- 写操作前不读取目标记录详情。
- 忘记 `--json` 导致解析不稳定。
- 混入不存在的隐式上下文机制（当前需要显式 remote/workspace）。

## Examples

```bash
struggle goal list --remote prod --workspace design-workspace --json
struggle skill list --remote prod --workspace design-workspace --json
struggle experience list --remote prod --workspace design-workspace --json
struggle worklog list --remote prod --workspace design-workspace --json
struggle handbook list --remote prod --workspace design-workspace --json
struggle proposal list --remote prod --workspace design-workspace --json
```

```bash
struggle worklog get wl_1024 --remote prod --workspace design-workspace --json
```

```bash
struggle handbook get hb_42 --remote prod --workspace design-workspace --json
```
