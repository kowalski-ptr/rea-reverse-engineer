---
name: reap-12-software-protection-research
description: REA prompt pack: Software protection research (4 ready-to-paste prompts). Use for a documented security assessment of license checks, integrity checks, and runtime-protection mechanisms with REA (rea.tools) via an AI coding agent.
---

# REAP — 12. Software protection research

Part of REAP (REA Investigation Prompts). Replace everything in <angle brackets>; keep each request focused on one feature. Demand file/function/address evidence and separate facts from hypotheses.

Scope these at **your own machine, your own software, a vendor bug bounty,
a CTF, or academic study**. Never at other players' live game sessions. See
this project's authorized-use policy in `DISCLAIMER.md`. Every template
below produces one thing: a documented, evidence-backed assessment of how a
protection mechanism works, for its owner to evaluate and strengthen it.

### Map a protection system's detection surface

```text
Use REA to analyze <protection driver/service binary> on a lab machine I own.

Identify: what it enumerates (processes, modules, handles, callbacks), which
integrity checks it performs, what it hooks or registers with the kernel, and
what data it sends out (endpoints, intervals, payload fields).

For each finding cite the function/address. Separate "code present" from
"behavior observed". Do not run the protected target itself; static analysis first.
```

### Security-test my own product's protections

```text
I own <my application> and its protection scheme. Use REA to assess it the
way a security reviewer would: locate the license check, the integrity checks,
the anti-debug/anti-VM measures, and trace what a failed check does. Report which
checks are easy to defeat, which are load-bearing, and concrete fixes —
each claim backed by file/function/address evidence.
```

### User-mode protection component anatomy

```text
Use REA on <protection user-mode DLL/service> from a product I own, on a lab
machine, for a defensive security review. Map: what it enumerates at startup (loaded
modules, handle counts, window titles, device paths), which hooks it installs
(IAT/EAT, inline, syscall stubs), how it detects debuggers and memory
readers, and how it packages + ships detections (endpoint, payload fields,
hashing of module code sections). For each mechanism give the function/
address and say whether it is confirmed statically or needs runtime
observation. Do not attach to or interfere with a live production session.
```

### Licensing flow analysis (interoperability)

```text
Use REA on <application> to document its licensing flow: where entitlement is
validated, what local and remote checks exist, what state is stored on disk,
and how offline mode behaves. Goal is interoperability and security review of
software I legitimately own. Report facts with evidence; flag anything you
could not confirm.
```

---
