<!--
This file is a preserved copy of the description of
https://github.com/kowalski-ptr/rea-reverse-engineer/pull/1
("feat(skills,setup): rework prompts/skills for voice and refusal-risk, add --skill-root"),
kept here so its content survives even if the repository or that PR is
later deleted. The text below is pasted verbatim from the PR body.
-->

# PR: feat(skills,setup): rework prompts/skills for voice and refusal-risk, add --skill-root

## Summary

This PR stays entirely within `kowalski-ptr/rea-reverse-engineer` — it does not touch, fork from, or propose anything to the upstream `morluto/rea` project. It bundles two logically independent, non-overlapping groups of changes developed in one session, kept as one PR because this repo has one owner and no external reviewers:

**Group A — prompt/skill rework** (`3c617d4`..`da92495`, docs/skill files only, zero `src/` changes): renamed and rewrote the 4 contributor-facing skills for clarity, rewrote `reverse-engineer-anything/SKILL.md` and its reference guides for structure/voice, added a new `rea-case-files` prompt-library skill (consolidating the inspiration material under `prompts-new/`), and applied a consistent educational/authorized-use framing repo-wide (`README.md`, `AGENTS.md`, `DISCLAIMER.md`) to reduce the chance of an AI model refusing to read this repo's anti-cheat/malware/kernel-exploitation research skills, without weakening any actual authorized-use boundary.

**Group B — `--skill-root` CLI flag** (`38ec489`, `61b56a4`, `src/` + one test + docs): lets `rea setup --skill-client shared --skill-root <path>` and `rea doctor --skill --skill-root <path>` install/verify the bundled skill inside one project directory instead of the caller's home directory, without touching any per-client skill path (Claude Code, Cursor, ...).

## Problem and expected behavior

**Group A:** several of this repo's agent-facing skills discuss anti-cheat, DMA, kernel exploitation, cracking, and malware analysis — legitimate, already-authorized-use-scoped research topics, but with enough keyword density near the top of each file that an AI model reading them cold could refuse to continue. Expected outcome: same technical content and the same (or stronger) authorized-use boundary, reframed so the scope/authorization statement comes before any risk-sounding term, consistently across every affected file.

