# Case file — incident-response sample triage

> **Scope:** your own software or hardware, a CTF/crackme, a vendor-authorized
> bug bounty, or incident-response work you're permitted to do. This
> project's authorized-use policy ([DISCLAIMER.md](../../../../DISCLAIMER.md))
> applies to everything below. Never against a system, account, or session
> you don't own or weren't given explicit permission to test.

Static-first by design: every template here asks for runtime observation
only inside an isolated VM, and several explicitly forbid execution. Scope
this case file to authorized work — your own incident response, a
sandboxed lab, threat-intel analysis, or a sample you have explicit
permission to handle. Never run a sample on a production machine.

### Static triage and capability map

```text
Analyze this sample with REA: <PATH> (collected for authorized incident
response / threat-intel analysis). Produce a triage report: file type,
compiler/packer indicators, first-run artifacts, imports grouped by
capability (persistence, credential theft, keylogging, screen capture,
network command-and-control, process injection, defense evasion),
suspicious strings with addresses, and any embedded configs (URLs, wallet
addresses, mutexes, encryption keys). Cite the function/address behind
every capability claim, and separate confirmed static findings from
behaviors that still need sandbox confirmation. Do not execute the sample
in this step.
```

### Command-and-control protocol and config extraction

```text
Recover this sample's command-and-control protocol: the endpoint
construction (hardcoded strings, DGA, XOR/base64 buffers — show the decode
routine and its key), the beacon format, the tasking commands it accepts
and what each does, and any fallback infrastructure. Output a written
protocol spec, a Python parser for a captured beacon if one exists, and
the addresses proving each field. Label anything inferred as inferred.
```

### Persistence, injection, and evasion inventory

```text
Enumerate every persistence mechanism in this sample (registry Run keys,
scheduled tasks, services, startup folder, WMI subscriptions), every
process-injection technique (CreateRemoteThread, APC, process hollowing,
DLL side-loading), and every evasion trick (anti-VM, anti-debug,
sandbox-timing checks, sleep evasion, AMSI/ETW patching). For each:
function/address, the exact API calls used, and the trigger condition —
ranked by what runs first in the execution flow.
```

### Detection-rule generation from recovered code

```text
From the functions already located in this sample, generate detection
artifacts: YARA rules built on stable code sequences (not obfuscated
strings), a list of file/registry/network indicators with the addresses
where each appears, and a threat-hunt query outline. Explain why each
pattern is family-stable rather than sample-specific.
```

### Family correlation between samples

```text
Compare <sample A> and <sample B>: matching functions by structure (not
just hash), common string-encoding routines, shared protocol fields, and
reused constants or PDB paths. Conclude same family, same builder, or
unrelated — backed by the function-pair evidence behind each claim.
```

### Loader stage-chain reconstruction

```text
Reconstruct the stage chain in this loader sample: what the first-stage
stub does (process hollowing, DLL side-loading, reflective loading, APC
injection — show the API sequence with addresses), where the second-stage
payload comes from (embedded blob offset, download URL, decode routine),
and how the payload's integrity/decryption is verified before execution
transfers to it. Deliver a stage diagram: stage → mechanism → artifact →
evidence address. Do not execute the sample.
```

### Sandbox-evasion decision tree

```text
Find every environment check in this sample that gates execution:
VM/GPU/CPU fingerprinting, disk-size/MAC-prefix checks, debugger/analysis-tool
enumeration, timing-based sleep inflation, human-interaction probes. Map
each check to the branch it controls and the threshold constants it
compares against, with addresses. Produce a decision tree of which
conditions must hold for the sample to proceed — for building a detonation
environment, not for helping anything evade one.
```

### Destructive-payload behavior documentation (defensive)

```text
For authorized incident response on this sample, document its data-destruction
or encryption routine as code: algorithm and mode, key generation and
where the key/material leaves (network, embedded, discarded), file-targeting
rules (extension allow/deny lists, path exclusions with addresses),
shadow-copy and backup-destruction calls, and any ransom-note generation
logic. Deliver an indicator + behavior report with evidence per claim. Do
not attempt key recovery or decryption.
```
