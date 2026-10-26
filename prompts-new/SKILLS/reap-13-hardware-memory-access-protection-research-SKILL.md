---
name: reap-13-hardware-memory-access-protection-research
description: REA prompt pack: Hardware memory-access protection research (4 ready-to-paste prompts). Use for a defensive study of how a platform or product detects external physical-memory access, with REA (rea.tools) via an AI coding agent.
---

# REAP — 13. Hardware memory-access protection research

Part of REAP (REA Investigation Prompts). Replace everything in <angle brackets>; keep each request focused on one feature. Demand file/function/address evidence and separate facts from hypotheses.

External physical-memory access is a real platform-security problem. Use
these to **study how detection works** and to **harden your own
product/lab** — on machines you own, offline targets, or with vendor
authorization. See this project's authorized-use policy in
`DISCLAIMER.md`. Reading another machine's memory without authorization is
illegal in most jurisdictions.

### How hardware memory-access protection works on a target platform

```text
Use REA on <protection driver / memory-access-protection firmware (e.g. Thunderbolt
security, IOMMU/VT-d config)> in my lab.

Explain, with function/address evidence: how the platform restricts external
PCIe memory reads (IOMMU mappings, Thunderbolt ACLs, kernel DMA protection),
what the protection enumerates to spot an external memory-access device (PCIe
topology, BAR regions, unknown root ports, timing anomalies), and which checks are
load-bearing versus cosmetic.
```

### Analyze a memory-access toolchain sample (for defensive research)

```text
I have a sample <PCIe/USB memory-access firmware or driver> for
defensive research. Use REA to document: how it opens physical memory
(PCIe BAR mapping, page-table traversal techniques), how it locates a target
process, what it reads/writes, and any detection-avoidance tricks (pattern
scanning avoidance, timing padding). Give the function chain with addresses
for each stage. Do not run it against any live production system — analysis only.
```

### Detection-gap analysis for my own product

```text
Use REA to evaluate how <my application/my protection component> would react
to external physical memory access: which integrity checks exist, whether
sensitive values sit in predictable memory, what side-channel validation the
server performs. Propose concrete hardening (value obfuscation, server-side
validation, memory layout randomization) mapped to the code locations that
need each change.
```

### FPGA/microcontroller device firmware study

```text
Use REA on <device firmware dump> from a PCIe/USB device I own. Identify the
MCU/FPGA soft-core, the protocol it speaks to the host, the memory-read
primitives it implements, and how it hides from host enumeration. Show
evidence per claim and label anything that needs runtime confirmation.
```

---
