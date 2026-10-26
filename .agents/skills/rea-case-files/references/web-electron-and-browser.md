# Case file — web, Electron & browser targets

For ASARs, extracted JavaScript, live sites, and browser extensions. Static
work routes through `analyze_javascript_application`; anything that needs
the real page or app running goes through the browser/Electron workflows in
[references/runtime-observation.md](../../reverse-engineer-anything/references/runtime-observation.md)
and [references/javascript-applications.md](../../reverse-engineer-anything/references/javascript-applications.md).

### Electron app anatomy

```text
Analyze <PATH TO ELECTRON APP> with REA, including any app.asar archives.
Map the main and renderer processes, the preload scripts, and the IPC
channels between them. Then trace <FEATURE> from the UI to its
implementation — network calls and filesystem operations included — and
show the evidence for each hop.
```

### IPC and contextIsolation security review

```text
Enumerate every ipcMain handler in <Electron app> and every channel exposed
via contextBridge/preload. For each: what arguments it accepts, whether it
reaches shell.openExternal, child_process, fs, or webview navigation, and
what validation exists before it does. Flag any handler where
renderer-controlled input reaches a privileged sink unchecked, with file
and function evidence.
```

### Tracing a feature through minified/bundled JS

```text
Trace <FEATURE> through <app.asar / bundle.js> even though it's minified.
Identify module boundaries, recover the relevant functions with readable
names, follow the data flow from the UI event to its network/filesystem
effect, and produce a call graph. Cite byte offsets in the bundle for every
hop.
```

### Live web feature mapping

```text
Observe <site> with REA while I use <feature> — via `capture_browser_scenario`
or an existing open target through `list_browser_targets`. List the
requests, scripts, and state changes involved, then summarize how the
feature works and which parts run client-side versus server-side.
```

### Full client API-surface mapping

```text
Enumerate the complete client API surface of <web app URL> (authorized
testing of an app I own or am permitted to assess): every endpoint the
bundled JavaScript calls, HTTP method, request/response shape, auth
header/token source, and the file + offset where each call is made. Group
by feature, flag anything validated only client-side, and separate
endpoints proven in code from ones only observed in traffic.
```

### Source-map recovery, or de-minifying by hand

```text
Check <web app> for exposed source maps (.map references in the bundles, or
probing known paths). If they exist, reconstruct the original module tree
and map it back to the shipped bundles. If they don't, de-minify the hot
modules for <FEATURE> yourself — rename locals, split the bundle into
logical modules, and produce a readable equivalent. State plainly which
names came from maps and which are inferred.
```

### Auth and token-flow tracing

```text
Document <web app>'s authentication flow end to end: where the login form
posts, how tokens are stored (cookie flags, localStorage, IndexedDB),
refresh/renewal logic, and which requests attach the token and how. Give
the bundle offset proving each step, and flag any client-side-only
authorization check as a hypothesis to confirm with a controlled request —
not as a finding on its own.
```

### WASM module inside a web app

```text
Inspect the WASM module in <web app> with `inspect_wasm_artifact`. Identify
the toolchain that produced it (Emscripten, wasm-bindgen, Go), map exported
functions to their JS callers, and recover the linear-memory layout and any
embedded strings/data tables. For <FEATURE>, trace the JS↔WASM boundary —
arguments crossing, return values, side effects — citing export indices and
JS call-site offsets.
```

### Browser-extension (MV3) audit

```text
Audit <extension folder / .crx> that I own or am authorized to review.
Enumerate manifest permissions, service-worker and content-script entry
points, every host it can reach, message-passing channels between
content-script/worker/page, and any remote code loading. Map each
capability to the file and function implementing it, and distinguish
declared permissions from behavior actually observed.
```

### Service-worker offline logic

```text
Recover <web app>'s service-worker behavior: the cache strategy per
resource class, what persists offline (IndexedDB stores and their schemas,
read from code), and how queued actions sync once connectivity returns.
Show the worker file and the functions proving each rule.
```

### Electron fuse check before attaching a debugger

```text
Before attempting an electron-main Inspector attach on <app>, check its
packaged fuses with `inspect_electron_fuses`. If `EnableNodeCliInspectArguments`
is disabled, say so and don't attempt `--inspect` — propose the static or
`list_electron_targets` alternative instead.
```
