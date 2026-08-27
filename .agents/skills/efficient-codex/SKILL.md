---
name: efficient-codex
description: Use when the user invokes efficient-codex or explicitly asks a GPT-5.6 Sol xhigh coordinator to reduce cost or wall-clock time by dispatching research, coding, and testing across Sol and Terra subagents. The coordinator judges each lane's complexity, selects the worker model and reasoning level, and keeps planning, synthesis, and final acceptance. Do not activate for ordinary Codex tasks when the user has not requested delegation.
---

# Efficient Codex

Run the coordinating agent on `gpt-5.6-sol` with `xhigh` reasoning. This is a
hard precondition, not a dispatch choice. A skill cannot change the active
agent's model or reasoning level, so the caller must select Sol xhigh before
invoking this skill. Treat invocation as asserting that precondition; if the
runtime exposes a different active profile, stop before spawning workers and
tell the user to switch the session. Do not emulate the requirement by spawning
a child orchestrator while a lower-profile root remains in charge.

The Sol xhigh coordinator owns decomposition, complexity judgment, agent
topology, architecture, synthesis, escalation, and final acceptance. Workers
own bounded evidence or implementation lanes. Every worker handoff must say not
to spawn descendants unless the coordinator explicitly authorizes it.

## Where Sol Shines

Keep these decisions with the Sol xhigh coordinator:

- Decomposing ambiguous work into clean parallel slices.
- Architecture, product, and safety tradeoffs.
- Reading conflicting subagent reports and deciding what matters.
- Integrating partial implementations into one coherent result.
- Final review, risk assessment, and user-facing synthesis.

## Judge Complexity and Select a Worker

Classify each lane separately. Judge its ambiguity, coupling to other lanes,
blast radius if wrong, and how objective its success check is. Do not route an
entire project to one profile merely because the overall project is large.

Use only these profiles:

| Profile | Dispatch when |
| --- | --- |
| Sol `xhigh` coordinator | Always. It owns the task graph, routing, architecture, conflict resolution, integration, and final answer. |
| `gpt-5.6-terra` with `max` | The lane is broad or context-heavy but tightly bounded, and correctness can be shown with concrete evidence: repository scans, inventory, documentation extraction, log reduction, test runs, browser checks, or mechanical edits with an exact specification. |
| `gpt-5.6-sol` with `medium` | The lane is narrow, its acceptance criteria are clear, and it needs normal coding or debugging judgment rather than broad architectural reasoning. |
| `gpt-5.6-sol` with `high` | The lane crosses several files or boundaries, has incomplete or competing signals, or needs substantial implementation, diagnosis, or review judgment. |
| `gpt-5.6-sol` with `xhigh` | The lane is independently difficult and high-risk: architecture alternatives, security or data-loss risk, conflicting expert findings, or adversarial review where a wrong conclusion would materially affect the result. |

The coordinator should keep tiny tasks and immediate blockers local when
delegation would cost more than the work. Escalate a worker only when its
evidence reveals underestimated ambiguity, coupling, risk, or a missing success
oracle. Once uncertainty is resolved, use a lower profile for deterministic
follow-up work. Do not rerun a successful lane on a stronger profile merely for
reassurance.

Pass both `model` and `reasoning_effort` explicitly for every worker. Do not use
another pairing under this skill: Sol workers are limited to `medium`, `high`,
and `xhigh`, while Terra is limited to `max`. If the runtime does not expose an
exact model ID, report that instead of silently substituting another model.

## Delegation Pattern

1. Map the decision graph and keep the decisions that affect multiple lanes
   with the Sol xhigh coordinator.
2. Name the expensive-token risk: a large repository search, long logs, broad
   documentation, repetitive edits, or independent validation.
3. Split only genuinely independent work into bounded lanes, then classify and
   dispatch each lane with the rubric above.
4. Ask workers for concise evidence: files, line references, commands run,
   diffs, uncertainties, and stop conditions they hit.
5. Compare results at the coordinator layer, resolve conflicts, choose the
   implementation path, and review the integrated patch.

Prefer parallel subagents when the slices do not depend on each other. Keep
blocking or highly coupled work local.

## Handoff Packets

Write delegated prompts as if the subagent has no useful chat context. Include
only the context it needs:

- The repository or worktree path and exact objective.
- The files, packages, or surfaces in scope and anything explicitly out of
  scope.
- Whether the lane is read-only or may edit. Give writing agents exclusive
  ownership because Codex agents share the filesystem.
- State that the worker must not spawn descendants. The Sol xhigh coordinator
  owns the agent topology and may make an explicit exception for a named lane.
- State that the worker must not create, remove, or switch branches or
  worktrees, commit, push, or mutate remote services unless that exact action
  is part of its assigned lane.
