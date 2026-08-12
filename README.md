# HAPPI

> *"AI is a syscall. happi.md is the protocol."*

**One JSON envelope in, one NDJSON event stream out. Any tool, any provider, any
transport — one contract.**

`HAPPI.md` is the canonical reference for the Harnessed-AI Polyglot Protocol
Interface. Current version: **HAPPI/1.3**.

## The spec is the runtime

This file is simultaneously the specification, the schema, and a working
implementation. You can read it and execute it:

```bash
# identity + smoke
bash HAPPI.md

# dispatch a real envelope through the embedded runtime
echo '{"v":"happi/1.0","id":"hello","cmd":"version"}' | bash HAPPI.md run
```

```
{"v":"happi/1.3","id":"hello","type":"started","ts":0}
{"v":"happi/1.3","id":"hello","type":"delta","ts":0,"text":"happi/1.3"}
{"v":"happi/1.3","id":"hello","type":"completed","ts":0}
```

Self-bootstrapping: any machine with `bash` and `python3 >= 3.10` runs this file
as-is. No clone, no `pip install`, no other files needed for protocol mechanics.

## Why

Every AI application today is coupled to a vendor's SDK. Change provider and you
rewrite. That is the pre-POSIX world — software written for a machine rather than
for an interface.

A syscall is the boundary that fixed this once already. `open()`, `read()`,
`write()`: small, stable, universal, implementable by anyone. HAPPI is that
boundary for AI, so software written today keeps working when the models change.

## The four axioms

1. **CLI (stdio) is the canonical transport.** Every other transport is a shim
   that maps to this contract.
2. **Seven core event types are sufficient for streaming.** All known provider
   streaming semantics map onto them; provider-specific data lives in sub-fields.
3. **`sub_request` recurses through the same runtime.** The protocol is fractal;
   no privileged inner interface.
4. **The polyglot form IS the specification.** This document is the contract —
   no separate authoritative schema repo.

## Beyond dispatch

HAPPI carries provenance in the wire format rather than as an afterthought:

- **`idr`** — Intent Decision Record, a signed audit receipt (v1.1)
- **`context`** — a replayable, content-addressed memory-chain link (v1.1)
- **`cite.verify`** — deterministic citation provenance (v1.3). Every cited quote
  is checked verbatim against its source, so a fabricated citation cannot verify.

## Implementations

| SDK | Notes |
|---|---|
| [hal-go](https://github.com/CodeTonight-SA/hal-go) | stdlib only; static binary, byte-compatible with the reference runtime |
| [hal-py](https://github.com/CodeTonight-SA/hal-py) | Python |
| [hal-js](https://github.com/CodeTonight-SA/hal-js) | Node 20+ / Deno / Bun |
| [hal-conformance](https://github.com/CodeTonight-SA/hal-conformance) | fixtures plus spec-derived invariants |

Conformance is two-layered on purpose. Recorded fixtures alone are circular — they
were captured *from* a reference runtime, so re-recording them would turn a
regression green. The invariants are drawn from the spec prose instead and cannot
be re-recorded. Results are three-valued — `HOLDS` / `VIOLATION` /
`NOT-IMPLEMENTED` — so a missing feature never reads as assurance.

## Status

The protocol is stable and versioned; `happi/1.0` through `happi/1.3` envelopes
are all accepted, and emitted events carry the runtime's version rather than the
envelope's.

Known gaps, stated plainly:

- There is **no identity or addressing layer** in the envelope. `model_versions`
  is self-asserted by the emitting runtime, so HAPPI alone cannot establish who
  spoke. Attestation, if you need it, must come from outside the protocol.
- The OpenAPI `/v1/dispatch` network binding is specified but not yet implemented
  by any SDK. Dispatch today is subprocess-based, so there is no browser or edge
  runtime.
- `sub_request` (axiom 3) has no conformance fixture yet — the fractal claim is
  specified but untested.

## Licence

AGPL-3.0-only. If you run a modified version as a network service, that counts as
distribution — users of that service must be offered the modified source.
Commercial licences are available via the contact given in the specification.

Copyright (C) 2024-2026 Lourens Cornelius Scheepers / CodeTonight (Pty) Ltd
