---
name: conformance
description: Audit a change set against the requirements that originated it. Not a code review; never run unasked.
disable-model-invocation: true
---

Read-only audit of whether a change set does what its originating requirements asked, no more and no less. Non-goals, which belong to the repository's ordinary review: code quality, style, security, test adequacy, and general defects. Fix nothing, propose no fixes, and never reconstruct requirements the authoritative sources do not state.

## 1. Pin the comparison

1. **Get a reference.** The user supplies a commit, branch, tag, or any revision expression. With none, ask and do not start; never fall back to `HEAD`'s parent or a guessed base branch.
2. **Resolve it.** `git rev-parse --verify <ref>^{commit}`; on failure, report that the reference does not resolve and stop.
3. **Pin the diff.** `git diff <ref>...HEAD`, three dots so Git diffs from the merge base; never the two-dot form. Record it: every later look at the change is this command, narrowed with `-- <path>`, never a fresh comparison. If `git status --porcelain` is nonempty and the user asked about their working state, say the audit stops at `HEAD` and let them commit first.
4. **Get the commit list.** `git log --oneline <ref>..HEAD`. Record it beside the diff command for the scout brief.
5. **Check for changes.** `git diff --stat <ref>...HEAD`; if it prints nothing, report an empty comparison and stop.

Done when the reference resolves, both commands are recorded, and the pinned diff is nonempty.

## 2. Establish the authoritative source set

The sources are the contracts that originated the work. Commit messages and the PR body can establish provenance by pointing at them, but are never requirement sources themselves. Search these locations in order, following explicit provenance links and resolving all candidates that may govern the change. Finding one source does not exclude another:

1. **Linked work items.** Collect every reference on the change: issue keys (`WEB-12`), `Linear-issue:` trailers, and `Fixes #N` or `Closes #N` lines across the whole commit list, `linear issue id` for the branch, and `gh pr view` for issues linked from the PR. Retrieve each in full with `linear issue view <ID> --json` (read `linear` for mechanics first) or `gh issue view <N> --comments`; a comment that amends the requirement governs. Establish which items the commits deliver against and which are only related references. Collecting a reference does not make it authoritative. A Linear issue's description is its requirement contract; the Plan document attached to it is the route, not a source.
2. **A PRD or local spec path the user supplied.** Include it when the user says it originated the work or an explicit provenance link establishes that role.
3. **The working tree.** Search for files whose names or headings share words with the branch or feature, in order: `agent-docs/` (intents and archive), then `docs/`, `specs/`, `spec/`, `design/`, `rfcs/`, then root-level markdown. Use `glob` with gitignore off so untracked planning files count. These matches are candidates only. Require an explicit provenance link or user confirmation before treating one as an originating contract.
4. **The user.** Ask them to resolve any remaining uncertainty about which sources govern. If they cannot establish an authoritative source set, report that blocker and stop; never reconstruct requirements from the code, the tests, the PR description, or what the feature appears to be for.

Record each included source's identifier and provenance, read it in full with amendments, and map every operative requirement to its source citation. Follow requirement-source links by their meaning, not by a particular field name. When sources conflict and no amendment or explicit precedence settles them, ask the user before continuing.

Done when the authoritative source set is established, every source has been read in full, and every operative requirement maps to its source.

## 3. Brief one scout

Dispatch exactly one read-only `scout` through `task`, as a single-item batch. Give it the commands, not the diff, and every authoritative source by path or as the complete retrieved text, never a summary. Include the provenance record and requirement-to-source map.

```markdown
# Target
Pinned diff: `git diff <ref>...HEAD` (narrow with `-- <path>`; never diff against anything else)
Commits: `git log --oneline <ref>..HEAD`
Sources: <each authoritative source's identifier, provenance, and path or full text, with the requirement-to-source map>

# Change
Compare the authoritative requirements to the pinned diff and the resulting implementation at HEAD. Read unchanged implementation as needed to establish whether requirements are already met; do not expand the audit into unrelated existing behavior. Report candidates in three classes only:
1. Omitted or partial: a stated requirement the resulting implementation does not meet, or meets incompletely.
2. Unrequested: behavior the diff adds that no requirement in the authoritative source set asked for, however well built.
3. Incorrect: a requirement the diff implements in a way that contradicts what it states.
Do not report code quality, style, naming, tests, security, performance, design, or bugs unrelated to a stated requirement.

# Acceptance
Each candidate carries its class, the requirement quoted in full with its source identifier and location, and one sentence stating the disagreement. For implemented behavior, include the file and hunk (`@@` header or line range) in the pinned diff. For an omission with no hunk, provide absence evidence instead: the relevant or expected implementation location, the search scope and queries, and the results showing the requirement is not met. Search the pinned diff and relevant implementation at HEAD, including unchanged code, before claiming an omission. Drop candidates missing the citation or the evidence appropriate to their class. Return candidates, not conclusions.
```

