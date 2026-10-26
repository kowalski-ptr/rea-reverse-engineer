# Case file — software protection assessment

> **Scope:** your own software or hardware, a CTF/crackme, a vendor-authorized
> bug bounty, or incident-response work you're permitted to do. This
> project's authorized-use policy ([DISCLAIMER.md](../../../../DISCLAIMER.md))
> applies to everything below. Never against a system, account, or session
> you don't own or weren't given explicit permission to test.

A license check, a copy-protection scheme, a platform integrity check, and a
hardware memory-access guard all have the same shape: a check, a trigger,
and a response. Studying that shape — for your own product, a bug bounty, or
a CTF — teaches you how to build and harden protection mechanisms yourself.
Every template here produces one artifact: a **documented, evidence-backed
assessment** of how a mechanism is built, for its owner to evaluate and
strengthen it.

### Map a protection system's detection surface

```text
Analyze <protection driver/service binary> on a lab machine I own. Identify
what it enumerates (processes, modules, handles, callbacks), what integrity
checks it performs, what it hooks or registers with the kernel, and what
data it sends out (endpoints, intervals, payload fields). Cite the
function/address behind each finding, and separate "code present" from
"behavior observed." Do not run the protected target itself — static
analysis first.
```

### Security-test my own product's protections

```text
I own <my application> and its protection scheme. Assess it the way a
security reviewer would: locate the license check, the integrity checks,
the anti-debug/anti-VM measures, and trace what a failed check actually
does. Report which checks are easy to defeat, which are load-bearing, and
concrete fixes — each claim backed by file/function/address evidence.
```

### Hardware memory-access protection: how the platform defends against it

```text
In my lab, explain how <protection driver / memory-access-protection
firmware, e.g. Thunderbolt security or IOMMU/VT-d config> restricts
external PCIe memory reads: IOMMU mappings, Thunderbolt ACLs, kernel DMA
protection. Then explain what the protection enumerates to spot an external
memory-access device (PCIe topology, BAR regions, unknown root ports,
timing anomalies), and which of those checks are load-bearing versus
cosmetic — with function/address evidence for each.
```

### Detection-gap analysis for my own product

```text
Evaluate how <my application/my protection component> would react to
external physical memory access: which integrity checks exist, whether
sensitive values sit in predictable memory, and what side-channel
validation the server performs. Propose concrete hardening — value
obfuscation, server-side validation, memory-layout randomization — mapped
to the exact code locations that need each change.
```

### Protector/obfuscator fingerprinting

```text
Determine whether <binary> is protected/packed and by what: UPX, VMProtect/
Themida (virtualized functions, handler tables), Arxan-style watermarking,
.NET obfuscators, or mobile hardening (Appdome/DexGuard-style). Report
which protector(s), the version indicators you can see, which sections are
original versus synthetic, and which code regions are protected versus
plain — with section headers, addresses, and string fingerprints behind
each conclusion.
```

### Mapping a virtualized protection region (own software or CTF)

```text
Map the protection surface of <binary I own or a crackme>: the VM entry
stubs, the context structure, the handler dispatch table, and which
functions are virtualized versus native. For virtualized regions, estimate
the opcode-set size and identify handlers you can already recognize (memory
read/write, arithmetic, branch). Deliver a coverage map — function →
protected or plain → confidence — with evidence. This map is the input for
a deobfuscation study, not an attempt to defeat the protection.
```

### Anti-debug / anti-VM / anti-tamper inventory

```text
Inventory <binary>'s self-protection checks: anti-debug, anti-VM, integrity
checks (self-hashing, CRC tables), and tamper responses (silent flag vs.
crash vs. report-to-server). For each: the function/address, what triggers
it, and what happens on failure. For software I own, state which checks
are load-bearing for security and which are cosmetic, with recommended
fixes.
```

### License-validation algorithm study (document the algorithm, not a removal)

```text
Find the validation routine for <license key/serial> in <binary>. Decompile
it, identify the input encoding, the checksum/transform steps, and the
success/failure branches with addresses. Write a test harness that
exercises the recovered algorithm against sample inputs, as a way to
understand and document how the scheme works. The deliverable is the
documented algorithm, not a patched or disabled check.
```

### Crackme / CTF challenge

```text
Solve this crackme: <path>. Enumerate entry points, strings, imports, and
any packer/protection indicators. Identify the flag-checking logic,
decompile it, and derive the correct input from the recovered algorithm.
Show the full evidence chain: address → instruction → conclusion.
```

### Protection coverage assessment for my own product

```text
I own <my application> and its protection stack (<protector names>). Assess
its real-world coverage: what fraction of license-critical and
security-critical code is actually protected versus plain, which layered
checks fail silently versus loudly, the weakest protected path to <the
feature being protected>, and anti-analysis depth. Deliver a risk-ranked
report: each weakness with function/address evidence, the protection
feature that should have covered it, and a concrete hardening
recommendation. The deliverable is a documented weakness report, not a
working circumvention.
```
