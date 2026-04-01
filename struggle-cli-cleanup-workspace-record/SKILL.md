---
name: struggle-cli-cleanup-workspace-record
description: Clean up workspace wiki nodes through safe delete or archive-oriented closeout workflows. Use when removing stale wiki content, deciding between delete vs `status=archived`, verifying cleanup results, and preventing wrong-workspace deletion.
---

# struggle-cli-cleanup-workspace-record

## What this skill is for

执行 wiki 删除、收尾和清理动作，减少误删风险，并在操作后验证结果。

## Required inputs

- `remoteName`
- `workspaceName`
- `wikiId`
- 清理策略：直接删除，或改状态到 `archived`

## Command patterns

```bash
# 删除
struggle wiki delete <id> --remote <remoteName> --workspace <workspaceName> [--json]

# 非删除收口（推荐先考虑）
struggle wiki update <id> --revision <n> --status archived --content-file <path> --remote <remoteName> --workspace <workspaceName> [--json]
```

```bash
# 删除后验证
struggle wiki get <id> --remote <remoteName> --workspace <workspaceName> [--json]
struggle wiki list --remote <remoteName> --workspace <workspaceName> [--json]
```

## Workflow

1. 先 `get` 目标记录，确认 `wiki` 类型、id、workspace 正确。
2. 判断删除还是状态收口：
   - 需要保留历史时优先 `status=archived`。
   - 明确无需保留时再 `delete`。
3. 对 wiki 节点先检查是否有子节点，避免误删树结构。
4. 执行操作并使用 `--json` 获取结构化结果。
5. 删除后用 `list/get` 复核结果。
6. 把清理结果回报给用户（删除了什么、剩余什么）。
7. 如果用户想回看历史而不是清理内容，优先引导到 hub 的 `activity`，不要误删记录。

## Common mistakes to avoid

- 未确认资源类型就直接删。
- 在错误 workspace 下执行 delete。
- 删除前未让用户确认是否允许保留为 archived。
- 删除 wiki 父节点前不检查子节点。
- 删除后不做验证。
- 把 `activity` 时间线和内容资源删除混为一谈。

## Examples

```bash
# 收口而非删除
struggle wiki get wk_2 --remote prod --workspace design-workspace --json
struggle wiki update wk_2 --revision 11 --status archived --content-file ./notes/wk2-archive.md --remote prod --workspace design-workspace --json
```

```bash
# 直接删除
struggle wiki get wk_77 --remote prod --workspace design-workspace --json
struggle wiki delete wk_77 --remote prod --workspace design-workspace --json
struggle wiki list --remote prod --workspace design-workspace --json
```
