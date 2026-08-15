# `ag.agent-config.v1` specification

One language-neutral way for a project to say which agent **harness**
runs which **model** for which **role**, plus how a machine supplies its
local facts, and what every run records. The contract deliberately does
*not* cover prompts, charters, tool grants, permissions, working
directories, artifacts, or success judgment — those stay owned by each
project and role, and common settings must not turn them into one
deterministic workflow.

## 1. Files and precedence

- **Committed config** — `agents.toml` at a project-documented location
  (recommended: project root). Owned by the project, checked in.
- **Local overlay** — `agents.local.toml` under the project's `.local/`
  directory. Git-ignored. Carries only machine/deployment facts and the
  narrow overrides listed in §6.

Resolution reads the committed file first, then applies the overlay.
The overlay can never introduce new harnesses, models, profiles, or
roles. An overlay key outside its permitted scope is an error
(`E_OVERLAY_SCOPE`), not a silently ignored key. A missing overlay file
is fine; a missing committed file is an error (`E_SCHEMA`).

Both files start with the schema marker:

```toml
schema = "ag.agent-config.v1"
```

A file whose `schema` is absent or different is rejected (`E_SCHEMA`).

## 2. Canonical harness IDs

Closed vocabulary in v1 — configs reference these, never define them:

| harness | meaning | provider compatibility |
|---|---|---|
| `claude_code` | Claude Code CLI headless run (`claude -p`) | `anthropic` only |
| `agcode` | single-file agentic loop over the Anthropic Messages API, shipped with the reference implementation (`python -m agag.agcode`) | any provider serving a Messages API endpoint (`ollama`, `anthropic`, …) |
| `fake` | test-only stub; never invokes a real model | any |

Ollama is **not** a harness; it is a model provider a harness reaches.
Direct provider API calls (Ollama `/api/chat`, Anthropic Messages SDK) are
not harnesses in v1 and have no ID; migrating off them is the point of the
roadmap. **`agcode` is the one stated exception**: it
talks to a Messages API endpoint directly, with no third-party CLI in
between, and is still a harness because it is a complete agentic run — a
tool loop with a working directory, a turn budget, a wall-clock deadline,
and results normalized into §9 — rather than a bare model call. The rule
the carve-out preserves is that a *single request/response* is not a
harness; the ID belongs to the loop around it. Any other harness name is
`E_UNKNOWN_HARNESS`.

This closed vocabulary has changed twice, and neither change carries a
compatibility shim — `E_UNKNOWN_HARNESS` on either side is the correct
no-silent-fallback behavior (§8), and a consumer moves its pin and its
profiles together.

- `agcode` was **added** after the original IDs. An implementation predating
  the row rejects an `agcode` profile.
- `opencode` (`opencode run`, any configured provider) was **removed** once
  nothing ran on it. An implementation newer than that change rejects an
  `opencode` profile. Its intrinsic capabilities were `agentic_tools` and
  `workspace_fs`, the same pair `agcode` provides, so a profile that only
  needed those migrates by changing the harness name and the endpoint
  spelling: `agcode` posts to `{base_url}/v1/messages`, so a
  `local.provider.*.base_url` written for an OpenAI-compatible client loses
  its `/v1` suffix.

`fake` exists so conformance fixtures and deterministic tests can share
one spelling across projects. Runs recorded against `fake` must be
recognizable as test runs by the harness field alone.

## 3. Canonical model IDs

A model ID is `<provider>/<name>`:

- `provider` — lowercase `[a-z0-9_-]+`, e.g. `ollama`, `anthropic`;
- `name` — non-empty, provider-native spelling, may contain dots,
  colons, dashes (e.g. `qwen3.6:35b-a3b-coding-nvfp4`).

Examples: `ollama/qwen3.6:35b-a3b-coding-nvfp4`,
`anthropic/claude-sonnet-5`. A model reference without a provider
prefix is malformed (`E_BAD_MODEL_ID`).

Every model a config uses must be declared in its `[models]` table:

```toml
[models."ollama/qwen3.6:35b-a3b-coding-nvfp4"]

[models."anthropic/claude-sonnet-5"]
# optional, harness-interpreted knobs are allowed here, e.g.:
# effort = "low"
# max_tokens = 4096
```

Declaring a model does not promise it is pulled/available on a given
machine; it fixes the spelling. A profile referencing an undeclared
model is `E_UNKNOWN_MODEL`. When a harness needs the provider-native
name (e.g. `claude --model claude-sonnet-5`, `python -m agag.agcode --model
qwen3.6:...`), the loader derives it from the canonical ID; projects must not
store a second, unprefixed spelling.

## 4. Profiles

A profile names a compatible (harness, model) pair:

```toml
[profiles.local]
harness = "agcode"
model = "ollama/qwen3.6:35b-a3b-coding-nvfp4"

[profiles.sonnet]
harness = "claude_code"
model = "anthropic/claude-sonnet-5"
```

The standard cross-project profiles are `local` (agcode plus the declared
local Ollama coding model), `sonnet` (Claude Code plus the declared Anthropic
Sonnet model), and test-only `stub` (`fake` plus the declared local model).
An adopter may add a project-only profile when it has a real need, but every
standard profile it declares must retain that same harness/model identity.

Validation: the harness must be canonical (§2), the model declared
(§3), and the model's provider must be compatible with the harness per
the §2 table — e.g. `claude_code` + `ollama/...` is
`E_INCOMPATIBLE`. Profile names are project-chosen, but a name should
mean the same harness+model wherever it appears across projects; the
cross-project matrix in later phases checks exactly that.

## 5. Roles

