# Evidence, comparison, and verification workflows

## Start from what's already inline

Evidence-producing tools return the complete Evidence record directly, in
MCP `structuredContent` and CLI JSON alike. Read `normalized_result` for the
operation data, and cite `evidence_id` whenever another tool or your own
explanation needs a stable reference. Preserve authority, limitations,
coverage, and residual unknowns throughout.

If a result comes back incomplete or paginated, keep going only when the
remaining data actually matters to the task — don't fetch a bundle or
resource just to read a result you already have inline. When complete
`inspect_binary_layout` or `analyze_javascript_application` Evidence is
already retained, call `inspect_analysis_view` for a summary, one
section/symbol/module, or a stable page instead of repeating the producer.

## Page a retained artifact inventory

A completed `inspect_artifact` inventory also supports a summary, plus
`view: {kind: "inventory", collection: "occurrences", logical_paths: ["package.json", "main.js"]}`.
Omit `logical_paths` to page every available occurrence; `[]` selects none.
The other inventory collections are `nodes`, `edges`, and
`integrity_contradictions`.

Keep `source_complete` separate from page exhaustion — a missing match in
partial source Evidence doesn't establish absence. Use the returned parent
Evidence ID for subsequent views, and export before closing the session if
the source needs to survive it.

When `tools/list` advertises `inspect_artifact.detail`, request `"summary"`:
it retains the complete source and hands you that view immediately. Without
that advertised choice, keep making the existing complete call. CLI
inspection returns portable complete Evidence either way — save it before
projecting a selected inline view.

## Page a retained native inventory

For native procedure/name/string inventories, select `detail: "summary"`
when the connected tool advertises it and the task only needs counts or
selected rows. Then query the parent Evidence ID with
`view: {kind: "native-inventory", offset: 0, limit: 32, fields: ["value"]}`.
Search tools retain the existing pattern/mode/case/document selection.

Omit `fields` for complete records; `record.address` and `source_index`
stay on every selected row regardless. Selecting `value` keeps its
string/decoding qualifications, and parent Evidence ID plus source index
preserves same-address alternatives. Follow `coverage.next_offset` only as
far as the question needs — exhausting the retained rows doesn't establish
complete program discovery.

## Track what's still unresolved

Use `record_unknown` to log unresolved questions in the user's investigation,
naming the authority or environment still required, and supplying supporting
and contradicting evidence IDs. It updates the session registry directly.

Use `update_unknown` with the current revision; if that revision is stale,
reread rather than retrying blindly. Only qualifying observed evidence can
verify a resolution.

## Compare with the matching tool

Reach for comparisons only with complete, compatible page sets when you're
claiming equivalence or absence:

- **`compare_artifacts`** compares inventory pages by occurrence path,
  content, metadata, and graph relations.
- **`compare_functions`** compares explicit function Evidence, not fuzzy
  whole-binary matches — it rejects an `analyze_function` summary reply, so
  pass the summary's `parent_evidence_id` as a `retained-evidence` reference,
  or the complete dossier.
- **`compare_bundles`** compares canonical bundle membership and unknown
  history.
- **`find_changed_behavior`** combines existing comparisons; static
  differences stay candidates, not causal proof.
- **`build_call_path`** needs explicit function dossiers from one
  artifact/provider — an incomplete frontier makes absence unknown.
- **`correlate_static_and_runtime`** uses explicit mappings; matching
  patterns are hypotheses, not causality.
- **`verify_reconstruction`** evaluates a finite typed specification — a
  pass covers only the declared comparable claims, not global equivalence.

## Process captures: freshness, not a sandbox

Process captures are opt-in behavioral evidence, not a security sandbox. V3
captures cannot be upgraded to V4 — rerun the original scenario instead.
Distinguish root exit from descendant settlement, and require freshness when
the task needs it.

A timed-out target that must still write a final report needs
`finalization_ms`: REA sends SIGTERM to the captured root, keeps observing,
and sends SIGKILL exactly once if the target hasn't exited by the end of the
interval. `exit.finalization.signals` lists each attempt with its delivery
result. The observed exit signal stays in `exit.signal`; `exit.code` is null
for every deadline exit; the code of a finalizer that exited on its own
lands in `exit.finalization.exit_code` (null on a signal exit, or when no
exit was observed).

## Turn a HAR into surfaces, an environment, and its state

**Group requests by shape.** `inspect_analysis_view` with
`view: {kind: "api-surface"}` on retained `inspect_web_network_capture` HAR
Evidence groups observed request lines by method, origin, and path
template. Format matches are inferences, not handler identity or schemas.
Group pages cite parent record ordinals — check record coverage and
exclusions before claiming completeness. No requests are replayed. When the
connected schema advertises `include_query_parameter_names`, select it for
distinct observed names with their original record ordinals; query values
stay omitted, so the names establish neither required parameters nor a
complete server schema. Keep the original parent Evidence around for exact
URLs.

**Project an agent environment manifest.** `view: {kind: "environment-manifest"}`
on the same retained HAR Evidence projects the primary origin's request
lines into named tools — path templates, parameters, observed query names,
value-free body shapes, and record pointers. Opt into `template_rules` only
when recorded identifiers actually need them: every template, parameter, and
merged sibling is an inference, no header, cookie, or body value is copied,
and the three stated unknowns stay unresolved.

**Derive the state those reads observed.** `view: {kind: "environment-state"}`,
used with the manifest's `template_rules`, extracts entity types and identity
fields, entities with recorded values, documents, and per-tool render plans
checked against the recordings. The state comes only from reads before the
first related write — types, identity, and list membership are inferences.
The view also gives each state-changing tool a write plan, served only when
a later recorded read confirms its effect and none contradicts it.

`rea environment-serve manifest.json --evidence capture.json` derives that
state at startup, answers read tools from it, and serves the write tools
whose plan is served — each applying its effect to that state — plus
`data/reset`, `data/add`, and `data/get` on `/agentenv`. Other write tools
aren't served; each plan's `reason` says why. It falls back to replaying the
capture with `--replay-only`, or whenever the state is unavailable (not
derivable, or a catalog over the discovery budget), and the stopped
document's `state` field says which path applied.

**Preview what an environment would serve.** `view: {kind: "environment-coverage"}`,
with the same rules, shows what an environment built from a capture would
serve: read tools answered from the state or replayed (with their reason),
and each write tool served or withheld (with a hint about what to record
next) — without applying the serve-time catalog and state size checks.
`rea environment-build <capture.har> <dir>` inspects, projects, and exports
in one step, printing that same coverage, or `null` when the bundle ends up
serving replay.

## Page retained native exports

For a PE/ELF export question, `inspect_native_exports` accepts
`detail: "summary"` and retains the complete table. Query
`inspect_analysis_view` with
`view: {kind: "native-exports", collection: "exports", names: ["selected_name"], fields: ["name"], offset: 0, limit: 32}`
against the reported parent Evidence ID. All same-name versions and aliases
survive; use `source_index` with the parent ID for stable identity.
`collection: "issues"` pages interpretation failures.

Page coverage describes the selected retained rows only — `summary.coverage`
or `exports.coverage` still describes the source table, so an empty partial
page doesn't prove absence. CLI
`inspect-native-exports --detail summary --evidence_output PATH` saves the
complete bundle for later `inspect-analysis-view --evidence-bundle PATH`
requests.