Done when the candidate list is back; nothing in it is a finding yet.

## 4. Verify every candidate yourself

A candidate is confirmed against its authoritative requirement and implementation evidence or it is gone:

- **The citation.** Reread it in context in the identified source and confirm it is an operative requirement, not background, an example, a rejected alternative, an open question, or work marked deferred or out of scope. Check other authoritative sources and amendments for qualifications.
- **The hunk or absence evidence.** For a cited hunk, run `git diff <ref>...HEAD -- <file>` and confirm it says what the scout said it says. An omission needs no hunk. Instead, check the full requirement citation, the relevant or expected implementation location, and the recorded search scope, queries, and results.
- **Omitted or partial.** Search the whole pinned diff and relevant implementation at HEAD before accepting the omission; another file, helper, config entry, migration, test fixture, or unchanged implementation may already carry the behavior. `git diff <ref>...HEAD --stat` lists touched files, not every possible implementation location. Repeat the searches needed to establish absence and record their results.
- **Unrequested.** Check the entire authoritative requirement set before declaring behavior unrequested. Confirm the behavior is new, not pre-existing code moved, renamed, or reformatted: `git diff <ref>...HEAD -M -w -- <file>`, and for a block that still looks added, `git grep -n "<a distinctive line>" $(git merge-base <ref> HEAD)`.
- **Incorrect.** The contradiction is between the diff and the requirement's text, not between the diff and how you would have built it; a preference is a review comment, not a finding.
- **Drop what does not confirm.** No "possible", "worth checking", or "the scout suggested" reaches the report, and dropped candidates are never mentioned.

A conforming verdict is also a claim: walk every authoritative source's acceptance criteria once yourself against the pinned diff and resulting implementation at HEAD, including relevant unchanged code. The scout's silence on one is not evidence.

Done when every candidate is confirmed or discarded and every acceptance criterion in the source set has been checked once with its source recorded.

## 5. Report

Order findings by impact; at comparable impact, omitted or incorrect outranks unrequested.

```markdown
Verdict: <one line: N findings against <source identifiers>, or conforms>
Sources: <each authoritative source's identifier, location, amendments, and provenance>
Comparison: `git diff <ref>...HEAD`

## Findings

1. <Omitted | Partial | Unrequested | Incorrect>. <one line naming the disagreement>
   Requirement: "<the requirement quoted in full, enough to keep its meaning>" (<source identifier, section or comment; include other governing citations when needed>)
   Change: <what the diff does, or what the resulting implementation fails to do, one or two sentences>
   Evidence: <for implemented behavior: file and pinned-diff hunk header or line range; for an omission without a hunk: relevant or expected implementation location, search scope and queries, and results covering the pinned diff and relevant implementation at HEAD, including unchanged code>

Summary: <N> confirmed findings; highest impact is #1, <one clause>.
```

A citation shortened until it loses a condition is a misquote. With no confirmed findings, replace the findings section with one sentence: the change set conforms to the authoritative source set as written, listing its identifiers. An unresolved reference, an empty diff, or an unestablished source set is reported as only that blocking result, with no verdict.

Done when every template field is filled and each finding maps to its governing sources, or the conforming sentence stands in place of the findings.

## Routing

`shape` for an unclear problem statement; `intent` for capturing a wanted outcome; `spec` for turning an authoritative requirement into a spec; `plan` for implementation breakdown; `software-design` for architecture and test-seam questions; `shakedown` for adversarial pressure on a proposal; `pr` for pull-request preparation; `handoff` for transition material; `natural-english` for the report's prose, never its scope; `linear` for Linear command mechanics.
