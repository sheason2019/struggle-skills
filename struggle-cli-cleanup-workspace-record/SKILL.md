---
name: struggle-cli-cleanup-workspace-record
description: Clean up workspace records through safe delete or archive-oriented closeout workflows. Use when removing stale records, deciding between delete vs `status=archived`, verifying cleanup results, and preventing wrong-type or wrong-workspace deletion.
---

# struggle-cli-cleanup-workspace-record

## What this skill is for

执行删除、收尾和清理动作，减少误删风险，并在操作后验证结果。

## Required inputs

- `remoteName`
- `workspaceId`
- 资源类型与 `recordId`
- 清理策略：直接删除，或改状态到 `archived`

## Command patterns

```bash
# 删除
struggle <resource> delete <id> --remote <remoteName> --workspace <workspaceName> [--json]

# 非删除收口（推荐先考虑）
struggle <resource> update <id> --revision <n> --status archived --content-file <path> --remote <remoteName> --workspace <workspaceName> [--json]
```

```bash
# 删除后验证
struggle <resource> get <id> --remote <remoteName> --workspace <workspaceName> [--json]
struggle <resource> list --remote <remoteName> --workspace <workspaceName> [--json]
```

## Workflow

1. 先 `get` 目标记录，确认资源类型、id、workspace 正确。
2. 判断删除还是状态收口：
   - 需要保留历史时优先 `status=archived`。
   - 明确无需保留时再 `delete`。
3. 执行操作并使用 `--json` 获取结构化结果。
4. 删除后用 `list/get` 复核结果。
5. 把清理结果回报给用户（删除了什么、剩余什么）。

## Common mistakes to avoid

- 未确认资源类型就直接删（例如把 skill id 当 goal id）。
- 在错误 workspace 下执行 delete。
- 删除前未让用户确认是否允许保留为 archived。
- 删除后不做验证。
- 忽略 `delete --json` 的标准返回 `{ deleted: true, id }`。

## Examples

```bash
# 直接删除
struggle experience get exp_77 --remote prod --workspace design-workspace --json
struggle experience delete exp_77 --remote prod --workspace design-workspace --json
struggle experience list --remote prod --workspace design-workspace --json
```

```bash
# 收口而非删除
struggle goal get g_2 --remote prod --workspace design-workspace --json
struggle goal update g_2 --revision 11 --status archived --content-file ./notes/g2-archive.md --remote prod --workspace design-workspace --json
```
