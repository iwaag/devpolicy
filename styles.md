# Development Styles

## AG Standard Style

### Project folders
pj-(project_name)/ ... Project folder

### Typical files and folders inside project folders (NOT rule, just a recommendation)
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

### Misc

- English is recommended for generated texts.
- Absolute local file paths are machine-specific: they do not resolve on other machines and reveal local layout, so tracked files usually use relative paths.
- Tool Implantation(To force agents to use tools and strip them of autonomy) is usually discouraged.
- Tool Giving(To give agents tools and autonomy) is encouraged.

### Agent guides

A conversational agent's guide opens with a short head: who the agent is, who it speaks with, what is assumed of it, and what it can see (the tools, each with what it tells you and how to get its `--help`). The body states the facts the agent works under, not procedures under conditions. Each fact lives in one place: a tool's usage in that tool's `--help`, text shared by several roles of one agent in that agent's shared file, text shared by several agents in the shared library they all use, and a role's own contract in its guide. A role that answers with files instead of speech keeps its output contract; its head says who reads the files and what it may consult. A paragraph written after a seen-live failure or a named trial keeps its trial reference wherever it moves.
