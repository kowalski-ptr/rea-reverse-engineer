---
name: reap-prompts
description: REAP master index: curated REA prompts for learning how real software is built -- native binaries, Go/Rust source recovery, web/Electron, games, firmware, Android, kernel, cloning -- plus a security-education set (vulnerability review, incident-response triage, protection research) for your own software, CTFs, and authorized research. Use when pointing an agent at RE work.
---

# REAP — REA Investigation Prompts (skill index)

A curated collection of prompts for [REA](https://rea.tools/) — the toolset that lets your AI coding agent learn how real software is built, so you can study the best patterns you find and build your own. Each pack below is a sibling skill folder in this directory; load the one matching the target, paste a prompt and replace <angle brackets>.

**Scope:** your own software or hardware, a CTF/crackme, a vendor-authorized bug bounty, or incident-response work you're permitted to do. This project's authorized-use policy (`DISCLAIMER.md`) applies to every pack below — lawful analysis and security education, never unauthorized access to someone else's running system or session.

| Skill | Section | Prompts |
|---|---|---|
| `reap-00-install-and-verify` | 0. Install & verify | 0 |
| `reap-01-getting-started` | 1. Getting started | 2 |
| `reap-02-feature-tracing-any-program` | 2. Feature tracing (any program) | 3 |
| `reap-03-native-binary-exploration` | 3. Native binary exploration | 5 |
| `reap-04-software-and-feature-cloning` | 4. Software & feature cloning | 4 |
| `reap-05-games` | 5. Games | 4 |
| `reap-06-game-portability-to-wasm` | 6. Game portability to WASM | 3 |
| `reap-07-firmware-and-embedded` | 7. Firmware & embedded | 3 |
| `reap-08-android` | 8. Android | 4 |
| `reap-09-kernel-analysis` | 9. Kernel analysis | 3 |
| `reap-10-vulnerability-research` | 10. Vulnerability research | 5 |
| `reap-11-electron-and-javascript` | 11. Electron & JavaScript | 11 |
| `reap-12-software-protection-research` | 12. Software protection research | 4 |
| `reap-13-hardware-memory-access-protection-research` | 13. Hardware memory-access protection research | 4 |
| `reap-14-license-validation-research` | 14. License-validation research | 3 |
| `reap-15-version-comparison-and-crash-work` | 15. Version comparison & crash work | 1 |
| `reap-16-long-running-investigations` | 16. Long-running investigations | 1 |
| `reap-17-incident-response-sample-triage` | 17. Incident-response sample triage | 8 |
| `reap-18-advanced-binary-exploration-and-deobfuscation` | 18. Advanced binary exploration & deobfuscation | 8 |
| `reap-19-kernel-and-driver-reachability-research` | 19. Kernel and driver reachability research | 7 |
| `reap-20-advanced-software-cloning-and-feature-reconstruction` | 20. Advanced software cloning & feature reconstruction | 5 |
| `reap-21-protector-and-obfuscator-research` | 21. Protector & obfuscator research | 7 |
| `reap-22-go-and-rust-binary-source-recovery` | 22. Go & Rust binary source recovery | 7 |
| `reap-prompt-writing-tips` | Prompt-writing tips (why these work) | 0 |
| `reap-wildcard` | Wildcard prompt (start here) | 0 |

## Wildcard prompt (start here)

```text
Use REA to understand how <FEATURE> works in <PATH TO APPLICATION>.

Check first which tools are actually available. Start with static analysis
and trace the feature down to the responsible functions.

Present evidence for each conclusion, separate facts from hypotheses, and
record the limitations. At the end, generate a behavioral specification and
propose tests to verify it.
```

## Prompt-writing tips (why these work)

1. **One feature per prompt.** Broad "explain the whole app" questions return shallow maps; narrow ones return call chains and addresses.
2. **Demand evidence.** Every prompt here requires file/function/address citations — it stops the agent from confabulating.
3. **Separate fact / inference / unknown.** The three-bucket format keeps hypotheses honest.
4. **Static first.** Say "do not execute the application" until you actually need runtime observation.
5. **End with a deliverable.** SPEC.md, a reader script, a port plan, a minimal rebuild — a concrete artifact forces the agent to verify its own claims.
6. **State the scope out loud.** "Lab machine I own", "offline single-player", "a documented assessment of my own product" — the framing keeps results usable for research and reporting.