A role is a project-owned agent identity that selects a profile:

```toml
[roles.generator]
profile = "local"
requires = []              # capability names, see §7
```

Known roles today: agforge `generator`; agautolab `front`, `director`,
`mediator`, `coding`, `summarizer`; agdevworld `front`. Roles are an
open set — projects add their own. A role referencing an undeclared
profile is `E_UNKNOWN_PROFILE`.

A project may let a specific run override a role's profile through its
own mechanism (e.g. a `profile:` key in an agautolab `job.yaml`); the
override value must still name a profile declared in `agents.toml` and
resolves under the same rules. What a role may *do* (tools,
permissions, prompts, cwd) is not expressed here.

## 6. Local overlay scope

The overlay may contain only:

```toml
schema = "ag.agent-config.v1"

# 1. Harness runtime facts
[local.harness.claude_code]
command_glob = "~/.vscode-server/extensions/anthropic.claude-code-*/resources/native-binary/claude"
# glob → newest match wins; no match is a resolution failure at run time

# 2. Provider endpoints
[local.provider.ollama]
base_url = "http://example-host:11434"   # bare base URL; agcode appends /v1/messages

# 3. Secret references (never values)
[local.secrets]
anthropic_api_key_file = "~/.secrets/anthropic"   # keys end in _file or _env
gitea_token_env = "GITEA_TOKEN"

# 4. Role→profile selection override
[roles.front]
profile = "sonnet"
```

Rules:

- Keys under `[local.secrets]` must end in `_file` (path to a file
  whose content is the secret) or `_env` (name of an environment
  variable). A key that embeds an apparent literal secret value is
  `E_SECRET_VALUE`. Secret values never appear in either file.
- A role override may only change `profile`, and only to a profile the
  committed file declares. Anything else in the overlay —
  new profiles, new models, new roles, harness definitions, prompt or
  tool settings — is `E_OVERLAY_SCOPE`.
- Overlay `[capabilities]` may extend `provides` (§7).

## 7. Capabilities

Some roles need something the harness alone cannot promise — e.g. an
external UI action channel that a browser applies, or the ability to
launch a nested harness run. Capabilities make that need explicit and
checkable instead of implicit.

- Harness-intrinsic capabilities (fixed by §2 semantics):
  `claude_code` and `agcode` provide `agentic_tools` and `workspace_fs`;
  `fake` provides whatever the test declares.
- Deployment-provided capabilities are declared in config:

```toml
[capabilities]
provides = ["ui_actions"]
```

  The committed file declares what the project's runtime always
  provides; the overlay may add what this particular deployment
  provides.

- A role's `requires` list must be covered by
  (harness-intrinsic ∪ declared `provides`); otherwise resolution fails
  with `E_CAPABILITY_UNMET`.

Capability names are an open vocabulary of plain strings compared by
equality. Names in use or foreseen: `ui_actions` (UI-only operations
such as `switch_view` / `show_image` are collected and applied by an
external client), `nested_harness` (the run may launch another harness
run, as agautolab's window→director path does), `service_http` (the
role is reachable as an HTTP service). Introduce new names in the
project that needs them; promote here once shared.

## 8. Failure behavior and error codes

Resolution and run-time selection fail loudly. **No silent fallback of
any kind**: an unknown or unavailable harness, model, provider
endpoint, or binary must produce an error naming the missing thing —
never a quiet substitution of a different harness or model.

Stable error codes (conformance tests compare these, not message
prose):

| code | condition |
|---|---|
| `E_SCHEMA` | missing/wrong `schema`, unparsable TOML, missing committed file |
| `E_UNKNOWN_HARNESS` | harness not in the §2 vocabulary |
| `E_BAD_MODEL_ID` | model reference without `provider/` prefix or malformed |
| `E_UNKNOWN_MODEL` | profile references a model absent from `[models]` |
| `E_INCOMPATIBLE` | profile pairs a harness with a provider it cannot serve |
| `E_UNKNOWN_PROFILE` | role (or per-run override) references an undeclared profile |
| `E_OVERLAY_SCOPE` | overlay contains anything outside §6 |
| `E_SECRET_VALUE` | overlay embeds a secret value instead of a reference |
| `E_CAPABILITY_UNMET` | role's `requires` not covered at resolution |
| `E_UNAVAILABLE` | resolved binary/endpoint absent at run time (fail, don't fall back) |

Messages should name the offending key and the allowed set; the code is
the contract, the prose is the courtesy.

## 9. Normalized run metadata

Extends `devpolicy/agent_records.md` (which fixes fields, not paths or
formats — each project keeps its evidence locations). Every agentic run
resolved through this contract records, in the project's existing
record shape:

| field | meaning |
|---|---|
| `role` | role name that ran |
| `profile` | profile name resolved (after any per-run override) |
| `harness` | canonical harness ID actually invoked |
| `provider` | provider segment of the model ID |
| `model` | full canonical model ID (`provider/name`) |
| `outcome` | `done` \| `failed` \| `aborted` |
| `duration_ms` | wall clock; harness-reported value wins when present |
| `cost_usd` | only when the backend reports it; missing is fine, invented is not |
| `usage` | token counts as reported (input/output, cache fields as available) |
| `num_turns` | when the harness reports it |
| `failure` | on failure: free-text in the agent's/harness's own words |

The legacy composite `backend_model` string (`"ollama/gemma3"`,
`"claude/claude-sonnet-5"`) is superseded by the separate
`harness`/`provider`/`model` fields; readers of pre-contract record
archives must expect the old spelling in old files. Raw transcripts
stay: normalization adds fields, it never replaces the raw output kept
for diagnosing a failed run.
