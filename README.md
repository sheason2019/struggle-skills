# struggle-skills

This directory contains publishable local skills for current Struggle workflows.

## Versioning

- `struggle-skills/package.json` is the canonical target version source consumed by Struggle Hub setup prompts.
- Each published `SKILL.md` also carries the same `version` in frontmatter so an installed copy can be probed locally before deciding whether to reinstall.

## Current coverage

These skills are aligned with the current `struggle-ai` capability surface:

- `struggle remote ...` for remote setup and verification
- `struggle wiki ...` for structured workspace knowledge
- `struggle activity ...` for reading workspace activity and recording manual milestone updates

Important limits:

- Hub Web remains the only UI surface for browsing the full activity timeline
- Activity writes are fleet-token driven and should go through `struggle activity create/update`
- Wiki edits no longer auto-create activity records; agents should write milestone activity explicitly

## Directory contract

- Each skill lives under `struggle-skills/<skill-name>/SKILL.md`.
- `SKILL.md` must include YAML frontmatter with at least:
  - `name`
  - `description`
  - `version`

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
