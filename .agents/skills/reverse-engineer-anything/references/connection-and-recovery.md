# Connection and task recovery

Come here only once something has actually gone wrong — a connection,
command, approval, or provider blocker you've observed, not one you're
anticipating. Keep the user's target and question in view while you repair
it: explain what prevented the operation and the next useful step, then
resume the investigation once it's fixed.

Commands below assume an installed `rea`. If none is available, use the
package invocation in `SKILL.md` when acquiring REA is authorized. Follow
the connected server's schemas and the installed CLI's own help rather than
assuming repository `main` and an older release expose the same arguments.
A tool-name prefix belongs to the *client*, not REA — find the canonical
REA operation in the client's actual tool listing and server association
before concluding it's absent. A shared prefix alone doesn't establish
which server an approval wildcard covers. See
[client tool names and discovery](https://github.com/morluto/rea/blob/main/docs/client-configuration.md#client-tool-names-and-discovery).

## Match the remedy to what you actually observed

| Observation                                                     | Meaning and next step                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| --------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| No REA tools in the active session                              | Inspect registration with `rea doctor --client codex --json`. A skill installation alone does not register MCP.                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| Missing, malformed or stale registration                        | Prepare a scoped setup plan; apply only authorized changes with backups.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Aligned registration, but no connected tools                    | Restart/reconnect the client. Doctor checks registration; it cannot establish the current agent's connection. Inspect the client's launch error if reconnection fails.                                                                                                                                                                                                                                                                                                                                                                                                                  |
| MCP connection dropped or REA could not start                   | If the connection drops, retry that call once only when it is safe to repeat or known not to have started. If it may already have changed the target, or the same failure returns, tell the user REA's connection failed and ask them to restart the MCP client. If REA could not start its connection, retry once, then tell the user and ask them to restart the MCP client. Do not retry a startup failure that returns the same unchanged result. Run `rea doctor` when REA could not start or tools stay missing after restart. Doctor cannot restore a live connection by itself. |
| `MCP tool call requires approval, but approval policy is never` | Codex rejected the call before REA executed it. Do not install an engine or repeat setup to solve this client-policy decision. See the approval section below.                                                                                                                                                                                                                                                                                                                                                                                                                          |
| REA returns `provider_unavailable` or an unavailable tool entry | Read that operation's provider diagnostics or `binary_session.result.tool_availability`. Expand each group's tool names and inherit the shared client requirements unless overridden. Use the operation's availability, reason, and remediation; a complete tool catalog does not imply every operation is available. Request `{"detail": "complete"}` only for provider diagnostics.                                                                                                                                                                                                   |
| `rea` is missing in a child shell, or Node is incompatible      | Inspect the actual shell PATH and launcher/runtime. A prefix installation may be absent from a login shell's PATH; this does not establish a broken MCP registration.                                                                                                                                                                                                                                                                                                                                                                                                                  |

If the client can't launch its shell/sandbox helper or read the installed
skill, inspect its own launch diagnostic — Codex, for instance, can refuse
helper aliases when `CODEX_HOME` is under `/tmp`; use an authorized profile
outside the OS temporary directory instead. REA setup can't repair the
client's executor. Preserve the sandbox rather than quietly disabling it
just to make one analysis command work.

None of this is a reason to stall a question static evidence can already
answer: static JavaScript and ordinary ZIP inventory need neither Hopper
nor Ghidra, and HAR inspection is offline and needs no live browser. Native
decompilation, Android analysis, and firmware extraction each carry their
own bring-your-own prerequisites. Use whatever supported inventory/static
evidence already answers part of the question, and name the unavailable
evidence separately rather than substituting another target. Label
observations that came from ordinary shell tools as such — only actual REA
output supplies REA Evidence.

## Distinguish client and server deadlines

A host message such as `timed out awaiting tools/call` with no REA result
means the *client's* deadline expired before a result arrived — it does
not establish that REA finished, canceled successfully, or released every
resource. Don't repeat the unchanged analysis. Inspect available
session/provider state and retained Evidence only when it's actually
useful: an idle `binary_session` isn't proof that every target-free
operation stopped, and a missing Evidence reference is never something to
invent.

Let the host tool deadline cover execution, cancellation/cleanup, and
response delivery together. In Codex, that setting is
`mcp_servers.<server-id>.tool_timeout_sec` in **seconds** — not
`startup_timeout_sec`. Pick a value for the actual operation's budget plus
headroom, not a one-size-fits-all timeout; see the
[official MCP configuration reference](https://learn.chatgpt.com/docs/extend/mcp?surface=cli).
Apply configuration changes only within the authorized scope, preserving
unrelated entries and backups, and reconnect/restart as the host requires.
When configuration isn't editable, report the setting that's needed or use
an authorized CLI fallback — don't silently broaden permissions or switch
targets instead.

A returned REA `provider_timeout`, by contrast, means the **server or
provider** itself reached its deadline. Follow its `remediation.action`,
reuse whatever partial Evidence is already accessible, and resolve any
reported incomplete cleanup before retrying. Change a request field or a
`REA_*` setting only when the returned remediation explicitly recommends
that exact change, and restart REA only when that text says the value is
read at startup — a note that a control *doesn't* raise the deadline is
not a recommendation to change it, and no setting the remediation doesn't
name should be invented. Raising the client deadline alone never changes
this server-side budget.

For static JavaScript specifically, `analysis_timeout_ms` — CLI
`rea analyze-javascript-application <path> --analysis-timeout-ms <milliseconds>` —
controls each source's startup, analysis, and transfer deadline in
**milliseconds**. Increase that budget, reduce semantic work, or select
exact inventoried `source_paths` when the task allows it. Omit
`source_paths` to cover every source; `[]` is invalid, not "none."
`detail: "summary"` reduces delivery, not analysis work, and a per-source
budget doesn't bound the elapsed time of an entire multi-source
application. Inspecting partial Evidence can avoid recomputation — an
unavailable reader is not, on its own, a reason to block a corrected
static-analysis retry. The shorthand `rea analyze <path>` doesn't expose
this timeout flag at all; use the explicit JavaScript command when you
need to change that budget.

For invalid arguments, use the connected tool's current input schema, or
the installed CLI's own command help. Refresh the catalog if it's stale —
don't guess fields from an older skill, and don't return the entire
catalog just to repair one field. Correct every reported field before
retrying.

## Work the recovery ladder in order

Follow the returned remediation. Change a request field or a `REA_*`
setting only when that text explicitly recommends the change, and restart
REA only when it says the value is read at startup. A note that a control
doesn't raise a limit is not a recommendation to change it, and no setting
should be invented. Host memory and `NODE_OPTIONS` never raise a REA
admission limit unless that remediation says so.

- **MCP connection dropped:** retry that call once, only when it's safe to
  repeat or known not to have started. If it may have already changed the
  target, or it fails again, tell the user REA's connection failed and ask
  them to restart the MCP client. Don't run `rea doctor` for the drop
  itself.
- **REA could not start its connection:** retry once, then tell the user
  and ask them to restart the MCP client. Run `rea doctor` when REA
  couldn't start, or tools stay missing after that restart. Don't retry a
  startup failure that returns the same unchanged result.
- **`cleanup_incomplete`, or a retained cleanup owner named in the error
  details:** retry that exact cleanup owner. Don't start new work that
  needs those resources until that owner confirms release — repeating the
  original call is not the same as retrying the cleanup.
- **Capacity**, when the result is `resource_constraint` and `retryable`
  is true: wait for the active admission to finish, then retry within the
  reported limits. Don't restart REA just to free the slot.
- **`configuration_invalid`:** correct the named setting in the
  environment that starts REA (the shell or the MCP registration), then
  restart REA. Run `rea doctor` to see the `runtime-configuration:` check.
  Re-run `rea setup` for that client when the registration itself must
  carry the new setting, then reconnect.
- **`invalid_request`:** correct the reported fields from the current
  schema. Don't repeat the unchanged call and hope.

## Repair registration when it's actually needed

For Codex:

```bash
rea doctor --client codex --json
rea setup --client codex --dry-run --json
```

Use the current supported client's ID: `claude_code`, `claude_desktop`,
`codex`, `cursor`, `gemini_cli`, `windsurf`, `devin`, `opencode`,
`antigravity`, `copilot_cli`, `commandcode`, `qwen_code`, `vscode`, `trae`,
`grok_build`, `omp`, `pi`, `hermes`, `qoder`, `droid`, `zcode`, `dsh`,
`freebuff`, or `grok_bot`. If the client is unknown, inspect `doctor --json`
before choosing a scope. `grok_bot` uses a chat registration step rather
than a local `mcp.json`. For unsupported clients, use version-pinned manual
stdio registration or the CLI directly.

Show the plan's exact paths, backups, changes, and bundled-skill
replacement. Get approval for writes whenever that concrete scope isn't
already authorized, then apply the same scope with
`rea setup --client codex --yes`. Add `--install-hopper` only when that
separate installation was actually needed and explicitly approved — and
never install or upgrade an unrelated prerequisite along the way. Then
restart/reconnect, verify the tools actually appear, and resume the
original task. If they're still absent, inspect the launch failure itself
rather than looping through setup again.

## Codex tool approval is separate from REA readiness

REA tools have no permission-grant handshake of their own. Codex can still
require approval because an operation creates owned resources, launches a
helper, or has cleanup effects — and an advertised tool or a successful
doctor result doesn't override that client policy. `workspace-write` alone
doesn't resolve an MCP approval rejection, and `approval_policy = "never"`
doesn't mean every MCP tool is preapproved.

When interactive approval is available, use the client's approval flow for
the requested operation. In an unattended run, continue through an
authorized CLI workflow if the execution environment permits it, or report
the blocked operation and the approval it needs to proceed. Never retry the
same rejected call, silently broaden approvals, or rewrite an MCP effect as
read-only to get around this.

For an operator who explicitly wants to preapprove named operations in a
disposable Codex profile: current Codex supports per-tool configuration.
For example, opening and closing an analysis session can be scoped
independently from each other:

```toml
[mcp_servers.rea.tools.open_binary]
approval_mode = "approve"

[mcp_servers.rea.tools.close_binary]
approval_mode = "approve"
```

Use the actual server ID, and only the operations whose effects were
actually approved — this example does not approve inspection, extraction,
or runtime execution. Show the proposed config edit and preserve unrelated
entries and backups before writing it. Managed policy can constrain client
settings further; consult the
[Codex configuration reference](https://learn.chatgpt.com/docs/config-file/config-reference)
for the installed client's supported settings. Treat this as an optional
recovery choice, not a prerequisite for every REA task.

## Repair an editable REA limit

If `details.limit` names an editable setting and its bounds, call
`update_analysis_settings` directly with a `changes` entry containing
`setting`, `action: "set"`, and `value`. `inspect_analysis_settings` is
optional once those facts are already in hand. Retry the same input after
work and cleanup settle. Runtime defaults affect new work only — an
explicit per-call argument takes precedence and must be corrected
directly where it was set. Reset restores launch values. Updates survive a
target close/open but disappear on REA restart, and they never edit host
deadlines, response buffers, registrations, or fixed limits.

## Continue through an installed CLI

Check the command environment only once the launcher has actually failed:

```bash
command -v rea
command -v node
node --version
rea --version
```

Use the public launcher at its known installation path with a supported
existing Node on PATH when a child shell has lost a prefix installation.
Avoid internal `dist/cli.js` entrypoints or global toolchain changes — MCP
can have its own valid launcher even when a shell can't resolve `rea`.

For a user-supplied JavaScript tree or ASAR, select output files in the
authorized workspace and save complete Evidence before reading a view:

```bash
rea analyze-javascript-application /absolute/path/to/app --json > app-evidence.json
jq -c '{source: {kind: "inline", evidence: .}, view: {kind: "summary"}}' app-evidence.json > app-view.json
rea inspect-analysis-view app-view.json
```

None of these commands register MCP or install an analysis engine. CLI
JavaScript JSON is complete and can get very large — redirect it rather
than loading it into context, and use an existing JSON processor if `jq`
is absent rather than installing one just for this recipe. Inspect a
module page or item using that same inline source and the
[JavaScript recipes](javascript-applications.md), preserving graph
coverage, limitations, and unknowns. Native CLI analysis still requires
its selected engine, and every CLI operation retains the host's own
execution permissions.
