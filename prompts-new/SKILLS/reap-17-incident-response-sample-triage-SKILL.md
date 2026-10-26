---
name: reap-17-incident-response-sample-triage
description: REA prompt pack: Incident-response sample triage (8 ready-to-paste prompts). Use for authorized static analysis of a collected sample during incident response or threat-intel work, with REA (rea.tools) via an AI coding agent.
---

# REAP — 17. Incident-response sample triage

Part of REAP (REA Investigation Prompts). Replace everything in <angle brackets>; keep each request focused on one feature. Demand file/function/address evidence and separate facts from hypotheses.

Authorized contexts: your own incident response, a sandboxed lab, threat-intel
work, or samples you have explicit permission to handle. See this project's
authorized-use policy in `DISCLAIMER.md`. Never run a sample on
a production machine; these prompts are static-first and ask for runtime
observation only inside an isolated VM.

### Static triage and capability map

```text
Use REA on this sample: <PATH> (collected for authorized incident
response / threat-intel analysis).

Produce a triage report: file type, compiler/packer indicators, first-run
artifacts, imports grouped by capability (persistence, credential
theft, keylogging, screen capture, network command-and-control, process injection, defense
evasion), suspicious strings with addresses, and any embedded configs
(URLs, wallet addresses, mutexes, encryption keys).

For every capability claim, cite the function/address that proves it. Separate
confirmed static findings from behaviors that still need sandbox confirmation.
Do not execute the sample in this step.
```

### Command-and-control protocol and config extraction

```text
Use REA on this sample to recover its command-and-control protocol.

Find the endpoint construction (hardcoded strings, DGA, XOR/base64-encoded
buffers — show the decode routine and its key), the beacon format (fields,
encoding, encryption), the tasking commands it accepts and what each one
does, and any fallback infrastructure.

Output: a written protocol spec, a Python parser for a captured beacon if you
have one, and the addresses proving each field. Label anything inferred.
```

### Persistence, injection and evasion inventory

```text
Use REA on this sample. Enumerate every persistence mechanism (registry
Run keys, scheduled tasks, services, startup folder, WMI subscriptions),
every process-injection technique (CreateRemoteThread, APC, process hollowing, DLL
side-loading) and every evasion trick (anti-VM checks, anti-debug, sandbox
timing checks, sleep evasion, AMSI/ETW patching).

For each: the function/address, the exact API calls used, and the trigger
condition. Rank them by which runs first in the execution flow.
```

### Detection-rule generation from recovered code

```text
Use REA on this sample and, from the functions you have already located,
generate detection artifacts: YARA rules built on stable code sequences (not
obfuscated strings), a list of file/registry/network indicators with the addresses
where each appears, and a threat-hunt query outline. Explain why each pattern
is family-stable versus sample-specific.
```

### Family correlation between samples

```text
Use REA to compare <sample A> and <sample B>. Identify shared code: matching
functions (by structure, not just hash), common string-encoding routines,
shared protocol fields, reused constants or PDB paths.

Conclude whether they are the same family, same builder, or unrelated, with
the function-pair evidence behind each claim.
```

### Loader stage-chain reconstruction

```text
Use REA on this loader sample. Reconstruct the stage chain: what the first-stage
stub does (process hollowing, DLL side-loading, reflective loading,
APC injection — show the API sequence with addresses), where the second-stage
payload comes from (embedded blob offset, download URL, decode routine), and
how integrity/decryption of the payload is verified before transfer of
execution. Deliver a stage diagram: stage → mechanism → artifact → evidence
address. Do not execute the sample.
```

### Sandbox-evasion decision tree

```text
Use REA on this sample. Find every environment check that gates
execution: VM/GPU/CPU fingerprinting, disk-size and MAC-prefix checks,
debugger and analysis-tool enumeration, timing-based sleep inflation,
human-interaction probes (mouse jitter, cursor checks). Map each check to the
branch it controls and the threshold constants it compares against, with
addresses. Produce a decision tree: which conditions must hold for the
sample to proceed — useful for building a detonation environment, not for
helping anything evade one.
```

### Destructive-payload behavior documentation (defensive)

```text
Use REA on this sample for authorized incident response. Document its
data-destruction or encryption routine as code: algorithm and mode (from API calls or embedded
primitives), key generation and where the key/material leaves (network, embedded,
discarded), file-targeting rules (extension allow/deny lists, path exclusions
with addresses), shadow-copy and backup destruction calls, and any ransom-note
generation logic. Deliver an indicator + behavior report with evidence per claim;
do not attempt key recovery or decryption.
```

---
