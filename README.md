# struggle-skills

This directory contains publishable local skills for current Struggle workflows.

## Current coverage

These skills are aligned with the current `struggle-ai` capability surface:

- `struggle remote ...` for remote setup and verification
- `struggle wiki ...` for structured workspace knowledge

What is not covered here:

- `worklog` is obsolete and no longer part of the active CLI surface
- `activity` exists in Struggle Hub as a read-only timeline, but it is not currently exposed as a `struggle-cli` command
- `proposal` is not part of the current local skill pack because the active CLI workflow surface here is centered on `remote` and `wiki`

## Directory contract

- Each skill lives under `struggle-skills/<skill-name>/SKILL.md`.
- `SKILL.md` must include YAML frontmatter with at least:
  - `name`
  - `description`

## Validate discoverability

```bash
pnpm run skills:list:local
```

This should list the current six skills without structural errors.

## Publish skills

`skills publish` works on a single skill directory, not on `struggle-skills/` root.

```bash
npx -y skills publish ./struggle-skills/struggle-cli-basics --non-interactive --access public --region cn
```

Publish all skills in one shot:

```bash
pnpm run skills:publish:all
```
