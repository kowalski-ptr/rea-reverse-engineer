---
name: rea-case-files
description: Ready-to-paste REA investigation templates for learning how real software is built, so you can design and build your own for private use — organized by target domain (native binaries, web/Electron, Android, games, firmware, kernel/drivers, cloning). Also includes a security-education set (vulnerability review, incident-response triage, software-protection assessment) scoped to your own software, CTFs, bug bounties, and authorized research — see DISCLAIMER.md. Use when a user wants to point REA at a reverse-engineering task and needs a concrete, evidence-first prompt rather than a vague request. Install the `reverse-engineer-anything` skill separately for the actual tool contracts this library points at.
---

# REA case files

**What this is for:** learning how existing software actually works — its
algorithms, protocols, data formats, and architecture — so you can study the
best patterns you find and build your own software from that understanding.
That's the same method students, security engineers, and curious developers
have always used to learn: read how something good was built, then build
your own. Every template below produces *your own* documented understanding,
never someone else's code or assets.

A blank "reverse-engineer this for me" produces a blank answer. This library
is the fix: one opening ritual, then a case file per target domain, each
holding a handful of prompt templates that already encode the habits that
make REA's output trustworthy — cite the evidence, separate fact from
inference, start static, and finish with something concrete.

This skill is a **companion** to `reverse-engineer-anything`, not a
replacement. It assumes that skill (and REA itself) is installed and
answers "what should I ask for", not "how does the tool work" — for tool
contracts, provider setup, and recovery, defer to that skill and its
references.

## Scope, up front

> **Scope:** your own software or hardware, a CTF/crackme, a vendor-authorized
> bug bounty, or incident-response work you're permitted to do. This
> project's authorized-use policy ([DISCLAIMER.md](../../../DISCLAIMER.md))
> applies to everything below — lawful analysis, security education, and
> interoperability, never unauthorized access to someone else's running
> system, account, or session.

Every case file repeats this scope in its own opening line, because it's the
precondition for everything in it, not an afterthought.

## Open every case the same way

Before picking a domain, run the opening ritual once per investigation:

```text
Use REA to understand how <FEATURE> works in <PATH TO APPLICATION OR URL>.

Check which analysis tools and providers are actually available before
assuming any of them. Start with static analysis and trace the feature down
to the functions, classes, or modules responsible.

For every conclusion, cite the evidence — file, function, address, or
snippet — and sort findings into confirmed facts, inferences, and open
unknowns. Do not run or execute the target unless static evidence can't
answer the question and you've confirmed the target is one I own or am
authorized to test. Finish with a concrete deliverable: a short spec, a
reader script, or a note on what remains unverified.
```

That single template is the wildcard: paste it when nothing more specific
fits, or as the frame you specialize with a case file below.

### First session on an unfamiliar target

```text
Give me a map of <program>: its main parts, entry points, and the three
features most worth investigating first. Keep it short, and point to
evidence for each claim rather than describing the whole thing.
```

### Picking up a paused investigation

```text
Review this investigation's existing notes and Evidence. Use
audit_residual_unknowns, if available, to list what still isn't demonstrated,
then rank the next analyses by how much uncertainty each one would remove.
Update the investigation log with new findings, discarded hypotheses, and
what's next — don't restart work that's already settled.
```

### Comparing two shipped versions

```text
Use the compare_application_versions workflow, if available, to compare
<OLD VERSION> and <NEW VERSION> of <application>. Identify functional
changes, API/dependency/permission changes, and anything that looks like a
security fix. Map each change to the functions or files behind it, and keep
build-only changes (recompiles, stripped symbols) separate from actual
behavior changes.
```

## Why these templates are shaped this way

1. **One feature per request.** "Explain the whole app" returns a shallow
   tour; "explain this one button" returns a call chain with addresses.
2. **Evidence is mandatory, not optional.** Every template demands a file,
   function, address, or snippet — that's what keeps an agent from
   confabulating a plausible-sounding answer.
3. **Three buckets, not two.** Fact, inference, and unknown — collapsing
   inference into fact is the single most common way an investigation
   quietly becomes wrong.
4. **Static before runtime, always stated out loud.** Say so explicitly
   until you actually need an observed execution to answer the question.
5. **End on a deliverable.** A spec file, a standalone reader, a port plan,
   a risk-ranked report — naming the artifact forces the agent to verify its
   own claims instead of just describing them.
6. **Name the scope, every time.** "My own lab machine," "offline
   single-player," "a documented assessment of my own product" — stating it
   keeps the investigation usable for learning and reporting instead of
   drifting into something nobody actually asked for.

## Case files

Open the one that matches the target — each is short enough to read in
full, opens with the same scope statement, and links back to
`reverse-engineer-anything`'s own references where the real tool contracts
live.

| Target                                                                | Case file                                                                                             |
| ------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------- |
| Native executables, libraries; deobfuscation; Go/Rust recovery           | [references/native-binaries-and-deobfuscation.md](references/native-binaries-and-deobfuscation.md)       |
| Web apps, Electron, browser extensions, bundled/minified JS              | [references/web-electron-and-browser.md](references/web-electron-and-browser.md)                           |
| Android APKs, Java/Kotlin + native/JNI boundaries                        | [references/android-and-mobile.md](references/android-and-mobile.md)                                     |
| Games: gameplay logic, engines, asset pipelines, WASM portability         | [references/games-and-portability.md](references/games-and-portability.md)                               |
| Firmware images, MCU/IoT blobs, embedded protocols                       | [references/firmware-and-embedded.md](references/firmware-and-embedded.md)                               |
| Kernel modules, drivers, syscalls — reachability and hardening review     | [references/kernel-and-drivers.md](references/kernel-and-drivers.md)                                     |
| Trust boundaries, sinks, patch diffing, crash-to-root-cause               | [references/vulnerability-and-security-review.md](references/vulnerability-and-security-review.md)       |
| Incident-response sample triage: static capability mapping, config/IOC extraction | [references/incident-response-sample-triage.md](references/incident-response-sample-triage.md)    |
| Software-protection assessment: license checks, anti-tamper, hardware countermeasures | [references/software-protection-assessment.md](references/software-protection-assessment.md)    |
| Clean-room cloning, protocol/ABI recovery, behavioral equivalence         | [references/cloning-and-reconstruction.md](references/cloning-and-reconstruction.md)                       |

## Scope, stated plainly

Only point any of these templates at software you own or are explicitly
authorized to test — your own builds, a lab machine, a CTF or crackme, a
vendor bug bounty, or a sample handed to you for incident response. The
kernel, software-protection, and incident-response case files exist for
security education, interoperability, and hardening your own product: they
ask the agent to produce a *documented assessment*, not to weaponize
anything against someone else's software or touch another player's live
session. When a template doesn't fit your authorization, don't paste it —
describe the actual boundary to the agent instead.
