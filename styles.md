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
devdocs/runs/(workflow)/(run)/ ... A workflow run's records and documents (plan.md, report.md, ...), written by WFE and the run's agent

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

### Keeping a project tidy

A project is tidy when a reader learns what it is and does now from its entry, without reverse-engineering
its source and without mistaking one run's record for the whole project (tidy p1: ponderer reported
protoprey's adopted wolf scene as missing after a source search found no GLB, while a root `report.md` left
by an unrelated run said "no game code was played"; devdocs/episodes/tidy/p1/).

- One maintained entry, `README.md`, says what the project does now: capabilities, what it offers other
  projects, how to set it up and run it, where it was tested, known limits. History is linked and labelled
  as history, not mixed into the current account.
- A run's or an episode's record stays in its own folder (`devdocs/runs/...`, `devdocs/episodes/...`). A root
  `report.md`, `answer.md` or `review.md` reads as the project's own and is overwritten by the next run that
  writes one. Moving a record keeps its content and provenance and updates what refers to it.
- Capability, installed bytes and verification are separate facts. An adopted delivery is part of the
  project while its bytes live in an ignored folder; a source search that finds nothing is not evidence
  that a feature is absent.
- Generated caches and build outputs (a Godot project's `.godot/`, `__pycache__`, ...) are ignored rather
  than tracked once the sources reproduce them. Intentional media and evidence stay.
- Facts of one machine belong to local files and context mechanisms; portable text states what is
  required, not which host has it. Records whose domain is machines (a cluster's) keep their evidence.
- Domain folders (`knowledge/`, `lab/`, `study/`, `wedo/`, archives, named deliverables) are legitimate.
  Tidying repairs demonstrated ambiguity, misplaced records, broken references and irreproducible state,
  not differences from the example layout, and never rewrites history.

### Project cooperation

Projects cooperate through published deliveries, not through each other's checkouts (project_coop p1:
protoprey adopted gimmickimg's and playprey's results; devdocs/episodes/project_coop/p1/).

- A producer says what it offers in `README.md` "For other projects" (what, how to ask, what arrives, limits),
  linking its delivery contract. Its delivery tool builds a folder with `delivery.json`: format and version,
  identity and revision, producer commit and tree state, every file by sha256, how to use it. The folder is
  published as a WFE run output (`wfe run publish`). How a consumer embeds it is a separate contract, shipped
  with the delivery when it needs tooling.
- A consumer keeps `docs/cooperation.md` (who offers what, what a request needs, how it adopts) and a tracked
  adoption record (identity, manifest hash, source reference, install place, what it replaced). The bytes stay
  on the shared area; a copy obtains them by `wfe share resolve`, a remapped share root or an explicit package,
  checks every hash, and installs them into an ignored folder. Where and how a delivery appears, and its
  tuning, are the consumer's own data.
- Endpoints, share locations and host tools are environment facts (host configuration, scoped context,
  environment variables), not tracked code. A consumer that delegates generation needs no endpoint.
- A replacement is another adoption; the previous identity stays in the record, and adopted outputs are kept.

### Reporting and accepting results

A result that is handed over is a claim, and its record is the evidence. In tidy p1 a receiving agent accepted a
report that was never written and a README with one host's addresses within seconds, from the outcome line
(devdocs/episodes/tidy/p1/, p2/).

- The producer's report says what changed and at which revision, which checks actually ran and their results,
  where the report and artifacts are, and what is unverified, deliberately unchanged or open. Work that changes
  what readers rely on (entry, guide, setup, deliveries) keeps them true in the same work; a tidy pass reports
  evidence rather than repeating itself.
- The receiver compares the claim with the documents, artifacts and verification records before accepting or
  summarizing it. What it cannot check it inspects, or says it did not. Something missing goes back to its owner,
  and the corrected result is checked again: an earlier judgement does not cover a newer result.
- Who found a defect, who asked for the work and who reviewed the result are separate facts. A receiver's own finding
  is recorded as its own, with what it checked, even when it reopens its own earlier acceptance; a go-ahead or an
  instruction is neither the finding nor a review (tidy p3: a correction citing a go-ahead read as the person's review;
  devdocs/episodes/tidy/p3/).
- A summary of several commissioned results accounts for each one: its disposition and its remaining limits.
- Records state facts and the gaps in them. A reminder or a tidy step is evidence that the question was asked,
  not that it was answered well. Neither its presence nor its absence proves quality or a defect.

### Agent guides

A conversational agent's guide opens with a short head: who the agent is, who it speaks with, what is assumed of it, and what it can see (the tools, each with what it tells you and how to get its `--help`). The body states the facts the agent works under, not procedures under conditions. Each fact lives in one place: a tool's usage in that tool's `--help`, text shared by several roles of one agent in that agent's shared file, text shared by several agents in the shared library they all use, and a role's own contract in its guide. A role that answers with files instead of speech keeps its output contract; its head says who reads the files and what it may consult. A paragraph written after a seen-live failure or a named trial keeps its trial reference wherever it moves.
