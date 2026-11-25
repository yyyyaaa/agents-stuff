---
name: audit-copywriting
description: "Audit and rewrite in-product UI copy (button and menu labels, dialog and confirmation text, error and empty-state messages, inline help, onboarding, settings descriptions) for distracted users who don't read: cut words so the text that remains gets read, remove padding such as \"please\", \"thank you\", and \"are you sure?\", drop confirmations for harmless actions, and replace instructions with good defaults and self-explaining labels. Based on Joel Spolsky's \"Designing for People Who Have Better Things To Do With Their Lives\". Use when the user asks to audit, review, tighten, simplify, or cut UI copy, microcopy, dialog text, or error messages, or says an interface is too wordy. Not for marketing or landing-page copy, brand voice, docs, READMEs, or long-form writing; not for visual layout or interaction design (see web-interface-polish)."
---

# Audit copywriting

Source: [Designing for People Who Have Better Things To Do With Their Lives](https://www.joelonsoftware.com/2000/04/26/designing-for-people-who-have-better-things-to-do-with-their-lives/) (Joel Spolsky, 2000).

Treat these as facts even when they are not literally true:

- Users don't have the manual, and they wouldn't read it if they did.
- Users don't read on-screen text either. They read just in time, only what they need.
- You get about 30% of a user's attention. The phone rings, the kid cries, the cat is on the mouse.
- The more words on a dialog, the fewer people read any of them.

In Joel's Juno test, an 80-word modem dialog lost to Windows' 5-word version. Experts skipped the text, most novices skipped it and hoped the defaults worked, and the novices who did read it ended up more confused than before. So wordy text does not help novices. Short text helps everyone, the way hotel-bathtub grab bars help people who don't need them.

## Find the copy

Collect every user-visible string in scope:

- Text in markup and components (JSX/TSX, templates, SwiftUI/Compose views)
- Locale source files: `en.json`, `*.strings`, `*.po`, `strings.xml`, `*.arb`, `*.resx`
- `alert`, `confirm`, `prompt`, toast, snackbar, modal, and dialog calls
- Error messages that reach the user, not only logs
- `placeholder`, `title`, `aria-label`, `alt`, and tooltip props
- Empty states, onboarding steps, settings descriptions, inline help

If the app is localized, audit the source-language file only. Leave translations alone.

## Rules

Cite one rule per finding.

1. **Cut words.** Keep the fewest words that let a distracted user act. Delete parenthetical clauses, explanations of how the control works, and sentences that restate the label or title.
2. **Cut padding.** Delete "please", "thank you", "sorry", "note that", "simply", "just", and greetings. Politeness adds bulk, and bulk lowers the share of users who read the text.
3. **Question the dialog before the words.** If the action is harmless or easy to undo, remove the confirmation. Joel watched a user click No on Juno's 11-word exit prompt again and again, because the question made her doubt herself. If the action is harmful, keep the confirmation, name the consequence, and label the buttons with verbs ("Delete 3 files" / "Cancel"), not Yes/No. Drop "Are you sure?".
4. **Fix the UI, not the instructions.** A control that needs a paragraph to explain it has a design problem. Propose a better label, default, or control. Report it under "Design issues", and don't polish the paragraph.
5. **Use plain words.** Use short sentences with common words and no memo prose. Readers may be children, non-native speakers, or in a hurry.
6. **Teach the domain, not the software.** Short concept text can stay where users lack domain knowledge (Joel's example is QuickBooks teaching accounting). Cut text that explains the software itself.
7. **Keep what the user needs to act.** An error keeps what happened and what to do next. Keep the product's existing term for a thing, and don't introduce a synonym.

## Gotchas

- Keep interpolation placeholders (`{count}`, `%@`, `%s`, `{{name}}`) and plural or ICU syntax intact. Count placeholders before and after each rewrite.
- `aria-label` and `alt` text must still describe the element on its own. Don't cut them down until they are ambiguous.
- Don't rewrite legal, consent, compliance, or pricing text. Flag it for the user.
- Tests, snapshots, and E2E selectors often match on copy. Find them before you edit, and update them in the same change.

## How to work

1. **Collect** the strings in the scope the user named. If no scope is given, use the whole app, and say so.
2. **Judge** each string against the rules. Skip strings that already pass, and don't report them.
3. **Report** the findings in the format below. Then stop and wait for the user's approval.
4. **Apply** only the approved rewrites. Edit the source strings and keep the i18n keys. Update the tests and snapshots that assert on the old text. List translations that are now stale.
5. **Verify** with the project's typecheck, lint, and tests. Report any failures with the output.

## Report format

Group findings by file. Use one row per string:

| Location | Current | Problem (rule) | Rewrite | Words |
| --- | --- | --- | --- | --- |
| `src/Exit.tsx:14` | "Thank you for using Juno. Are you sure you want to exit?" | Padding, needless confirm (2, 3) | Remove the dialog | 11 → 0 |

Then add these sections:

- **Design issues.** Dialogs to remove and controls that need instructions to work, each with a proposed fix.
- **Flagged, not rewritten.** Legal, compliance, and pricing text.
- **Totals.** Strings audited, strings changed, and total words before and after.
