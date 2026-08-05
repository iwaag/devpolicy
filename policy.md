# Project folders
pj-(project_name)/ ... Project folder

# Typical files and folders inside project folders (just a recommendation)
README.md
README_DEV.md ... Dedicated for developers
devdocs/episodes/(desire_title)/braindump/ ... Contains whatever describes the developer's desire
devdocs/episodes/(desire_title)/braindump.txt ... simple one-file braindump
devdocs/episodes/(desire_title)/roadmap.md ... Roadmap to fulfill the developer's desire. For heavy, long running episodes.
devdocs/episodes/(desire_title)/plan.md ... Plan to fulfill the developer's desire
devdocs/episodes/(desire_title)/p[phase number]/plan.md ... Plan for each phase in a roadmap
devdocs/episodes/(desire_title)/p[phase number]/report.md ... Final report for each phase in a roadmap
devdocs/episodes/(desire_title)/p[phase number]/report[step number].md ... Report for each step in a plan

devenv/ ... Contains resources for local development (such as Docker Compose)

Plan and roadmap are optional. Report is strongly recommended.

.local/ ... Contains local-only files. Always ignore in version control systems.
.local/devenv.md ... Contains information about how to develop the project locally
.local/.env

# Misc

- English is recommended for generated texts.
- Don't include absolute local file path inside non-ignored files without proper justification and user's approval