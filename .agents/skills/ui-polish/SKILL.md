---
name: ui-polish
description: Make web UI feel hand-crafted and premium instead of generic or "AI-built," through specific motion and depth techniques: tuned cubic-bezier easing (never the browser defaults), a design-token foundation, layered low-opacity shadows with a hairline ring instead of a border, blur-in entrances, tactile press states, drag physics with momentum and magnetic snap points, grid-rows expand/collapse and FLIP layout moves, and state-driven components (idle/hover/pressed/loading/disabled/...). Use when building or refining the CSS/animation layer of web components and the user wants polish, "premium feel," smooth or tactile micro-interactions, or to fix UI that looks generic. Not for high-level visual design direction, layout, typography, color palettes, or copywriting; not for React architecture, data fetching, or non-web UI.
---

# UI Polish

Polish is consistency plus a hundred deliberate motion/depth decisions — not a thing you get from "make it premium." Give exact numbers, reuse one token set, and treat every component as a system of states. The values below are tuned by feel; copy them as defaults, then nudge.

## Define tokens before building any component

Establish these first, then make every state/hover/dark-mode variant pull from them. No one-off `13px` radii or random `0.3s` timings.

```css
:root {
  /* Easing — never use the browser defaults (ease, ease-in-out, linear) */
  --ease-smooth: cubic-bezier(0.22, 1, 0.36, 1);    /* default for almost everything */
  --ease-out:    cubic-bezier(0.17, 1, 0.32, 1);    /* decorative entrances */
  --ease-spring: cubic-bezier(0.35, 1.55, 0.65, 1); /* badges, pops, overshoot */
  --ease-in-out: cubic-bezier(0.66, 0, 0.34, 1);    /* symmetric moves */

  /* Corner radius */
  --radius-sm: 6px;
  --radius-md: 12px;
  --radius-lg: 24px;

  /* Duration */
  --duration-fast:   150ms;
  --duration-normal: 200ms;
  --duration-slow:   280ms;

  /* Shadows — see "Depth" below */
  --shadow-card:
    0 1px 2px rgba(0,0,0,0.05),     /* close drop   */
    0 2px 4px rgba(0,0,0,0.02),     /* soft spread  */
    0 0 0 0.5px rgba(0,0,0,0.08);   /* hairline ring */
  --shadow-elevated:
    0 4px 8px rgba(0,0,0,0.02),     /* spread       */
    0 8px 12px rgba(0,0,0,0.02),    /* wide ambient */
    0 2px 4px rgba(0,0,0,0.02),     /* mid          */
    0 1px 2px rgba(0,0,0,0.04),     /* contact      */
    0 0 0 0.5px #e0e0e0;            /* hairline ring */
}
```

## Easing

The single biggest "a human built this" tell. The browser defaults (`ease`, `ease-in-out`) read as generic — banned. Default everything to `--ease-smooth`; use `--ease-spring` only for things that should pop in with overshoot (badges, counts).

## Depth: layered light, never one shadow

A single flat blur is an instant tell. Real objects cast several faint shadows at once. Rules baked into the tokens above:

- **Hairline ring (`0 0 0 0.5px`) replaces the border.** Biggest tell of hand-made UI — the edge is defined by light, not a 1px stroke.
- **Opacities stay tiny, ~2–8%.** Heavy shadows look cheap; depth is the sum of many faint layers.
- **Stack several blurs at different sizes** — a tight contact shadow plus a wide soft ambient.

Animate the whole stack on hover (card → elevated), but see Performance before doing it across long lists.

## Entrances blur in — never just fade

A plain fade is the least premium entrance. Combine three things so content focuses into place:

```css
@keyframes enter {
  from { opacity: 0; transform: translateY(6px); filter: blur(2px); }
  to   { opacity: 1; transform: translateY(0);   filter: blur(0); }
}
/* ~280–320ms on var(--ease-smooth) */
```

The clearing blur is the secret ingredient. Same pattern, scaled down, for tooltips: fade + lift 4px + clear a 2px blur — never instant pop-in.

## Tactile press

Every interactive element responds to press. Default:

```css
.button:active { transform: scale(0.98); }  /* var(--duration-fast) */
```

`0.98`, not `0.9` — a firm press, not a collapse. Apply to buttons, swatches, tabs, rows — anything clickable.

## Drag physics

A timed fade/slide on a draggable feels dead. Make it feel like a physical object:

- **Velocity tracking**, smoothed over time, so a flick carries weight.
- **Momentum on release** — keep moving, decelerate to rest like sliding across a table.
- **Soft boundaries** — at the edge, stretch a little and spring back, don't stop hard. This is the "iOS, not web input" difference.

For values a duration can't express (counters, live numbers), use a **spring** (tuned stiffness/bounce/mass), not a fixed time.

## Snap points = free haptics

As a handle nears a meaningful value, magnetize to it. Make it feel real with a **two-zone system**: a tight pull-in zone to snap in, a larger release zone to break free — so once snapped, you have to mean it to leave. **Flash/pulse the label when it catches** for micro-feedback.

## Reveal height the right way

Don't use `max-height: 9999px` — jittery, mistimed. Animate CSS grid rows to the content's real height:

```css
.reveal { display: grid; grid-template-rows: 0fr;
          transition: grid-template-rows .22s var(--ease-smooth); }
.reveal[data-open="true"] { grid-template-rows: 1fr; }
.reveal > * { overflow: hidden; }
```

For an element moving **across the layout** (a card flying into another container), use **FLIP** (First → Last → Invert → Play): measure start, measure end, visually jump it back to start, then animate to end. Impossibly smooth from two position measurements.

## Performance & accessibility (part of polish)

- **Honor `prefers-reduced-motion`** everywhere: animations collapse to instant, decorative loops stop.
- **Favor cheap properties** (`transform`, `opacity`) over heavy ones (shadow stacks, height reveals) for 60fps. Heavy effects are worth it only in small, deliberate moments — never animated across long lists or large surfaces.

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after { animation: none !important; transition: none !important; }
}
```

## State-driven components (the actual job)

A component is not a picture — it's a system of states: idle / hover / pressed / loading / disabled / success / empty / error. Build the states you know up front; expect two or three more to surface only once you're dragging the live thing — that's where the polish lives. Micro-interactions discovered through use, not specced:

- Numbers that **roll** digit-by-digit instead of hard-cutting.
- A **shimmer** sweep across a label while a task is working (a calm light gliding across the word, looping ~2s — not a spinner).
- Play/pause icons that **cross-fade and scale** between each other rather than swapping.

## Working / iterating

- **Numbers, never adjectives.** "Smooth" is unbuildable; a specific curve at a specific duration is. State the exact `cubic-bezier` and ms.
- **Lead with the tokens, forbid one-off values.** This alone kills most of the generic look.
- **Name the states**, then build exactly those — no more.
- **Tune one variable at a time.** Isolate: now only the shadow stack, now only the entrance. Don't thrash.
- **Anchor to a reference feel** — "like an iOS sheet: weighty, slightly springy, settles fast" lands better than abstractions.
- **Figma/MCP handoff:** never assume it lands pixel-perfect. Point at the selection and enumerate every property to copy — padding, gaps, tokens, colors, radius, type sizes and weights.
