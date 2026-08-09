# Project folders
pj-(project_name)/ ... Project folder

# Typical files and folders inside project folders (NOT rule, just a recommendation)
README.md
README_DEV.md ... Dedicated for developers
devdocs/todo_done.md ... to memorize todo and done
devdocs/episodes/(some folders)/braindump/ ... Contains whatever describes the developer's desire
devdocs/episodes/(some folders)/braindump.txt(or .md) ... simple one-file braindump
devdocs/episodes/(some folders)/roadmap.md ... Roadmap to fulfill the developer's desire. For heavy, long running episodes.
devdocs/episodes/(some folders)/plan.md ... Plan to fulfill the developer's desire
devdocs/episodes/(some folders)/p[phase number]/plan.md ... Plan for each phase in a roadmap
devdocs/episodes/(some folders)/p[phase number]/report.md ... Final report for each phase in a roadmap
devdocs/episodes/(some folders)/p[phase number]/report[step number].md ... Report for each step in a plan
devdocs/ent-episodes/ ... Easier Next Time episodes to improve workflow
devdocs/ent-episodes/(some folders)/problem.md ... ENT Episode often begis from problem explanation

devenv/ ... Contains resources for kick-starting local development (such as Docker Compose)

Plan and roadmap are optional. Report is strongly recommended.

.local/ ... Contains local-only files. Always ignore in version control systems.
.local/devenv.md ... Contains information about how to develop the project locally
.local/.env

# Misc

- English is recommended for generated texts.
- Don't include absolute local file path inside non-ignored files without proper justification and user's approval
- Tool Implantation(To force agents to use tools and strip them of autonomy) is usually discouraged.
- Tool Giving(To give agents tools and autonomy) is encouraged.

# Agent guidelines (recommendations, not enforcement)

## Single Entrance
- An agent has exactly one desire-accepting conversational window.
- Extra endpoints are fine when they are deterministic (evidence reads, auth machinery) — never a second place to express desire.
- Auth may split entrances (cagent-style), but identity, not endpoint shape, decides what a request may do.

## Entrance Guide
- Every window should answer capability and cost questions ("what can you do", "what does it cost"), like `--help` on a CLI.
- Tentative values and "unknown" are acceptable answers; absence of the Q&A form is not.

## Agent ≠ Model
- The backend model/harness is a swappable parameter of an agent, never its identity.
- Every agentic run records which backend served it. See devpolicy/agent_records.md for the common record.

## Deus Ex Machina note
- When the Omni Agent performs work that belongs to an in-system agent, leave a one-line note in the episode doc: "did X for agent Y — handoff candidate".
- Perhaps positive for the mission, perhaps negative for workflow growth; the note is the whole obligation.

# refs
devpolicy/terms.md ... Read only when you need to check terminologies