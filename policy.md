# Project folders
pj-(project_name)/ ... Project folder

# Typical files and folders inside project folders (just a recommendation)
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

# refs
devpolicy/terms.md ... Read only when you need to check terminologies