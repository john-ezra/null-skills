---
name: pr
description: Pull request structure. Use when opening a pull request or titling one.
disable-model-invocation: false
---

Once merged, the PR's title and body are the permanent record of what changed and why. Write both so a reader who has never seen the conversation needs nothing else.

## Title

Bare imperative. No type prefixes (`feat:`, `fix:`), no identifiers, no trailing period. It must pass the test:

> if you apply this PR it will …

"Add rate limiting to the login endpoint" passes. "Login fixes", "feat: rate limiting", and "Rate limiting" fail.

## Body

Carry this template verbatim, replacing the angle-bracket guidance:

```markdown
## What & why

<2 to 4 sentences: what changed and why it was needed.>

## Decisions

<Anything a reviewer would question: the approach chosen over alternatives, tradeoffs accepted, divergence from the plan. Link design decision records where the project keeps them. "None" is a legitimate entry.>

## Verification

<What was actually run and what was observed: commands, output, the screenshot compared. Evidence, not assertion: "tests pass" fails this section; "`bun test auth`, 14 pass, 0 fail" passes. Where the work has a spec, one line per acceptance criterion: the check and what it showed.>
```

Done when: the title passes the apply-test with no prefix, every body section is filled rather than deleted, and Verification cites observed output, one line per acceptance criterion where a spec exists.
