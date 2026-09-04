---
name: linear
description: Linear CLI mechanics. Use for any `linear` command: flags, file-based markdown bodies, issues, relations, projects, documents, initiatives, and the GraphQL fallback when no flag covers the operation.
disable-model-invocation: false
allowed-tools: Bash(linear:*), Bash(curl:*)
---

How to drive the `linear` CLI. What to track, when to move an issue, and where documents live are other skills' decisions; this file is flags and traps.

## The repo pin

A repo is Linear-tracked when its root holds `.linear.toml`, which pins the workspace and team so commands need no `--workspace` or `--team` flags:

```toml
workspace = "<workspace-slug>"
team_id = "<TEAM-KEY>"
```

`linear config` generates it interactively. `linear auth whoami` checks the login; if it fails, the user runs `linear auth login` themselves.

## Markdown goes through files

Any flag that takes markdown has a file form. Use it for anything longer than one line; inline flags mangle newlines and shell-escape badly.

| Command | File flag |
|---|---|
| `issue create`, `issue update` | `--description-file` |
| `issue comment add`, `issue comment update` | `--body-file` |
| `project create` | `--content-file` (overview) |
| `document create`, `document update` | `--content-file` |

## Recipes

Query issues. `issue list` is an alias of `issue mine` and shows only your issues; `issue query` covers a team or project:

```bash
linear issue query --project "<project>" --state unstarted --json
linear issue query --team WEB --label Bug --updated-after 2026-01-01
```

Create an issue, one label and a priority, into a project:

```bash
linear issue create --title "<imperative title>" --description-file ./spec.md \
  --project "<project>" --label Feature --priority 3 --state Todo --no-interactive
```

Move state, comment, wire a blocking edge:

```bash
linear issue update WEB-12 --state "In Flight"
linear issue comment add WEB-12 --body-file ./comment.md
linear issue relation add WEB-14 blocked-by WEB-12
linear issue relation list WEB-14
```

Create a project under an initiative with an overview:

```bash
linear project create --name "<name>" --team WEB --initiative "<initiative>" \
  --description "<one sentence, 255 chars max>" --content-file ./overview.md --status planned
linear project update <project> --status started
```

Attach a document to an issue or project:

```bash
linear document create --issue WEB-12 --title Plan --content-file ./plan.md
linear document update <document> --content-file ./plan.md
```

Inline image in a comment:

```bash
linear issue comment add WEB-12 --attach ./screenshot.png
```

## Traps

- `issue comment create` does not exist; the verb is `add`.
- `issue start` creates and checks out a git branch as a side effect. Move state with `issue update --state`.
- `issue attach` makes a sidebar link and never renders an image inline; `comment add --attach` does.
- `project update` has no `--content` or `--content-file`. Changing an overview after creation is the GraphQL fallback below.
- `project --description` is capped at 255 characters by the API; the overview (`--content-file`) is not.
- `issue query --state` and `issue list --state` filter by state type, never by name: `Ideas` and `Backlog` are both `backlog`, `Todo` is `unstarted`, `In Preparation`, `In Flight`, and `In Review` are all `started`. Filter by type, then read `state.name` in the JSON.
- `--no-pager` exists only on `issue list` and `issue query`; other commands error on it.
- `issue list` needs `--team <key>` unless the repo pin supplies it.

## Reference

Every command's full help is in [references/commands.md](references/commands.md), one file per command. Curated examples for initiatives, labels, projects, and bulk operations are in [references/organization-features.md](references/organization-features.md). `linear <command> <subcommand> --help` is always current.

## GraphQL fallback

Reach for `linear api` only when no command or flag covers the operation. Find the shape in the schema first:

```bash
linear schema -o "${TMPDIR:-/tmp}/linear-schema.graphql"
grep -A 30 "^input ProjectUpdateInput" "${TMPDIR:-/tmp}/linear-schema.graphql"
```

A query with non-null markers (`String!`) goes through a heredoc; inline quoting breaks on the `!`:

```bash
linear api '{ viewer { id name } }'

linear api --variable id=<project-id> --variable content="$(cat ./overview.md)" <<'GRAPHQL'
mutation($id: String!, $content: String!) {
  projectUpdate(id: $id, input: { content: $content }) { success }
}
GRAPHQL

linear api --variables-json '{"filter": {"state": {"name": {"eq": "In Flight"}}}}' <<'GRAPHQL'
query($filter: IssueFilter!) { issues(filter: $filter) { nodes { identifier title } } }
GRAPHQL
```

Raw HTTP, when the CLI's `api` command is not enough:

```bash
curl -s -X POST https://api.linear.app/graphql \
  -H "Content-Type: application/json" \
  -H "Authorization: $(linear auth token)" \
  -d '{"query": "{ viewer { id } }"}'
```
