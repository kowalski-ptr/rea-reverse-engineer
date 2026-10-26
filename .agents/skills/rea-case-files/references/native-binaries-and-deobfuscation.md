# Case file — native binaries & deobfuscation

From a first look at an unfamiliar executable through control-flow
flattening, struct recovery, and reconciling two builds of the same
program. REA opens the target with `open_binary`, decompiles through
whatever provider is attached (Ghidra, IDA, Hopper, or Kuna), and keeps
every claim tied to an address — see
[references/native-and-artifacts.md](../../reverse-engineer-anything/references/native-and-artifacts.md)
in the main skill for the underlying tool contracts.

### First pass on an executable

```text
Open <PATH TO EXECUTABLE> with REA. Identify format, architecture, runtime,
linked libraries, and candidate entry points; pull interesting strings and
state which analysis provider is attached and what it can't see yet.

Map the main components and explain each one's responsibility with
evidence. Start with static analysis — do not run the binary.
```

### Function deep-dive

```text
Pick the function in <binary> that handles <operation> and produce: its
decompiled pseudocode, callers and callees, the data structures it reads or
writes, and every string or global it touches. Annotate each claim with the
address range that proves it.
```

### Triage map: imports, strings, capability buckets

```text
Produce a triage map for <binary>: file format and architecture, section
layout, linked libraries and imports grouped by capability (network,
filesystem, crypto, process/thread, registry/IPC), interesting strings with
addresses, and the strongest candidate entry points for <FEATURE>. Flag any
sign of packing, obfuscation, or control-flow flattening you notice along
the way.
```

### File-format or wire-protocol extraction

```text
Work out how <program> reads and writes <file type OR its network/IPC
traffic for FEATURE>. Describe the structure field by field, show exactly
where each field is parsed (function + address), and produce either a small
reader that prints a sample file, or a struct/protobuf definition plus one
example message built from the code's own constants.
```

### Control-flow flattening and opaque-predicate cleanup

```text
The function(s) handling <FEATURE> in <binary> look control-flow-flattened
(a dispatcher variable plus a giant switch/branch chain) or full of opaque
predicates. Identify the dispatcher/state variable, map each state to its
real block, and reconstruct the logical flow as pseudocode or a graph. For
any opaque predicate, show why it's provably constant and which branch is
real. Deliver a cleaned control-flow view with the removed noise still
traceable back to its evidence, and note what remains ambiguous.
```

### String/constant decryption and struct recovery

```text
Locate the string- or constant-decryption routine(s) in <binary> (per-call,
single init loop, or lazy), recover the algorithm and keys with addresses,
and produce a decrypted table: offset, encoded form, decoded form, and the
functions that reference it. Separately, pick the structure that
<function> operates on and reconstruct it field by field from access
offsets and load/store widths — emit a header with recovered field names,
each citing the address that proves it.
```

### Calling-convention and stack-frame correction

```text
The decompiler mis-models <function/address range> in <binary>.
Reconstruct the real frame — prologue/epilogue, saved registers, local
slots, stack-canary handling, and the argument registers/stack slots at
entry — and determine the calling convention from how callers pass
arguments and how the callee cleans the stack. Rewrite the signature,
re-decompile with the corrected frame, and show the instruction evidence
for every frame slot.
```

### Matching functions across two builds

```text
Match functions between <binary v1> and <binary v2> of the same program
using `match_native_functions` or `compare_functions` if available, backed
by structural similarity (CFG shape, instruction counts, call graph,
string/global references) rather than raw hashes, so compiler reordering
doesn't break the mapping. Produce a matched-pairs table with confidence,
then list the functions that changed the most and what the change looks
like (added check, new branch, refactored loop), with addresses on both
sides.
```

### Go binaries: package map and logic recovery

```text
Open <Go binary> with REA and, where useful, ground it with
`inspect_go_binary` for build metadata. Recover the package layout from
pclntab/moduledata — every function symbol with its package path, the Go
version/build ID, and embedded dependencies — then, for <FEATURE>, produce
readable pseudocode that accounts for Go's calling convention, defer/panic
handling, goroutine/channel sites, and interface dispatch. Cite address
ranges per recovered statement group and separate confirmed logic from
inference. Say plainly that this recovers structure and logic, not the
original `.go` files.
```

### Rust binaries: crate map and trait-aware logic recovery

```text
Open <Rust binary> with REA and, where useful, ground it with
`inspect_rust_binary` for its cargo-auditable dependency metadata. Identify
the Rust version/profile from panic strings and embedded metadata, map
monomorphized generics back to their generic parents, and for <FEATURE>
produce pseudocode that accounts for Result/Option propagation, drop glue,
vtable-based trait dispatch (name each vtable slot), and inlined iterator
state machines. Reconstruct signatures from surviving mangled symbols, or
from register usage where stripped, and cite addresses per claim.
```

### Binary-to-source fidelity report

```text
Based on the REA findings for <binary>, write a RECOVERY_REPORT.md that
classifies each recovered module as symbol-exact (names from metadata),
structure-exact (types/fields proven by access offsets), or behavior-only
(pseudocode with inferred names) — never as the original source. Include
the reconstructed layout, a confidence level per file, and the evidence
addresses behind each classification.
```
