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
  (e.g. `opencode + ollama/<model>`, `claude -p + claude-sonnet-5`,
  an autolab adapter name + its model).
- **outcome** — done / failed / aborted.
- **cost / time** — when the backend reports them (USD cost, duration,
  turns, tokens). Missing values are fine; invented values are not.
- **on failure: a free-text report in the agent's own words.** The
  harness may fix only the path; the content is always the agent's.

Comparing or exploiting the records is out of scope here — recording is
the whole obligation.

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
