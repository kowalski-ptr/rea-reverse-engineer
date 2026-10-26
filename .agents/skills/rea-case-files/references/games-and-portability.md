# Case file — games & portability

Gameplay logic, engine/asset pipelines, and porting a native game toward
WASM. These build on the native-binary workflow — see
[native-binaries-and-deobfuscation.md](native-binaries-and-deobfuscation.md) —
with the game's loop and asset pipeline as the organizing structure.

### Gameplay-logic recovery

```text
Reconstruct <GAME>'s gameplay logic from its executable, in C, and test it
against the original. For each mechanic (movement, physics, scoring,
spawning), show the function, the constants, and the update loop that
prove the rule.
```

### A difficulty or speed rule over time

```text
In <game>, why does <difficulty/speed> change over time? Show the exact
rule and the constants behind it with addresses, then build a small
standalone version with an adjustable parameter so I can tune it myself.
```

### Engine and asset-pipeline map

```text
Identify <game>'s engine or framework, the render loop, the update/tick
order, how levels and assets are loaded and serialized, and where gameplay
data actually lives (tables, scripts, archives). Map each component to
functions/addresses and walk the load pipeline step by step.
```

### Offline single-player data location (no multiplayer, no persistence tricks)

```text
In <offline single-player game>, locate the code that controls <weapon
stats / spawn rates / economy>. Show the data tables or functions with
addresses — I only want to understand where the values live, for my own
offline experimentation. No multiplayer, no live-session interaction, no
persistence tricks.
```

### Porting a native game toward WASM

```text
Analyze <game executable/source> as a WASM portability target. Map the main
loop and frame pacing, the rendering backend (OpenGL/DirectX/SDL/Vulkan
calls), filesystem/save paths, the threading model, audio output, input
handling, and any platform-specific APIs or inline assembly. For each,
classify it as portable as-is, needs a shim, or must be rewritten — and
cite the functions and call sites that prove the classification.
```

### Building the port plan

```text
From the REA findings for <game>, produce a WASM port plan: toolchain
choice (Emscripten vs. manual recompile) and the shims it needs
(GL→WebGL, SDL→SDL2-compat, pthreads→SharedArrayBuffer); the minimal set of
source modules to compile; how saves move to IndexedDB and assets to
fetch/streaming; the performance risks already found in the native code
(per-frame allocation, busy loops, 64-bit assumptions) with evidence; and a
milestone list ending in a playable `index.html`.
```

### Isolating the headless logic core

```text
Isolate <game>'s pure simulation logic (physics, AI, rules) from rendering
and OS calls. Extract the exact rules with evidence, then implement that
logic core in C/Rust compiled to WASM, with a test harness that replays
recorded inputs and compares state hashes against the original's observed
behavior.
```
