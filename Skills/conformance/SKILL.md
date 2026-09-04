---
name: conformance
description: Audit a change set against the requirement that originated it. Not a code review; never run unasked.
disable-model-invocation: true
---

Read-only audit of whether a change set does what its originating requirement asked, no more and no less. Non-goals, which belong to the repository's ordinary review: code quality, style, security, test adequacy, and general defects. Fix nothing, propose no fixes, and never reconstruct requirements the source does not state.

## 1. Pin the comparison

1. **Get a reference.** The user supplies a commit, branch, tag, or any revision expression. With none, ask and do not start; never fall back to `HEAD`'s parent or a guessed base branch.
2. **Resolve it.** `git rev-parse --verify <ref>^{commit}`; on failure, report that the reference does not resolve and stop.
3. **Pin the diff.** `git diff <ref>...HEAD`, three dots so Git diffs from the merge base; never the two-dot form. Record it: every later look at the change is this command, narrowed with `-- <path>`, never a fresh comparison. If `git status --porcelain` is nonempty and the user asked about their working state, say the audit stops at `HEAD` and let them commit first.
4. **Get the commit list.** `git log --oneline <ref>..HEAD`. Record it beside the diff command for the scout brief.
5. **Check for changes.** `git diff --stat <ref>...HEAD`; if it prints nothing, report an empty comparison and stop.

Done when the reference resolves, both commands are recorded, and the pinned diff is nonempty.

## 2. Find the authoritative source

The source is what originated the work. Commit messages and the PR body point at it and are never it. Try these locations in order and take the first that yields one:

1. **Linked work items.** Collect every reference on the change: issue keys (`WEB-12`), `Linear-issue:` trailers, and `Fixes #N` or `Closes #N` lines across the whole commit list, `linear issue id` for the branch, and `gh pr view` for issues linked from the PR. Retrieve each in full with `linear issue view <ID> --json` (read `linear` for mechanics first) or `gh issue view <N> --comments`; a comment that amends the requirement governs. Then judge which item the commits deliver against; never take the first key and skip the rest. A Linear issue's description is the requirement contract; the Plan document attached to it is the route, not a source.
2. **A PRD the user supplied.** Authoritative when the user says it originated the work.
3. **A local spec path the user supplied.**
4. **The working tree.** A file whose name or headings share words with the branch or feature, searched in order: `agent-docs/` (intents and archive), then `docs/`, `specs/`, `spec/`, `design/`, `rfcs/`, then root-level markdown. Use `glob` with gitignore off so untracked planning files count.
5. **The user.** Ask them to name it. If they cannot, report that no authoritative specification is available and stop; never reconstruct requirements from the code, the tests, the PR description, or what the feature appears to be for.

Done when exactly one source has been read in full, amendments included.

## 3. Brief one scout

Dispatch exactly one read-only `scout` through `task`, as a single-item batch. Give it the commands, not the diff, and the source by path or as the complete retrieved text, never a summary.

```markdown
# Target
Pinned diff: `git diff <ref>...HEAD` (narrow with `-- <path>`; never diff against anything else)
Commits: `git log --oneline <ref>..HEAD`
Source: <path, or the full text pasted below>

# Change
Compare the source's requirements to the pinned diff, nothing else. Report candidates in three classes only:
1. Omitted or partial: a stated requirement the diff does not implement, or implements incompletely.
2. Unrequested: behavior the diff adds that no requirement asked for, however well built.
3. Incorrect: a requirement the diff implements in a way that contradicts what it states.
Do not report code quality, style, naming, tests, security, performance, design, or bugs unrelated to a stated requirement.

# Acceptance
Each candidate carries its class, the requirement quoted in full with its location in the source, the file and hunk (`@@` header or line range) in the pinned diff, and one sentence stating the disagreement; drop any candidate missing one of these. Return candidates, not conclusions.
```

Done when the candidate list is back; nothing in it is a finding yet.

## 4. Verify every candidate yourself

A candidate is confirmed against both the source and the diff or it is gone:

- **The citation.** Reread it in context and confirm it is an operative requirement, not background, an example, a rejected alternative, an open question, or work marked deferred or out of scope.
- **The hunk.** `git diff <ref>...HEAD -- <file>`; confirm it says what the scout said it says.
- **Omitted or partial.** Search the whole pinned diff before accepting the omission; another file, helper, config entry, migration, or test fixture may carry the behavior. `git diff <ref>...HEAD --stat` lists every touched file.
- **Unrequested.** Confirm the behavior is new, not pre-existing code moved, renamed, or reformatted: `git diff <ref>...HEAD -M -w -- <file>`, and for a block that still looks added, `git grep -n "<a distinctive line>" $(git merge-base <ref> HEAD)`.
- **Incorrect.** The contradiction is between the diff and the requirement's text, not between the diff and how you would have built it; a preference is a review comment, not a finding.
- **Drop what does not confirm.** No "possible", "worth checking", or "the scout suggested" reaches the report, and dropped candidates are never mentioned.

A conforming verdict is also a claim: walk the source's acceptance criteria once yourself against the pinned diff; the scout's silence on one is not evidence.

Done when every candidate is confirmed or discarded and every acceptance criterion has been checked once.

## 5. Report

Order findings by impact; at comparable impact, omitted or incorrect outranks unrequested.

```markdown
Verdict: <one line: N findings against <source id>, or conforms>
Source: <Linear WEB-12 description plus comment of <date> / GitHub #N / path>
Comparison: `git diff <ref>...HEAD`

## Findings

1. <Omitted | Partial | Unrequested | Incorrect>. <one line naming the disagreement>
   Requirement: "<the requirement quoted in full, enough to keep its meaning>" (<source id, section or comment>)
   Change: <what the diff does or fails to do, one or two sentences>
   Location: `<file>` `@@ <hunk header>` (or line range)

Summary: <N> confirmed findings; highest impact is #1, <one clause>.
```

A citation shortened until it loses a condition is a misquote. With no confirmed findings, replace the findings section with one sentence: the pinned diff conforms to `<source>` as written. An unresolved reference, an empty diff, or no available source is reported as only that blocking result, with no verdict.

Done when every template field is filled, or the conforming sentence stands in place of the findings.

## Routing

`shape` for an unclear problem statement; `intent` for capturing a wanted outcome; `spec` for turning an intent into the Linear spec; `plan` for implementation breakdown; `software-design` for architecture and test-seam questions; `shakedown` for adversarial pressure on a proposal; `pr` for pull-request preparation; `handoff` for transition material; `natural-english` for the report's prose, never its scope; `linear` for Linear command mechanics.
