# Conformance examples index

Every loader implementation should reach the outcomes below. Invalid
examples state their expected error code (`spec.md` §8) in a header
comment; conformance compares codes, not message prose.

## Valid

| example | demonstrates |
|---|---|
| `valid/agforge/agents.toml` | single `generator` role, both standard harnesses, `fake` stub profile |
| `valid/agforge/agents.local.toml` | overlay with command path, command glob, provider endpoint, secret file reference |
| `valid/agautolab/agents.toml` | five roles selecting profiles independently; `nested_harness` capability declared and required |
| `valid/agautolab/agents.local.toml` | overlay role→profile override (window flipped to the local model) |
| `valid/agdevworld/agents.toml` | `ui_actions` capability provided and required; per-model knobs (`effort`, `max_tokens`) |

Overlay examples use placeholder paths/hosts; real machines put real
values in their own git-ignored `agents.local.toml`.

## Invalid

| example | expected code |
|---|---|
| `invalid/missing-schema.toml` | `E_SCHEMA` |
| `invalid/unknown-harness.toml` | `E_UNKNOWN_HARNESS` |
| `invalid/bad-model-id.toml` | `E_BAD_MODEL_ID` |
| `invalid/unknown-model.toml` | `E_UNKNOWN_MODEL` |
| `invalid/incompatible-model.toml` | `E_INCOMPATIBLE` |
| `invalid/unknown-profile.toml` | `E_UNKNOWN_PROFILE` |
| `invalid/overlay-out-of-scope.toml` (an overlay file) | `E_OVERLAY_SCOPE` |
| `invalid/overlay-secret-value.toml` (an overlay file) | `E_SECRET_VALUE` |
| `invalid/capability-unmet.toml` | `E_CAPABILITY_UNMET` |

`E_UNAVAILABLE` has no file example: it is a run-time condition (a
resolved binary or endpoint missing on the machine), exercised by each
project's own tests, not by a config fixture.
