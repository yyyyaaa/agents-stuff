---
name: test-suite-audit
description: "Audit a repository's test suite and delete low-signal unit tests that restate the implementation, never fail, or break on every refactor, keeping only tests that catch real bugs that E2E or integration tests would miss, then add test-writing rules to AGENTS.md. Use when the user asks to prune, trim, clean up, or audit tests, remove junk or low-value tests, shrink an agent-bloated suite, or cut tests while holding coverage. Not for writing new tests, fixing failing tests, or adding coverage."
---

# Test suite audit

This repo is full of low-signal unit tests. Modern agents write tests that restate the implementation, always pass, break on every refactor, and catch almost nothing our stronger tests don't already catch.

Delete every unit test that would not catch a real bug an end-to-end or integration test would miss. Prefer a smaller suite that protects behavior over a large suite that protects coverage numbers.

## Keep a test only if all of these are true

- It protects an observable behavior, invariant, or public contract.
- There is a credible regression that would make it fail.
- Existing E2E or integration coverage does not already catch that failure.
- It would survive a behavior-preserving refactor. If it breaks only because an internal name, call shape, or mock changed, delete it.

## Delete these on sight

- Tests that restate the code they were written after
- Assertion-free coverage probes
- Source, import, or string greps
- Mocks that implement the behavior being asserted
- Expected values produced by the code under test
- Tests whose only purpose is to keep a test-only export, wrapper, or seam alive
- Near-duplicates of a stronger test at a higher boundary

## How to work

Do not stop early. Target removing at least 20% of the least useful tests while keeping coverage within 2% of current, or explain why that is impossible. Measure coverage before you start so you have a baseline to compare against.

Fan the audit across parallel subagents if you can. Read each candidate and its production owner before deleting it.

Do not delete a failing test to make the suite green. If a retained test fails, treat that as a possible product bug.

## Add rules to AGENTS.md

After the deletions, add these rules to AGENTS.md so future agents stop writing junk tests:

- Never write unit tests after you write code.
- Highly prefer E2E or integration tests as the sole testing mechanism. Use them to verify complex features work. At the end of those tests, produce a verifiable and repeatable artifact.
- If you must test a system in isolation, first write down all the ways it could fail, then write the code.
- A test that breaks under a behavior-preserving refactor is asserting implementation, not behavior. Do not add it.
- Never delete or weaken a failing test to make the suite pass. Fix the code, or ask.

## Report

When you finish, report:

- Tests removed
- Tests kept and why
- Production lines deleted with them
- Coverage before and after
- Anything you were unsure about
