---
name: rea-suite-integrity
description: Audit REA CI and test suites for correctness, fixture drift, false passes, and measured performance; use for suite or workflow investigations and efficiency changes, rather than ordinary feature testing or a single test failure.
---

# REA suite integrity

A green suite only means something if it would turn red for the right
reasons. This skill is for establishing that — which checks still catch real
regressions, which costs can be cut without losing that protection — not for
chasing a single failing test or writing ordinary feature coverage.

The outcome of an audit can be a verified fix, an evidence-backed
recommendation, or an explicit, justified decision to stop. All three count.

Follow the checkout's `AGENTS.md`, [testing guide](../../../docs/testing.md),
and [contribution rules](../../../CONTRIBUTING.md) — use their *current*
commands and pinned toolchain, not whatever you remember from a previous
audit. Investigation alone doesn't authorize edits: this skill doesn't grant
permission to publish issues, push, or merge beyond whatever the user has
already authorized. Keep audit notes and raw results outside the tracked
tree, normally in a task-specific `/tmp` directory.

## 1. Establish the executed baseline

Fetch when the user asks for latest `main`. Pin the baseline SHA and the
inspected workflow revision; use an isolated worktree when changing or
comparing code, and never touch another task's checkout. Record dirty
changes and the exact command, toolchain, host, selected tests,
instrumentation, and prerequisites you ran with. An old run remains evidence
for the revision it ran against, even once `main` has moved on.

Trace workflow triggers, changed-path selection, job dependencies, reusable
actions, package scripts, test discovery, artifact producers, and report
gates all the way through to the commands that actually execute — including
scripts, generated data, runtime registration, and subprocess boundaries that
an import graph alone won't show you. Check both directions of selection:
required files must be discovered once per intended test configuration, and
a deliberate platform/configuration repeat is separate coverage, not
duplication. A missing explicit test, or a missing required report, must not
be able to turn the suite green. Optional lanes may skip; preserve whatever
prerequisites they state.

Use existing timing and failure artifacts to pick the suspicious families
first. Run the relevant checks to get a real baseline; reserve a full local
suite run for broad CI diagnosis or for uncertainty a focused check can't
resolve. Don't rebuild or rerun an unchanged complete suite just because a
documentation file moved.

## 2. Prove the failure — or prove the protection

For each candidate, state the expected observable behavior and who owns it,
then build the smallest reproduction against real production behavior.
Follow the producer and its affected consumers before deciding where to fix
anything.

- **Fixture drift** — compare fixtures against current admission
  requirements, protocol replies, named contracts, tool versions, and actual
  public entrypoints. A plausible-looking mock can fail before the behavior
  its test claims to cover ever runs. Search the whole affected fixture
  family, including sibling version strings. Keep intentionally malformed
  fixtures and verify they're still rejected the way they're supposed to be.
- **A suspiciously green test** — perturb the relevant behavior in an
  isolated copy, or inject a controlled failure, and confirm the existing
  assertion or gate actually catches it. A test can quietly repeat its own
  mock, exercise abandoned code, or swallow a subprocess/checker failure
  without anyone noticing. Restore your mutation once the experiment is
  done.
- **Shared setup or pruning** — map each distinct assertion to the scenario
  it retains. Keep malformed inputs, cancellation, cleanup failure,
  permissions, capacity, identity, and meaningful concurrency interactions
  intact. A faster run that drops a case or weakens its oracle is a
  regression in disguise, not an improvement.
- **Provider coverage** — keep fake-producer, boundary, and actual-engine
  evidence clearly separate. Inspect named skips and prerequisites: a
  fixture never establishes real Ghidra, LLDB, browser, or other live
  provider behavior, no matter how convincing it looks.

Classify every finding as a reproduced defect, a supported cost, a
hypothesis, or evidence you don't yet have. Record the baseline command, the
observed output and exit status, the expected result, the affected scope,
and any competing explanation. A failure already present on `main` may be
pre-existing — that doesn't make it irrelevant to this audit. Attribute a PR
failure using the revision that actually ran plus a clean baseline, not just
its resemblance to a previous failure. Retry only to test one specific
transient explanation, keep the original failure on record, and stop
retrying once repetition stops adding evidence — a passing retry never
establishes a root cause or a fix on its own.

## 3. Assess efficiency without losing the gate

Read [measurement and CI checks](references/measurement-and-ci.md) before
investigating slowness, caching, runner waits, shared artifacts, or log
noise. Compare equivalent completed work before claiming any speedup, and
keep setup/build time, test execution, coverage instrumentation, dependency
waits, and runner scheduling distinct from one another — say plainly which
costs stayed unmeasured.

Prefer removing proven duplicate preparation, reusing immutable setup, and
cutting repeated subprocess work *before* you touch correctness coverage.
Keep independent sessions wherever lifecycle or concurrency is the actual
oracle. Treat live tests as perishable: a cached previous pass can't vouch
for the current host, installed provider contents, permissions, or external
state.

## 4. Fix, verify, and hand off within scope

Where implementation is authorized, make the smallest structural change at
the owning boundary and add a regression that fails on the baseline for the
right reason. Retest affected consumers and the meaningful negative paths;
verify cache invalidation or discovery wherever those mechanisms change.
Read the installed Turbo documentation before touching its configuration or
commands — don't assume last year's flags still apply.

Run the repository's applicable checks and inspect the complete current-head
CI results before any authorized merge. Preserve required coverage and
discovery gates. Never suppress a failure, bolt on a blanket retry, raise a
timeout, or delete a distinct assertion just to turn CI green — any change
to these controls needs its own demonstrated reason and must keep its
failure detection intact.

Report each confirmed issue with its exact revision, reproduction,
consequence, proposed correction, and validation limits. Report performance
with the workload and the actual measurements, never just a percentage. Use
English issue/PR records when requested, and follow the user's stated
assignment, commit identity, review, and merge preferences rather than
assuming them on someone else's behalf. Open ready-for-review PRs only when
that lifecycle is authorized. Resolve the necessary review findings, and
check final `main` against the tested tree once it's merged.

Stop once the authorized work is verified, the remaining benefits are
unproven, or a further change would trade correctness for speed that's still
speculative. List genuine uncertainties plainly instead of turning them into
another optimization round.
