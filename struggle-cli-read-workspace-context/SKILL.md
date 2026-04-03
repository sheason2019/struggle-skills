---
name: struggle-cli-read-workspace-context
description: Read workspace context from wikis before analysis or write actions. Use when summarizing current wiki state, checking whether a node exists, collecting revision values for updates, or grounding recommendations in actual workspace data.
version: 0.1.2
---

# struggle-cli-read-workspace-context

## What this skill is for

在分析、总结、改写或写操作前，先读取 workspace 当前上下文，避免脱离现状给出建议或执行变更。

## Required inputs

- `remoteName`
- `workspaceName`
- `wikiId`（仅 `get` 时需要）

## Command patterns

```bash
# list
struggle wiki list --remote <remoteName> --workspace <workspaceName> [--json]

# tree
struggle wiki tree --remote <remoteName> --workspace <workspaceName> [--json]

# get
struggle wiki get <wikiId> --remote <remoteName> --workspace <workspaceName> [--json]
```

## Workflow

1. 先确认 remote。
2. 读取 `wiki tree` 与 `wiki list`，理解结构化知识和节点关系。
3. 把 root 视为“显示出来但不编号的入口节点”，优先先读 root，再看编号后的章节树。
4. 使用章节编号理解结构：
   - 根下直接子节点是 `1`、`2`
   - 子节点继续是 `1.1`、`1.2`
   - 若 human output 里写的是“顶层章节 / 上级章节”，以此为准，不必回退到 `parentId` 心智
5. 锁定目标 wiki 节点后执行 `get` 读取完整内容。
6. 若需要确认最近发生过什么，先用 `struggle activity list`；需要更完整的时间线展示时再去 hub 的 `activity` 页面回看。
7. 程序化分析或后续自动流程使用 `--json`。
8. 把读取结论作为后续 create/update/delete 的唯一依据。

## Common mistakes to avoid

- 只读一条记录就下全局结论，跳过列表上下文。
- 写操作前不读取目标 wiki 节点详情。
- 忘记 `--json` 导致解析不稳定。
- 跳过 `wiki tree`，导致父子节点关系理解错误。
- 把 root 误判成可忽略的隐藏节点，或者把 root 也编号成 `1`。
- 看到 root 下的节点就说“上级章节是 root”；在目录语义里，这类节点应理解为“顶层章节”。
- 忘记 `struggle activity list` 已经可以读取 activity 窗口。

## Examples

```bash
struggle wiki tree --remote prod --workspace design-workspace --json
struggle wiki list --remote prod --workspace design-workspace --json
```

```bash
struggle wiki get wk_2048 --remote prod --workspace design-workspace --json
```