- The evidence format to return: files, line references, commands, diffs,
  failures, screenshots, and uncertainty.
- The verification commands or browser flows to run, plus what success should
  look like when that is knowable.
- Stop conditions: if the code does not match the prompt, a command still fails
  after a reasonable retry, the task needs out-of-scope files or authorization,
  or the agent cannot support a claim, stop and report instead of improvising.

## Keep Implementations Minimal

Before planning or assigning a lane that will design, write, refactor, or fix
code, read and apply
[the Ponytail reference](references/ponytail.md). Require implementation
workers to read it too. Use its ladder in `full` mode by default: understand
the complete flow first, then stop at the first solution that already meets the
objective. This keeps broad orchestration from producing speculative helpers,
dependencies, files, and abstractions in every lane.

Ponytail governs implementation choice within the assigned scope. Its session
persistence, slash commands, intensity switching, and user-facing output
format are source-skill behavior and do not override this skill, repository
instructions, or the user's request. Never use minimalism to remove required
validation, security, accessibility, data-loss protection, or explicit
acceptance criteria.

## Codex Spawn Rules

Model overrides require a bounded context fork. Prefer `fork_turns: "none"`
with a self-contained handoff. Use a small positive turn count only when recent
conversation is essential, and do not use `fork_turns: "all"` with a model or
reasoning-effort override.

Use a unique, descriptive task name. Stay within the runtime's concurrency
limit, count the coordinator as one active slot, and batch later work when all
slots are occupied. Reuse an existing agent for a related follow-up instead of
spawning a replacement that must rebuild the same context.

The Sol xhigh coordinator owns the topology: only it spawns, redirects,
interrupts, or authorizes descendant agents. Check live agents before each
batch. Use a message to add context to a running worker and a follow-up task to
restart an idle worker on related work. Wait only when the coordinator has no
useful local integration or verification work, and do not finish while a
required worker is still running. Interrupt work that has become obsolete.

All agents share a working directory and filesystem. The coordinator owns
shared Git state and assigns exclusive write scopes. If isolation requires a
dedicated worktree, the coordinator creates or selects it first and gives the
worker its exact path; workers do not invent their own worktree layout.

Delegation does not expand the user's authorization. Preserve all limits on
scope, destructive work, credentials, external mutations, and communication.

## Adversarial Review Passes

For tasks that produce code or a structural diff, assign an independent
adversarial reviewer after a coherent, stable patch exists and before final
acceptance. Do not require this code-quality pass for research, prose, or
test-running lanes that did not change code. On long or high-risk work, repeat
the pass when the architecture changes materially or a major lane hands back
its result; do not spend review slots on every small intermediate edit. Run it
concurrently only when no active writer can touch the files under review.

Use `gpt-5.6-sol` with `high` by default and `xhigh` when the change is unusually
ambiguous, cross-cutting, or risky. Keep the reviewer read-only so it cannot
race with implementation agents. Give it the original objective, base ref,
current diff, scope boundaries, and verification evidence, then instruct it to
read the complete reference and follow
[the thermo-nuclear review reference](references/thermo-nuclear-code-quality-review.md).
The reference contains language encouraging ambitious restructuring; because
the reviewer is read-only, interpret that as a requirement to propose a
concrete restructuring, not permission to edit.

Require each finding to include severity, file and line evidence, the concrete
maintainability mechanism and consequence, a proposed structural remedy, and
confidence. The coordinator must verify each material finding, reject findings
that do not survive source inspection, assign accepted fixes to an agent with
exclusive file ownership, and run another adversarial pass only when the fixes
substantially changed the design.

## Vetting Delegated Work

Treat subagent reports as leads, not facts. Before using a high-impact finding,
opening a pull request, or telling the user the work is done, reopen the
important cited files, confirm the relevant line references or failures, and
review the final diff against the task. Let subagents gather signal; keep the
final judgment with the coordinating Codex agent.

## Common Scenarios

Treat these as soft defaults, not rigid rules:

- Research: ask subagents to scan documentation, prior art, APIs, and repository
  surfaces; the coordinator decides which evidence changes the plan.
- Coding: give subagents bounded edits or candidate patches; the coordinator
  owns shared-file coordination, integration, and final review.
- Testing: have the coordinator choose the validation direction and the scripts
  or browser checks that matter. Let subagents run targeted tests, browser
  flows, screenshots, and log reduction, then report exact commands, failures,
  likely causes, and whether failures look flaky, environmental, or real.
- Debugging: use subagents to cluster logs, reproduce issues, and try small
  fixes; the coordinator decides which diagnosis is most trustworthy.

If a task is tiny or the validation itself needs delicate judgment, keep it
with the coordinating Codex agent.
