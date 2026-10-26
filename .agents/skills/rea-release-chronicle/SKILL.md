---
name: rea-release-chronicle
description: Prepare or rewrite REA release changelogs and GitHub release notes from pinned Git history, with verified contributor thanks and Release Please synchronization.
---

# REA release chronicle

A changelog is the one artifact every user and every contributor reads
without context. This skill turns pinned Git history into that record —
release-note prose plus verified contributor attribution — without
authorizing merging, tagging, or publishing on its own.

Read [docs/releasing.md](../../../docs/releasing.md) from the repository
root first; it holds publication authority and the existing checkpoint
checks this skill has to satisfy.

## 1. Inventory before prose

Fetch complete history and tags. Select the published baseline tag from
`.release-please-manifest.json` on the release PR's **base branch**, and pin
that branch's exact SHA as the editorial target. REA tags are
`rea-agents-VERSION`. Use the proposed version as given, including its
prerelease suffix — don't swap the `always-bump-minor` strategy for
OpenClaw's calendar versioning just because it looks tidier.

```bash
node scripts/release-notes.mjs inventory \
  --repository morluto/rea --base rea-agents-PREVIOUS \
  --target FULL_BASE_BRANCH_SHA --version VERSION \
  --manifest /tmp/rea-VERSION.contributions.json
node scripts/release-notes.mjs record \
  --manifest /tmp/rea-VERSION.contributions.json \
  --summary \
  --output /tmp/rea-VERSION.record.md
```

Read the inventory itself, not just the bot's existing draft. It carries
side-branch commits, GitHub-associated PRs whose merge commits fall in
range, commit authors/co-authors, and confirmed closing issues with their
reporters. GitHub connections paginate, and network errors stop generation —
rerun into a *new* output file after recovery; never overwrite an existing
one.

An unresolved commit identity stays a name/email with `user: null` — never
guess a handle, and never thank the merger as a stand-in for the author.
The report is built to preserve unknowns, not to paper over them. Treat it
as a reviewed metadata snapshot, not independent proof that a human wrote
the patch. For unattributed identities, unusual merge strategies, backports,
reversions, or any reference GitHub didn't associate on its own, go inspect
the source PRs and diffs and report the limitation honestly. The current
collector credits issue reporters only through confirmed PR-closing
relationships — an unrelated issue mention earns no credit. And never claim
that inventoried work still ships once it's been reverted.

## 2. Write for users, and keep every credit

Rewrite only the newest `## [VERSION]` section in `CHANGELOG.md`, keeping its
version/date/link heading exactly as it is. Use single-line Markdown list
items under:

- `### Highlights` — a handful of release-defining user outcomes; no quota
  for a small release.
- `### Changes` — capabilities and meaningful behavior changes.
- `### Fixes` — reliability, correctness, compatibility, and safety fixes.
- `### ⚠ BREAKING CHANGES` — keep this section whenever applicable, with
  migrations and the original PR/commit references the existing checkpoint
  validator requires.

Group related work by analyst workflow or user impact, and explain the
resulting behavior in short, concrete entries. Let Highlights summarize the
release without repeating its full description down in Changes. Internal
cleanup, tests, CI, and routine documentation generally belong only in the
provenance file, not the public prose. Read an ambiguous PR or diff before
making any claim about it, and keep simulated-provider checks clearly
distinct from real-provider verification.

Credit each represented contribution exactly once in the public notes, with
its link and a `Thanks @...` for its verified human author, known
co-authors, and confirmed closing-issue reporters. Grouping work into one
line preserves every credit it carries. When Highlights, a detailed entry,
and a migration note all touch the same work, put the thanks in exactly one
of them — not all three — and include the issue link. A direct-commit bullet
carries its own commit link and known human credit.

Exclude GitHub bots and known agent accounts. REA's observed Anthropic,
Cursor, Codex, and Copilot co-author trailers resolve to `@claude`,
`@cursoragent`, `@codex`, and `@Copilot` with GitHub type `User` — the
credit policy excludes them while still keeping their metadata on record.
At the REA maintainer's request, omit `@morluto` self-thanks while
preserving that authorship in the inventory; every other human contributor
stays eligible. Use explicit GitHub links for cross-repository references
instead of an ambiguous bare `#NNN`. An unknown handle stays unknown — never
fabricate a credit to fill the gap.

Append the generated `record --summary` provenance link verbatim at the end
of this release's section. The linked inventory accounts for every
discovered in-range PR and direct contribution, including internal work the
prose itself omits. Don't paste the full `record` output into the public
notes — it duplicates thanks and buries the user-facing changes; the full
record stays available for anyone who wants to review it. Never hand-edit
generated credits just to make validation pass. Refresh the inventory when
product history has actually changed; a prose-only correction can reuse the
existing snapshot.

## 3. Finalize with Release Please

Get authorization for the specific release PR before changing its branch or
body. Local preparation and validation can proceed independently of that
approval.

1. Add `rea:release-notes-finalized` to the selected open release PR
   **before** editing its branch or body. Main-branch proposal pushes
   respect this label — it's our own workflow guard, not Release Please's
   `autorelease: snooze` mechanism. Let any proposal already running finish
   first, and never dispatch manual preparation over notes already
   finalized.
2. On that release branch, save the reviewed inventory as
   `docs/releases/VERSION.contributions.json`. This is durable public
   release provenance, not a scratch planning file — never add credentials
   or a local environment dump to it. Keep the existing version artifacts
   aligned with it.
3. Run the existing checkpoint validator and the new checker:

   ```bash
   node scripts/release-notes.mjs check \
     --manifest docs/releases/VERSION.contributions.json
   node scripts/release-notes.mjs render \
     --manifest docs/releases/VERSION.contributions.json \
     --output /tmp/rea-VERSION.notes.md
   ```

   The checker supports inline PR bodies, surrounding metadata included, up
   to GitHub's 65,536-character limit. It fails outright on larger notes
   rather than silently truncating credit — Release Please's overflow-link
   bodies aren't integrated here.

4. Update the release PR body with the rendered section while preserving its
   Release Please header/footer and component/version structure. Use a body
   file for `gh pr edit --body-file`; editing only `CHANGELOG.md` does not
   change Release Please's release body. Push only what's authorized.
5. Freshly fetch the PR body to a file and run `check --pr-body FILE`.
   Review the complete diff and wait for current CI. Publication checks the
   body again before creating the release — avoid further body edits once
   you're in that window.

The finalization label freezes bot refreshes, not `main` itself. Any product
change that lands after the inventory target needs a refreshed set of notes
before publication. The workflow checks target ancestry and allows only
release-metadata paths past that target, and it also requires the inventory
target to equal the reviewed merge's first parent — intervening `main`
commits mean a fresh inventory, no shortcuts. Review the diffs within those
allowed release-metadata paths too: the path guard proves a path was
touched, not that a package dependency or script edit was actually
version-only. To resume automatic proposals, remove the finalization label
and let the bot regenerate — expect your editorial changes to be replaced
when it does.

Releases that predate this inventory process keep their existing
publication path. Never retag or republish a package just to correct
release prose after the fact.
