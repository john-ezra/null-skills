---
name: lifecycle
description: Where Null's work lives in Linear and what moves when. Reference, loaded on request.
disable-model-invocation: true
allowed-tools: Bash(linear:*)
---

How the skills bind to Linear. Each skill produces an artifact; this reference says where it lives and which state moves. Rigor for each artifact is the skill's; command flags and traps are the `linear` skill's. Every Linear write here happens on the user's word. Approval of spec content is not permission to publish it or change Linear state unless the user's request also authorizes those writes.

## The map

| Level | Linear | Artifact | Home |
|---|---|---|---|
| Venture | Initiative and team | Standing purpose and constraints | Initiative description |
| Finite effort | Project | Intent | `agent-docs/intents/<slug>.md` in the venture's repo, linked from the project overview |
| Unit of work | Issue | Spec | Issue description |
| Route | Document on the issue | Plan | Linear Document attached to the issue |
| Record | PR and close-out comment | `pr` skill, close-out below | GitHub, issue comment |

## Events

| Event | Skill | Artifact goes | Linear moves |
|---|---|---|---|
| Work arrives | sizing rule below | an issue or a project | `Ideas` if it is not yet sized or specced |
| Project shaped | `shape`, then `intent` | `agent-docs/intents/<slug>.md`, landed on main | project created at `planned`: name is the H1, description is the Goal's first sentence, overview is the link to the file on main, team and `--initiative` are the venture's |
| Intent sliced | `spec` | issue descriptions, the `Source:` line dropped since `--project` carries the intent association | placeholder to `Backlog`, approved spec to `Todo`, each edge `relation add <id> blocked-by <blocker>`, one label and a priority each |
| Standalone requirement approved | `spec`, standalone path | team-level issue description with a `Source:` link to the approved report or requirement in the first line, no project or intent required | approved spec to `Todo`, one label and a priority |
| Issue picked up | `plan`, unless trivial by its own test | Linear Document on the issue, title `Plan` | issue to `In Preparation`; the project's first pickup also moves it to `started` |
| Building | | branch, as the repo makes branches | issue to `In Flight` |
| PR opens | `pr` | the PR | issue to `In Review` |
| PR merges | | close-out comment | issue to `Done` |
| Project complete | | intent moved to `agent-docs/archive/<slug>.md`, landed on main | overview link repointed (the `projectUpdate` recipe in the `linear` skill), project to `completed` |

## States

No integration moves an issue; every transition is one `linear issue update <ID> --state "<Name>"` at the moment its event fires.

| Event | State |
|---|---|
| Captured, not yet sized or specced | `Ideas` |
| Placeholder published: a title and one sentence | `Backlog` |
| Spec written and approved | `Todo` |
| Picked up: plan | `In Preparation` |
| Building | `In Flight` |
| PR opens | `In Review` |
| PR merges | `Done`, plus the close-out comment |
| Dropped, superseded, or absorbed by a re-slice | `Canceled`, or `Duplicate` when another issue already covers it |

Every issue gets one type label and a priority at creation. Labels: `Bug`, `Feature`, `Improvement`, `Chore`, `Audit`. Priority 1 Urgent drops everything, 2 High is next up, 3 Medium is planned work and the default, 4 Low is nice to have.

The close-out comment is one line, `Done in PR #NN (<merge sha>).`, posted with `issue comment add --body-file`. One extra sentence only when what shipped diverged from the spec; rationale lives in the PR.

## Rules

- Sizing. One session of work is an issue carrying a spec; more is a project carrying an intent. Same size test as a `spec` slice.
- An unplanned issue attaches to an active project only when it blocks that project's stop criteria. Otherwise it is team-level with no project. Use `spec`'s standalone path from its approved bug report, GitHub issue, or explicit requirement, and keep a link to that source in the first line of the body. Once the spec is approved, publish it and move the issue to `Todo` only on the user's word.
- Promotion runs both ways. An issue that outgrows a session gets an intent and a project with itself as the first spec; a project that collapses to one slice is canceled and the issue kept.
- Each fact has one home. The intent cites the initiative for standing constraints. Blocking order is native relations, never prose in a body. The intent is linked from Linear, never copied into it.
- Publish blockers first, so every edge names a real id.
- Re-slice flat. Every slice is a top-level issue; the hierarchy is the blocking graph. When the frontier reaches a placeholder that is more than one session, re-scope it into the first real slice, create the siblings, and re-examine every edge that pointed at the placeholder: it meant "blocked by all of it," and each dependent now needs a specific sibling.
- The plan is frozen at pickup. A changed route is recorded in the PR's Decisions section, never edited into the Document.
- The Linear move is always `issue update --state`; `issue start` is not used.
- Completion is the operator's call. When every issue is terminal and the stop criteria appear met, say so and ask. On the word, the three completion actions happen together, in the order the Events row gives.
- Ventures have one repo. When one has several, ask which holds the intent.

## Not bound here

`shakedown` runs at any gate on request. `handoff` writes its file and nothing to Linear.
