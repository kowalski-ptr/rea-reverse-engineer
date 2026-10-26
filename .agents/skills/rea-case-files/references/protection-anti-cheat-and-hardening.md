# Case file — protection, anti-cheat & hardening research

Anti-cheat detection surfaces, DMA/hardware-assisted cheat research,
commercial protectors/obfuscators, and license/crackme analysis share one
shape: a check, a trigger, and a response. These templates ask the agent to
**identify, characterize, and document** that shape — not to produce a
working bypass of someone else's software, and never to touch another
player's live session.

Scope every template here to: your own machine and your own software, a
vendor bug bounty, a CTF or crackme, academic study, or incident-response
work you're authorized to do. Reading another machine's memory over PCIe
without authorization is illegal in most jurisdictions, and these prompts
are not a guide to pirating anyone's product.

### Map an anti-cheat's detection surface

```text
Analyze <anti-cheat driver/service binary> on a lab machine I own. Identify
what it enumerates (processes, modules, handles, callbacks), what integrity
checks it performs, what it hooks or registers with the kernel, and what
data it sends out (endpoints, intervals, payload fields). Cite the
function/address behind each finding, and separate "code present" from
"behavior observed." Do not run the target game — static analysis first.
```

### Stress-test my own product's protections

```text
I own <my application> and its protection scheme. Assess it as an attacker
would: locate the license check, the integrity checks, the anti-debug/anti-VM
tricks, and trace what a failed check actually does. Report which checks
are trivially bypassable, which are load-bearing, and concrete fixes — each
claim backed by file/function/address evidence.
```

### DMA memory access: how the platform defends against it

```text
In my lab, explain how <anti-cheat driver / DMA-protection firmware, e.g.
Thunderbolt security or IOMMU/VT-d config> restricts external PCIe memory
reads: IOMMU mappings, Thunderbolt ACLs, kernel DMA protection. Then
explain what the anti-cheat enumerates to spot a DMA device (PCIe topology,
BAR regions, unknown root ports, timing anomalies), and which of those
checks are load-bearing versus cosmetic — with function/address evidence
for each.
```

### Detection-gap analysis for my own product

```text
Evaluate how <my application/my anti-cheat component> would react to
external physical memory access: which integrity checks exist, whether
sensitive values sit in predictable memory, and what side-channel
validation the server performs. Propose concrete hardening — value
obfuscation, server-side validation, memory-layout randomization — mapped
to the exact code locations that need each change.
```

### Protector fingerprinting

```text
Determine whether <binary> is protected/packed and by what: UPX, VMProtect/
Themida (virtualized functions, handler tables), Arxan-style watermarking,
.NET obfuscators, or mobile hardening (Appdome/DexGuard-style). Report
which protector(s), the version indicators you can see, which sections are
original versus synthetic, and which code regions are protected versus
plain — with section headers, addresses, and string fingerprints behind
each conclusion.
```

### VM-protection region mapping (own software or CTF)

```text
Map the protection surface of <binary I own or a crackme> without trying to
defeat it: the VM entry stubs, the context structure, the handler dispatch
table, and which functions are virtualized versus native. For virtualized
regions, estimate the opcode-set size and identify handlers you can already
recognize (memory read/write, arithmetic, branch). Deliver a coverage map —
function → protected or plain → confidence — with evidence. This map feeds
a later deobfuscation plan; it is not itself a bypass.
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

### Keygen-style analysis — document, don't bypass

```text
Find the validation routine for <license key/serial> in <binary>. Decompile
it, identify the input encoding, the checksum/transform steps, and the
success/failure branches with addresses. Write a test harness that
exercises the recovered algorithm against sample inputs. Do not patch or
bypass anything — I want the algorithm documented, not the check removed.
```

### Crackme / CTF solving

```text
Solve this crackme: <path>. Enumerate entry points, strings, imports, and
any packer/protection indicators. Identify the flag-checking logic,
decompile it, and derive the correct input from the recovered algorithm.
Show the full evidence chain: address → instruction → conclusion.
```

### Protection hardening assessment for my own product

```text
I own <my application> and its protection stack (<protector names>). Assess
it the way an attacker would measure it: coverage (what fraction of
license-critical and security-critical code is actually protected vs.
plain), which layered checks fail silently versus loudly, the weakest
protected path to <the feature being protected>, and anti-analysis depth.
Deliver a risk-ranked report: each weakness with function/address evidence,
the protection feature that should have covered it, and a concrete
hardening recommendation. Document weaknesses — do not produce a working
bypass.
```
