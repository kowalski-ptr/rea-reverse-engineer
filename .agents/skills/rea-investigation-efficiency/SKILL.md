---
name: rea-investigation-efficiency
description: Optimize REA providers and shared analysis workflows for agent context, latency, CPU, and memory while preserving correctness and Evidence. Use for analysis bottlenecks and cache or query design; CI/test-suite optimization belongs to rea-suite-integrity.
---

# REA investigation efficiency

Find a concrete source of unnecessary work, fix it at the owner that produces
it, and verify equivalent completed investigations before and after. A
measured decision to leave an implementation alone is also a useful outcome —
this skill is not a mandate to change something.

Follow the checkout's [performance priorities](../../../AGENTS.md#performance),
[tool design](../../../docs/tool-design.md), and
[testing guide](../../../docs/testing.md). It supplies a workflow for finding
and fixing bottlenecks, not additional permission to execute targets, publish,
or merge — use the current contracts, provider prerequisites, and whatever
the user has already authorized.

**Not every change needs this.** Reserve performance measurement for changes
that target performance or carry a concrete regression concern. Routine
cleanup — a renamed helper, a swapped import, a tidied representation — uses
ordinary correctness checks; it doesn't need a benchmark to justify itself.

Read [the Kuna case study](references/kuna-case-study.md) before choosing a
reuse strategy or interpreting a benchmark. Its pinned observations illustrate
tradeoffs made under specific conditions — not guarantees that hold for every
provider, and not a universal speedup to copy blindly.

## 1. Pin the question and the correctness oracle

Describe the analyst's completed task: the facts and relationships the answer
needs, the requested detail, source access, and which unknowns are expected to
stay unknown. Pick a representative real artifact plus a small or empty edge
case. Record the baseline revision, target digest, engine identity, options,
host, and entrypoint. Work in a separate checkout so the change doesn't
disturb anything else in flight.

Establish an oracle that is independent of the implementation you're about to
change: source-built fixture expectations, independently mapped file bytes, or
a native producer paired with a check of its own interpretation. Comparing two
wrappers around the same faulty decoder proves nothing. Preserve address
widths, same-address alternatives, unresolved endpoints, coverage, and source
linkage — an empty result only proves absence within the scope you actually
examined.

## 2. Locate the earliest unnecessary work

Trace one public CLI or MCP task through shared application code to its
producer. Measure the phases that matter: admission and identity checks,
native analysis, decoding, lookup, projection, serialization, Evidence
retention, and cleanup — including catalog discovery and any extra calls that
affect the completed task.

Let the measured evidence pick the smallest structural correction:

| Observed cost                     | Candidate correction                                       | Condition to establish                                                               |
| ---------------------------------- | ------------------------------------------------------------ | --------------------------------------------------------------------------------------- |
| Repeated scans of validated facts | Reuse an existing order or lookup structure                 | The owner establishes ordering and identity; every matching alternative survives     |
| Repeated equivalent engine work   | Share a validated result or an in-flight producer           | Inputs, freshness, lifetime, and per-caller outcomes stay explicit                   |
| Full reports for selected facts   | Push selection/projection to the earliest capable owner      | Required facts and evidence remain reachable; examined coverage is reported          |
| Large temporary transport buffers | Decode incrementally, or use bounded source reads           | The whole admitted document is still validated; retained facts/copies still cost memory |
| Many repeated homogeneous reads   | Share admission, decoding, or batch work                    | Item identity and required order are preserved; individual failures stay visible     |

Don't reach for an index, a generic cache, a batch endpoint, or an
orchestration layer without first naming the work it removes and weighing its
maintenance/resource cost against that. Projection applied after a full report
is already materialized can shrink the output while leaving analysis time and
peak memory untouched — that's not the fix. Fix missing or incorrect facts
*before* you benchmark an alternative composition against a workflow with
better coverage; a faster wrong answer is not progress.

## 3. Keep one interpretation and a defined reuse lifetime

Let the producer boundary validate and normalize facts exactly once.
Consumers query that representation through the shared CLI/MCP
implementation — adapters should never rebuild relationships or
reinterpret a raw report on their own. Keep lookup identity separate from
display formatting. Reuse a contract whenever its intent already matches,
honoring the requested detail level and complete Evidence access.

For every cache or shared producer you introduce, state its key, owner,
invalidation rule, retained size, and lifetime. Account for anything that
affects reuse validity: result-affecting target bytes, engine contents,
analysis options, architecture/load mapping, and parser/schema versions.
Validate live state before treating a cached observation as current. Cache a
*refusal* only when it's deterministic for that validated key — malformed,
partial, transient, or cancelled attempts each need their own explicit retry
policy, not a shared one.

One cancelled waiter must never invalidate a successful peer. Preserve
producer failure context and each caller's own cancellation reason. Hold
ownership and capacity charges until shared work and cleanup actually settle,
and preserve completed observations even when cleanup fails. When any of
these paths change, exercise invalidation, concurrent consumers, last-waiter
cancellation, close, and cleanup retry — don't assume the happy path covers
them.

Bound the process family and retained state, not just a parent heap or worker
count. Account for decoding and serialization expansion *before* allocation.
Choose CPU, memory, concurrency, and disk limits from the host and the actual
workload; stop before OOM and verify cleanup ran. A guard-stopped task is
incomplete, not merely slow. Never trade a transport buffer cap for an
unbounded retained inventory, and never hide a partial result behind a
success status.

## 4. Compare completed tasks, and say only what you measured

Run the affected real public workflow before and after, with equivalent
inputs, results, coverage, and Evidence semantics. Include small inputs so
setup costs and regressions stay visible. Distinguish a fresh session from a
warm runtime cache and from the OS file cache. Alternate or repeat runs when
variance would change the decision. Where the real engine can't reliably
reproduce a case, use a meaningful boundary regression instead — a fake
engine never qualifies a real provider's behavior.

Report the task outcome, the relevant phase timings, total elapsed and CPU
time, peak process-family memory (and how you accounted for it), any resource
stops, and cleanup. Check the payload's exit status, not just the profiler's.
Keep failed attempts in the record and name the costs you didn't measure — a
passing retry doesn't explain away a prior failure.

For token claims, count schemas/discovery, arguments, replies, retries, and
every continuation the task needed to finish. Label tokenizer estimates
separately from actual model usage, and record the client/catalog behavior
behind them. A smaller JSON payload proves neither fewer model tokens nor a
better investigation — and "this is more complex software" is not a claim
you get to make without completed representative investigations and
comparable correctness/coverage to back it.

## Close the loop

Finish with: the bottleneck, the owning change, the semantics it preserved,
the checks that ran, before/after measurements, and the limits that remain.
Keep any integration record current with its tested revision, and keep
merged production work distinct from experiment-only code. Stop once the
demonstrated bottleneck is resolved, or once further benefit is speculative
rather than measured — don't keep optimizing past the evidence.
