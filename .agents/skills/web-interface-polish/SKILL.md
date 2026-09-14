---
name: web-interface-polish
description: Design or review web interfaces against distilled platform-grade interface principles — aesthetic integrity, consistency, direct manipulation, visible feedback, metaphor, and user control; clarity-deference-depth hierarchy; semantic color adapting to light and dark; hierarchical type scale; minimum pointer target sizes; purposeful motion; and conventions for navigation, modality, and inputs. Use when building or critiquing the structure, behavior, and interaction quality of web UI. Not for CSS animation and easing techniques or visual craft details (see ui-polish), color palette or typography mechanics, brand identity, or native platform code.
---

# Web Interface Polish

Interface quality comes from a small set of principles applied relentlessly, not decoration. Every screen should pass: can a first-time user say what this is, what they can do, and what just happened after they acted?

## The six principles

Apply these to every component and flow:

- **Aesthetic integrity** — appearance matches function. A tool for serious work looks quiet and dense; a consumer app can be expressive. Mismatched styling erodes trust faster than no styling.
- **Consistency** — same patterns, same behavior, everywhere. Standard conventions beat novel ones; innovate in the product, not the chrome.
- **Direct manipulation** — users act on the thing, not a proxy. Drag the item, click the text to edit it, resize the pane itself. Visible controls over hidden commands.
- **Feedback** — every action acknowledges immediately and shows its result. Nothing responds silently.
- **Metaphors** — use familiar objects and verbs (trash, archive, pin, draft). When the metaphor is known, the interface teaches itself.
- **User control** — the user initiates; the interface suggests and confirms. Never trap, never auto-act on destructive or consequential operations.

## Clarity, deference, depth

Three tests applied at every level:

- **Clarity** — text legible at every size, icons precise, controls obvious about what they do. If a label needs a tooltip to be understood, fix the label.
- **Deference** — content is the hero; the interface recedes. Chrome (borders, fills, shadows) exists to serve content, never to compete with it. When in doubt, remove.
- **Depth** — layering and motion convey hierarchy. Panels above content, transient UI above panels. Motion explains where things came from and where they went.

## Hierarchy through restraint

- One primary action per view. If two buttons compete, demote one to secondary or tertiary.
- Create levels with size, weight, and spacing — not decoration. Adjacent levels should be unmistakably different (≈1.2× scale steps minimum, commonly 1.25–1.5).
- Group by proximity: related items closer together than unrelated ones. Spacing communicates grouping before borders do.
- Align to a shared grid. Almost nothing should be positioned uniquely.
- Line length 45–75 characters for prose; full-width body text is a hierarchy failure, not a layout choice.

## Color

- Define semantic tokens (`bg`, `fg`, `muted-fg`, `accent`, `destructive`, `border`) and reference only those — never raw hex values in components.
- Every color adapts to light and dark. Design both; don't invert and hope.
- Never rely on color alone — pair with icon, text, or shape (error states, required fields, status).
- Contrast: ≥4.5:1 for body text, ≥3:1 for large text (≥24px / ≥19px bold) and for UI affordances (borders of inputs, focus rings, icons carrying meaning).
- Accent color is scarce on purpose. If everything is accented, nothing is.

## Typography

- One family for the interface (system stack is fine); a second only for prose or branding.
- Hierarchy through weight, size, and color within that family — not through more fonts.
- Body text: 15–17px, line-height 1.4–1.6. Secondary/muted text: still ≥12px, never a faint crutch for a missing hierarchy decision.
- UI text is sentence case and terse. Buttons are verbs ("Save draft"), not nouns and not "OK".

## Icons

- One icon system: same grid, same stroke weight, same corner radius across the set.
- Prefer universally recognized metaphors (search = magnifier, settings = gear). Ambiguous icons get text labels — icon-only is for the already-learned.
- Size icons to match adjacent text's visual weight, not its nominal size.

## Targets and inputs

- Pointer targets ≥44×44px including padding, even if the visible glyph is smaller. Dense desktop UI may go to 32px, never below 24px.
- Every interactive element exposes hover, focus-visible, active/pressed, and disabled states. Focus ring is never removed without a visible replacement.
- Inputs get persistent visible labels — placeholders are hints, not labels (they vanish when typing starts).
- Validate inline on blur/submit with the message next to the field. Never a summary list of errors at the top after a failed submit.
- Everything works with keyboard alone: logical tab order, Enter/Space activate, Escape dismisses, no focus traps except modals that release on close.

## Feedback and states

- Perceived response <100ms for direct actions (press, toggle). Anything slower gets an immediate optimistic ack or a progress indicator.
- >1s: show progress. Determinate when progress is known; otherwise skeleton screens for content, spinners only for short indeterminate waits.
- Every component is designed in all states: empty, loading, error, success, permission-denied, disabled. The empty state teaches what the full state does.
- Errors say what happened and what to do next, in plain language. "Something went wrong" is a failed state, not an error message.
- Confirm destructive or hard-to-reverse actions; make safe actions instant and undoable instead of confirmed.

## Motion

- Motion is functional or absent: it orients (where did this panel come from), confirms (the thing was saved), or directs attention. Never decorative-only.
- 150–300ms for UI transitions. Entrances ease out, exits ease in. Longer only for large spatial moves.
- Respect `prefers-reduced-motion` — replace moves with crossfades, keep state changes instant.
- During a transition, the interface stays coherent: no layout jumps, no double-renders, no content flash.

## Modality and interruption

- Modals are for tasks that must be completed or abandoned before continuing — nothing else. Default to inline expansion, panels, or navigation instead.
- Stacked modals are a design failure; restructure the flow.
- Interrupt only when the cost of not interrupting is higher (data loss, security). Banners and inline notices cover everything else.

## Navigation

- Pick the model that matches the content: hierarchical (drill-down), flat (peer sections via tabs/sidebar), or content-driven (the content itself links onward). Don't mix models for the same data.
- The user always knows where they are: persistent active state, breadcrumbs past two levels deep, page titles that match the nav item that led there.
- Back behaves as expected — restores scroll position and prior state, never discards work silently.

## Review checklist

When critiquing a screen or PR, check in order:

1. Purpose legible in <3s: what is this, what's the primary action?
2. Single primary action; hierarchy readable at a squint.
3. All states present (empty/loading/error/success/disabled) — not just the happy path.
4. Every action gives immediate feedback; every wait shows progress.
5. Semantic tokens only; both light and dark verified; contrast ratios met.
6. Targets ≥44px; visible labels on inputs; full keyboard operability with visible focus.
7. Motion purposeful and reduced-motion-safe.
8. No modal that could be inline; no error that doesn't say what to do; no color-only meaning.
