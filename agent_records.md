# Agent run records (common recording policy)

One shared convention every agent harness can follow when it performs an
agentic run. This policy fixes *fields*, not paths or formats — each
workspace keeps its existing evidence location and file shapes
(e.g. agforge: `.local/problems/` and `.local/out/` transcripts;
autolab: per-job `evidence/` dirs).

Per agentic run, record:

- **request/job id** — whatever identifies the run in that workspace
  (a request_id, a job name + iteration number).
- **backend** — the model + harness that served the run
  (e.g. `agcode + ollama/<model>`, `claude -p + claude-sonnet-5`,
  an autolab adapter name + its model).
- **outcome** — done / failed / aborted.
- **cost / time** — when the backend reports them (USD cost, duration,
  turns, tokens). Missing values are fine; invented values are not.
- **on failure: a free-text report in the agent's own words.** The
  harness may fix only the path; the content is always the agent's.
- **what was asked for**, when somebody asked. Since `runtime-profile`
  (2026-09-09) a conversation may name a public **execution option**
  (`ag.exec-options.v1`) and the run records it — the option, where the
  selection came from, and the message it was frozen from — *beside* the
  backend, never instead of it. The option is a request; the backend is the
  fact, and a record that carried only one of them cannot answer either
  "why did this run on agy" or "what actually served it".

Comparing or exploiting the records is out of scope here — recording is
the whole obligation.

A recorded field is only recorded if it reaches the **file**. `runtime-profile`
step5 shipped the execution option into the dict a run returned and not into
the record on disk, because the writer copies an allowlist and nobody had
added it there; every test asserted on the returned dict. Assert on what is
written.

## Existing precedents (this policy generalizes what they already do)

- agforge `service/agent_run.py`: request_id, `meta.backend`, job
  `status` (done/failed), `total_cost_usd` / `duration_ms` /
  `num_turns` when the backend reports them, raw transcript per run,
  and on honest failure a `problem.md` written by the agent
  (path-only rule, content the agent's own words).
- autolab job dirs: job name + `iter-NNNN`, `job.yaml` `adapter:`
  (model detail in `adapter_result.json` `modelUsage`), `state.json`
  status, `adapter_result.json` cost/usage/duration, and the agent's
  raw output kept as `adapter_output.txt`.