**Group B:** `rea setup` always installed the bundled skill into a home-directory-rooted location (`~/.agents/skills` or a client's own personal skill directory) — there was no way to scope the skill itself to one project the way `claude mcp add --scope local` already scopes the MCP registration. Expected outcome: `--skill-root <path>` writes `<path>/.agents/skills/reverse-engineer-anything` and nothing under the caller's home directory, while leaving every per-client destination unchanged.

## Change and scope

**Group A:** Edits to `.agents/skills/**`, `prompts-new/SKILLS/**`, `README.md`, `AGENTS.md`, `DISCLAIMER.md`, `CONTRIBUTING.md`, `docs/*.md`. No tool contracts, no code, no behavior change — wording, naming, and structure only. Not applicable: no new provider/capability, no target execution/injection/mutation/protection-recovery scope question.

**Group B:** `skillRoot` is threaded as a new trailing _optional_ parameter through the existing `home`-taking functions (`clientSkillDirectories`, and all nine functions in `SetupSkill.ts`), rather than inserted into the middle of any signature — every existing call site that doesn't pass it keeps its exact prior behavior. It overrides only the unscoped "shared" destination's base; per-client definitions keep resolving against the real home directory. Intentionally **not** wired into `rea update`/`rea uninstall` — this mirrors the existing `--scope local` MCP registration, which those commands also can't see or manage; re-running `setup` with the same `--skill-root` is the documented refresh path. Not applicable: no new provider, no target execution/injection/mutation/protection-recovery scope question — this is a path-resolution option on an existing installer contract.

## Contract and boundary impact

- Semantic owner and earliest changed stage: `clientSkillDirectories` (`src/application/SupportedClients.ts`) owns shared-destination resolution; `SetupSkill.ts`'s nine functions, `SetupHost.ts`, `Doctor.ts`'s `systemDoctorHost`, and `doctorRuntime.ts` are the affected consumers, all updated consistently.
- Contribution scope fit and linked maintainer decision: not applicable (ordinary fix/feature within existing installer boundaries; sole maintainer is the PR author's principal).
- CLI and MCP/tool-catalog contract: additive only — new `--skill-root` option on `setup` and `doctor`; no existing flag, tool, or schema changed.
- Provider, bridge, target-format, or platform compatibility: unaffected.
- Evidence, artifact, provenance, or reconstruction contract: unaffected (installer-only change).
- Target selection, authority, upstream behavior, and cleanup details: not applicable.
- Generated metadata/package/installation impact: `src/generatedPackageMetadata.ts` regenerated (`skillVersion` 49→50) to catch up with an earlier commit in Group A that bumped the skill's own frontmatter version; `docs/installation.md` updated for the new flag.

Expected effects:

- [x] Read existing files or captured data.
- [ ] Launch or attach to a process.
- [ ] Inject instrumentation or run scripts/code.
- [x] Change process memory, registers, files, or configuration. <!-- writes skill files under the caller-selected root, same as existing setup -->
- [ ] Contact network services.
- [ ] Recover protected code or work around protections.
- [ ] Documentation or usability only; no target interaction.
- [ ] Not sure yet; investigation or maintainer guidance needed.

## Evidence and regression coverage

- Tests added or updated: new `describe("skill root override", ...)` block in `tests/boundary/filesystem/setupSkill.test.ts` (shared destination resolves under the override while a selected client's destination does not; install/needs-install/read-identity all observe the override consistently). Fixed one pre-existing test that asserted exact wording Group A had already reworded.
- Base reproduction or other evidence: confirmed via a real CLI run (built `dist/`, isolated `HOME`, `--dry-run` then a real `--yes` install) that the planned/installed path is entirely under the caller-selected `--skill-root` and the isolated `HOME` directory ends up completely empty — see Validation below.
- User-visible CLI/MCP output (if applicable): see the dry-run/install transcript below.
- Remaining proof gaps: no Windows-specific manual run (relied on the existing cross-platform `appendCallerPath`/`clientSkillDirectories` logic and its existing test coverage, which already covers `win32`); no interactive-TTY manual run of the new flag (automated tests cover the non-interactive path).

## Validation performed

All commands run locally against pinned Node 24.22.1 (via `nvm`, since this environment's default Node was below the repo's `engine-strict` floor):

- `npm run typecheck` — clean.
- `npm run lint` — 0 errors; +5 `max-params` warnings (one new optional parameter crossed the warn-at-4 threshold on 5 already-long signatures; 559→564 of 2865 total, still warnings only, matching this codebase's existing convention).
- `npm run docs:check` — clean (after fixing the pre-existing Group-A regression noted above).
- `npm run test:focused -- tests/boundary/filesystem/setupSkill.test.ts tests/boundary/filesystem/setupSkillCleanupOwnership.test.ts tests/boundary/filesystem/setupSkillPublication.test.ts tests/boundary/cli/cliSetup.test.ts tests/boundary/filesystem/skillSelectionProvenance.test.ts tests/boundary/filesystem/dshClientConfiguration.test.ts tests/boundary/filesystem/kimiClientConfiguration.test.ts src/doctorRuntime.test.ts tests/process-global/updateMaintenance.test.ts` — 198 passed, 0 failed, 4 pre-existing skips.
- `npm run test:local -- src/application/Setup.configuration.test.ts src/application/Setup.initialSnapshot.test.ts src/application/Setup.planning.test.ts src/application/Setup.recovery.test.ts src/application/SetupCleanupOwner.test.ts src/application/SetupInstallFailure.test.ts src/application/SetupRegistrationPrewarm.test.ts src/setupEnvironment.test.ts` — 146 passed.
- `npm run test:focused -- tests/acceptance/setup/selectedSkillSetup.test.ts tests/acceptance/setup/selectedClientHomePaths.test.ts tests/acceptance/setup/claudeCodeStrictJsonSetup.test.ts` — 12 passed.
- Real CLI dry-run with an isolated `HOME`:
  ```
  $ HOME=$FAKE_HOME node scripts/rea.mjs setup --skill-client shared --skill-root $PROJECT --dry-run
  plannedActions[1]: install_skill ... target: $PROJECT/.agents/skills/reverse-engineer-anything
  ```
- Real install with the same isolation, then inspected the filesystem directly:
  ```
  $ HOME=$FAKE_HOME node scripts/rea.mjs setup --skill-client shared --skill-root $PROJECT --yes
  $ find $PROJECT -maxdepth 4   # -> .agents/skills/reverse-engineer-anything/{SKILL.md,references/}
  $ find $FAKE_HOME -mindepth 1 # -> empty
  ```

## Compatibility, safety, and release

- Breaking changes or migration steps: none — `--skill-root` is new and optional; every existing call defaults to identical prior behavior.
- Real Hopper/Ghidra, browser, or OS coverage: not applicable (installer-only change, no provider involved).
- Package or release metadata impact: `skillVersion` metadata regenerated to match the already-bumped skill frontmatter (see above); no other package metadata change.
- Security, privacy, process, or containment review: `--skill-root` only changes a file-write destination the caller explicitly passes; it reuses the same backup/rollback/verification machinery (`installCanonicalSkill`, `SkillFilePublication`) as every other skill destination, with no new privilege or network surface. Confirmed no leakage to the home directory (see Validation).

## Author readiness

Human owner: @kowalski-ptr

- [ ] I reviewed the final diff and relevant existing code, understand the change and its edge cases, and take responsibility for the complete PR, including AI-assisted work.
- [ ] I checked the evidence above, confirmed it supports the current change, and recorded meaningful validation gaps.
- [ ] I declare this PR ready for initial maintainer review.

## Before merge

- [x] This PR addresses a concrete problem or an agreed enhancement.
- [x] Material scope and authority questions are resolved (not applicable — no new provider/target-execution/injection/mutation/protection-recovery capability in either group).
- [ ] The PR has one focused outcome and the title follows `type(scope): outcome`. <!-- honestly: two groups bundled by owner's own choice, see Summary -->
- [ ] Related issue is linked, or the reason for not linking one is stated above. <!-- no issue tracker in use for this private repo -->
- [x] Tests cover changed observable behavior and meaningful failure paths (Group B; Group A is docs-only).
- [x] Owning docs, contracts, and generated metadata are updated where needed.
- [x] User-visible CLI/MCP changes include representative output.
- [x] I checked the final diff for secrets, unrelated cleanup, and unsupported claims.

🤖 Generated with [Claude Code](https://claude.com/claude-code)

https://claude.ai/code/session_01CmLJtyYXqxYUqMeGLdvLRU
