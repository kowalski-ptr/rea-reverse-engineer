# Case file — cloning & reconstruction

> **Scope:** your own software, a CTF/crackme, or a target you're otherwise
> authorized to study — see [DISCLAIMER.md](../../../../DISCLAIMER.md).

This is the flagship pattern for learning by building: study how a piece of
software works, write down what you learned as a specification, then build
your own implementation from that specification — for your own private use,
for interoperability, or simply to learn. Clean-room methodology, start to
finish: extract *behavior and interfaces* from a target, never its code,
assets, branding, or licensing mechanism. Use this for interoperability
work, feature parity, and research — and
pair the "validate" template with `verify_reconstruction` whenever it's
available, so equivalence is demonstrated rather than assumed.

### Full application clone (behavioral)

```text
Study <application> as a black box for a clean-room clone. Produce
CLONE_SPEC.md covering every user-visible feature, screen, and state
machine; inputs, outputs, and edge cases; file formats and network
protocols it uses; settings and persistence layout; and error behaviors.
For each requirement, cite the evidence (function, address, string,
captured request), and mark anything unconfirmed as UNKNOWN. Do not copy
code or assets — I will implement from the spec alone.
```

### Clone one feature into my own app

```text
Recover the exact behavior of <FEATURE> in <application>: trigger, inputs,
processing steps, outputs, side effects, and failure modes. Then implement
<FEATURE> from scratch in <MY LANGUAGE/PROJECT> matching that behavior,
with tests derived from the recovered rules. List every observable
difference remaining between my version and the original.
```

### Validating a clone against the original

```text
Compare <MY IMPLEMENTATION> against the observed behavior of <ORIGINAL
APPLICATION>, using `verify_reconstruction` if available. Check results,
errors, limits, and state transitions. Present passing cases, divergences,
and behaviors not yet tested — a handful of passing examples does not
establish equivalence.
```

### UI/UX behavior cloning without assets

```text
Document <application>'s interaction model: layout logic, keyboard
shortcuts, command dispatch, undo/redo semantics, and clipboard formats.
Recreate the behavior in <my framework> using only my own visual assets,
matching the documented semantics. Show which original function proves
each documented rule.
```

### Full protocol reimplementation for interoperability

```text
Recover <application>'s <network protocol / file format> completely enough
to interoperate: every message/record type, field layouts, endianness,
state-machine transitions, handshake/auth sequence, error codes, and
keep-alive behavior. Deliver a machine-readable spec (JSON Schema or
protobuf), a working client/server in <language> that speaks the recovered
protocol against the original, and a test log proving each message type
round-trips. Cite the marshal/unmarshal functions with addresses for every
field, and mark anything untested as UNKNOWN.
```

### Algorithm extraction to a standalone library

```text
Isolate the pure algorithm behind <feature> in <application> — its math,
tables, constants, and edge-case handling — from all UI, I/O, and platform
code. Reimplement it from scratch in <language> as a library whose public
API matches the observed behavior, then run a differential test: same
inputs through the original and the clone, comparing outputs across
normal, boundary, and malformed cases. Report the match rate and every
divergence, not just the ones that line up.
```

### State-machine / business-logic reconstruction

```text
Document the state machine governing <workflow> in <application>:
enumerate states (from enums, flags, or dispatch tables with addresses),
transitions, guards, and side effects per transition. Render it as a
diagram plus a table, implement the same machine in <my project>, and write
tests that replay every transition you documented.
```

### Plugin/extension ABI reconstruction

```text
Recover <host application>'s plugin interface: the exported functions a
plugin must provide, the callback table the host passes in, the struct
versions, and how plugins are discovered and loaded. Build a minimal
working plugin for my own feature using only the recovered ABI, and
document each ABI element with the address that proves it.
```

### Behavioral-equivalence harness

```text
Build a differential-testing harness between <MY CLONE> and <ORIGINAL
APPLICATION>: derive the observable-behavior matrix from the already-written
CLONE_SPEC.md (inputs, outputs, errors, state transitions, file formats),
run both sides against the same corpus, and emit a divergence report
covering every spec item as PASS / FAIL / NOT-TESTED — not a verdict drawn
from spot checks.
```
