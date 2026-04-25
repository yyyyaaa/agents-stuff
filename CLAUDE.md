# Working in this repo

This repo is a personal collection of [Agent Skills](https://agentskills.io/specification).

## Layout

- `.agents/skills/<name>/SKILL.md` — source of truth for each skill
- `templates/SKILL.md` — copy-this-to-start a new skill
- `bin/install`, `bin/uninstall`, `bin/validate` — manage symlinks and check skill validity

## Spec rules to keep in mind

- `name` must match the directory name exactly: lowercase, hyphens only, ≤64 chars, no leading/trailing/consecutive hyphens.
- `description` is the only trigger mechanism. Cover both what the skill does and when to use it. Be precise about non-triggers when the skill borders on adjacent territory.
- Body ≤ 500 lines. Move big material to `references/`. Reference files explicitly with load conditions ("Read `references/X.md` when Y").

## Authoring conventions in this repo

- Be terse. The body is read by an agent — skip explanations of general concepts.
- Lead with what the agent needs that it can't infer from code or training.
- Use a `## Gotchas` section only when there are real, specific, non-obvious environment-/project-specific facts. Skip it otherwise.
- Validate after edits: `bin/validate`.

## Don't

- Don't run `bin/install` automatically — let the user run it.
- Don't add a skill without first agreeing on its description with the user. Description quality is most of the skill's value.
- Don't trim the `.agents/skills/` directory or symlinks under `~/.agents/skills/` or `~/.claude/skills/` without checking — `bin/uninstall` is the safe path.
