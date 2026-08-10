# Agent configuration contract (`ag.agent-config.v1`)

This directory is the canonical home of the common agent-configuration
contract shared by `agforge`, `agautolab`, and `agdevworld`. It is
**contract-only**: format specification, field semantics, explanatory
documentation, and illustrative examples. Scripts, loaders, generated
data, live configuration, machine-specific values, and test artifacts
belong to the implementing projects and their test suites, never here.

## Files

- `spec.md` — the `ag.agent-config.v1` specification: TOML shape,
  canonical names, profile resolution, committed/overlay precedence,
  capability declaration, failure behavior, and normalized run metadata.
- `examples/` — shared conformance examples.
  - `examples/valid/` — one directory per known adopter shape, each a
    committed `agents.toml` (some with an `agents.local.toml` overlay).
  - `examples/invalid/` — one file per failure class, each with the
    expected error code in a header comment.
  - `examples/README.md` — index of every example and its expected
    outcome.

## How projects use this

Each project implements its own loader in its own language (Python for
agforge/agautolab, JavaScript for agdevworld) and may copy the examples
into its fixtures. Cross-project conformance means: given the same
example file, every loader reaches the same accept/reject decision and,
on rejection, the same error code from `spec.md` §8. Conformance tests
live with the implementations; drift between loaders must show up there,
not be patched here silently.

## Versioning

The schema string inside every config file is `ag.agent-config.v1`.
Breaking changes get a new schema string and a new spec document;
additive clarifications amend `spec.md` in place. Related workspace
policy: `devpolicy/agent_records.md` (run-record fields this contract
normalizes further).
