---
name: reap-14-license-validation-research
description: REA prompt pack: License-validation and protection research (3 ready-to-paste prompts). Use to document how a license/validation scheme works, for crackmes/CTFs or hardening your own software, with REA (rea.tools) via an AI coding agent.
---

# REAP — 14. License-validation research

Part of REAP (REA Investigation Prompts). Replace everything in <angle brackets>; keep each request focused on one feature. Demand file/function/address evidence and separate facts from hypotheses.

For studying protection schemes, crackmes/CTFs, and hardening your own
software. See this project's authorized-use policy in `DISCLAIMER.md`. Not
a guide to pirate other people's products.

### License-validation algorithm study (document the algorithm, not a removal)

```text
Use REA on <binary> to find the validation routine for <license key / serial>.
Recover the algorithm (decompile it), identify the input encoding, the
checksum/transform steps and the success/failure branches with addresses.
Then write a test harness that exercises the recovered algorithm against
sample inputs, as a way to document and understand how the scheme works.
The deliverable is the documented algorithm, not a patched or disabled check.
```

### Crackme / CTF challenge

```text
Use REA on this crackme: <path>. Enumerate entry points, strings, imports and
any packer/protection indicators. Identify the flag-checking logic, decompile
it, and derive the correct input from the recovered algorithm. Show the full
evidence chain: address → instruction → conclusion.
```

### Protection-change diffing between versions

```text
Use REA to compare <protected app v1> and <v2>. Identify what changed in the
protection layer: new integrity checks, new anti-debug measures, changed
validation paths. Map each change to functions/addresses and explain the
hardening intent.
```

---
