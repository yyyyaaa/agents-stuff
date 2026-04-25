---
name: replace-with-skill-name
description: One sentence on what the skill does. One sentence on when to activate (and when NOT to, if the skill borders on adjacent territory). Include the exact phrasing users would actually use. Max 1024 characters. The description is the only signal an agent uses to decide whether to activate the skill — invest in it.
---

# <Skill Name>

<One paragraph: the user-facing job this skill performs.>

## When to activate

<Optional. Expand the activation guidance from the description if it needs more nuance than fits there. Skip this section if the description is enough.>

## How

<Step-by-step procedure, examples. Move long reference material into `references/<file>.md` and call it out conditionally:
"Read `references/api-errors.md` if the API returns a non-200 status code.">

## Gotchas

- <Specific environment-/project-specific facts that defy reasonable assumptions. Skip the section if you don't have any — generic gotchas like "handle errors" are worse than no section at all.>
