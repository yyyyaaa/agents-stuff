# agents-stuff

Personal collection of [Agent Skills](https://agentskills.io/specification).

Source of truth: `.agents/skills/<skill-name>/`. Installed by symlink into
`~/.agents/skills/` and `~/.claude/skills/` so edits in this repo are picked
up live by every compatible agent.

## Install

```bash
bin/install      # symlinks every .agents/skills/* into both target dirs
bin/uninstall    # removes those symlinks (only its own; ignores others)
bin/validate     # checks each skill against the spec
```

`bin/install` is idempotent. It only creates symlinks; it never overwrites
real files or directories at the target path.

## Authoring a new skill

1. `mkdir .agents/skills/<your-skill-name>` and create `SKILL.md` inside it.
2. Add frontmatter — `name` must match the directory name exactly; `description` is the only activation trigger, so write it carefully (both what and when).
3. Write the body — keep under ~500 lines. Move long material to `references/`.
4. `bin/validate` to check.
5. `bin/install` to link it into `~/.agents/skills/` and `~/.claude/skills/`.

## Spec essentials

- Frontmatter: `name` (lowercase + hyphens, ≤64 chars, **must match directory name**) and `description` (≤1024 chars).
- The `description` is the *only* mechanism the agent uses to decide when to activate. Cover both **what** the skill does and **when** to use it. Be explicit about non-triggers if the skill borders on adjacent territory.
- Body ≤ 500 lines / ~5k tokens. Use `references/`, `assets/`, `scripts/` for bigger material — load on demand.
- Optional fields: `license`, `compatibility`, `metadata` (free-form), `allowed-tools`.

## Current skills

- **clarify** — interview the user one question at a time to resolve open decisions in a plan.
- **ui-polish** — refine web UI motion, depth, and interaction states for a hand-crafted feel.
