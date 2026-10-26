---
name: rea-contract-design
description: Design or change REA investigation tools, CLI/MCP contracts, provider capabilities, and Evidence semantics.
---

# REA contract design

A tool's contract — its boundary, input/result shape, and Evidence semantics —
is the thing every caller and provider depends on. Change it carelessly and
every consumer inherits the ambiguity. This skill is for the moment you're
adding, splitting, or reshaping a contract, not for using one.

## Before proposing anything

Read the [tool-design guide](../../../docs/tool-design.md) for tool
boundaries, contract semantics, provider ownership, and discoverability.
Then inspect the *current* contract and the nearest existing tool that
already answers something close to the analyst's question — source,
documentation, and live catalogs can disagree about what's actually shipped,
so trust the code you're changing over the page describing it.

## Shape the proposal

Return, in this order:

1. **Boundary** — what this tool owns, and what it explicitly defers to a
   neighbor or caller.
2. **Input/result shape** — the exact fields, not a paraphrase of them.
3. **Evidence semantics** — what gets retained, under what identity, and for
   how long.
4. **Nearest alternative** — the existing tool or composition this would
   replace or sit beside, and why it isn't enough.
5. **Verification approach** — how you'll know the contract holds under real
   use, not just the happy path.

Implement only once that shape is agreed, and keep the user's requested scope
and existing authorization — a design skill is not a blanket license to widen
either.

## Carry it through

For implementation or verification, consult [testing](../../../docs/testing.md)
for the affected consumer and provider lanes, and
[contributing](../../../CONTRIBUTING.md) for generated-file ownership. Pull in
a provider or workflow guide only when the task actually touches it — don't
read ahead of what the change needs.
