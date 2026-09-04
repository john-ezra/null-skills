---
name: pr
description: Pull request structure. Use when opening a pull request or titling one.
disable-model-invocation: false
---

Once merged, the PR's title and body are the permanent record of what changed and why. Write both so a reader who has never seen the conversation needs nothing else.

## Ground

Nothing is drafted from memory of the work. Before the title:

1. **The change.** `git diff <base>...HEAD` and `git log --oneline <base>..HEAD`, where `<base>` is the branch the PR targets. Read the whole diff, not the last edit; the body describes everything the reviewer will see.
2. **The requirement.** The issue, spec, plan, or intent the work delivers against: issue keys and `Fixes #N` lines in the commits, a spec path the user named, or the plan the branch followed. Read it in full (`linear` for Linear mechanics). Its acceptance criteria become the Verification lines, and its route is what Decisions measures divergence from. With none, say so in Decisions rather than inventing one.
3. **The project's template.** Look for `.github/PULL_REQUEST_TEMPLATE.md`, `.github/PULL_REQUEST_TEMPLATE/`, `docs/PULL_REQUEST_TEMPLATE.md`, and `PULL_REQUEST_TEMPLATE.md` at the root. One that exists wins: fill its sections, and put the What & why, Decisions, and Verification content under its nearest matching headings, adding a heading only where it has none.
4. **The evidence.** The verification commands and their observed output, from this session or run now. A result nobody observed does not go in the body.

Done when the diff, the commit list, the requirement or its absence, the template or its absence, and the observed verification output are all in hand.

## Title

Bare imperative. No type prefixes (`feat:`, `fix:`), no identifiers, no trailing period. It must pass the test:

> if you apply this PR it will …

"Add rate limiting to the login endpoint" passes. "Login fixes", "feat: rate limiting", and "Rate limiting" fail.

## Body

With no project template, carry this one verbatim, replacing the angle-bracket guidance:

```markdown
## What & why

<2 to 4 sentences: what changed and why it was needed.>

## Decisions

<Anything a reviewer would question: the approach chosen over alternatives, tradeoffs accepted, divergence from the plan. Link design decision records where the project keeps them. "None" is a legitimate entry.>

## Verification

<What was actually run and what was observed: commands, output, the screenshot compared. Evidence, not assertion: "tests pass" fails this section; "`bun test auth`, 14 pass, 0 fail" passes. Where the work has a spec, one line per acceptance criterion: the check and what it showed.>
```

Done when: the grounding step has run, the title passes the apply-test with no prefix, every body section is filled rather than deleted, the project's template was used where one exists, and Verification cites observed output, one line per acceptance criterion where a spec exists.
