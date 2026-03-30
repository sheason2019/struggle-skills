# struggle-skills

This directory contains publishable local skills for Struggle CLI workflows.

## Directory contract

- Each skill lives under `struggle-skills/<skill-name>/SKILL.md`.
- `SKILL.md` must include YAML frontmatter with at least:
  - `name`
  - `description`

## Validate discoverability

```bash
pnpm run skills:list:local
```

## Publish skills

`skills publish` works on a single skill directory, not on `struggle-skills/` root.

```bash
npx -y skills publish ./struggle-skills/struggle-cli-basics --non-interactive --access public --region cn
```

Publish all skills in one shot:

```bash
pnpm run skills:publish:all
```
