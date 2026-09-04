---
name: why
description: Reconstruct the historical reasons behind a code decision from commits, reviews, tickets, docs, and incidents. Use when the user asks why something exists, was chosen, was limited, or was rejected, or wants a value traced to its origin. Current behavior is `how`.
disable-model-invocation: false
---

Recover the reasons behind a past decision from the records that hold them, and report what the record establishes and at what confidence, never a tidy motive it does not support. Non-goals: what the code does now, which is `how`, and whether the old decision is still right.

The run is read-only: no commits, review comments, ticket or document edits, messages, or dashboard changes. Every conclusion is phrased in the tiers of [CONFIDENCE.md](CONFIDENCE.md); read it before collecting evidence and again in synthesis.

## 1. Anchor the decision

State the decision precisely enough that a search can hit or miss it: which value, rule, structure, or rejection, in which code, at what time. A question spanning several decisions gets one scope naming each. Then build the anchor:

- **Locate.** The paths, spans, and symbols that carry the decision; ask the user only when they cannot be found.
- **Blame.** `git blame -w -M -C` on each span.
- **Follow.** `git log --follow -p -- <path>` through renames, then `git show` each commit that touched the decision, reading the whole message.
- **Link out.** Every PR, review thread, ticket, design doc, and incident those messages and PR descriptions reference, collected as identifiers.

Today's code is a pointer into the record, not a witness: its shape and behavior guide the search and prove no intent. Done when the anchor lists exact paths and spans, the lineage of commits, and every linked record by identifier.

## 2. Map the evidence

Before dispatching anyone, check what this environment reaches: connected MCP tools and CLIs (`gh`, the `linear` skill), the repo's own pointers (`docs/`, ADR directories, runbooks, links in the README and in the anchor's tickets), and credentials. Then write the coverage map, one row per category, exactly seven:

| Category | Holds |
|---|---|
| Repository history and review | commits, PRs, review comments |
| Work tracking | issues, tickets, epics |
| Long-form engineering records | design docs, ADRs, RFCs, wikis, runbooks |
| Team conversation | chat threads, mailing lists, meeting notes |
| Operations and incidents | dashboards, alerts, postmortems |
| Exception tracking | error reports and their comment threads |
| Product and warehouse data | analytics, experiments, usage queries |

Each row is **Available** with the tool that reaches it, **Unavailable** naming the gap ("no chat integration connected"), or **Irrelevant** only when the decision cannot have left evidence there, with the reason ("a lint rule has no product analytics"). What a connected tool holds is fetched, not asked for; ask the user only for what no reachable system has, listing the systems checked. Done when all seven rows have a verdict.

## 3. Investigate

**Shortcut.** When one complete PR or design record answers a narrow question explicitly and in full, read it whole, comments included, and stop. Available rows not pursued keep their status, annotated "not pursued, answered by <record>".

**Parallel scouts.** Otherwise, when any Available row lies outside the repository, every Available category gets one read-only `scout`, all launched in one batch; the map's Irrelevant verdict is the only relevance judgement. Each brief carries:

- The user's question, word for word.
- The anchor and every known identifier: paths, symbols, shas, PR numbers, ticket ids.
- Exactly one category and the tool that reaches it.
- The rules. Open wide in the assigned source, narrow to the hits that bear on the anchor, and read each whole with its comments and same-category links. Quote exact wording only where it states a reason. Log every query and record opened, yield or not. Keep stated rationale apart from circumstantial observation; note conflicts and credible alternate readings; report failed searches and material that should exist but could not be reached. Return references into other categories as leads, uninvestigated.
- The report shape: source, search log, explicit findings, indirect findings, conflicts, gaps, leads, each with a citation a reader could follow. Raw retrievals stay with the scout.

When repository history is the only available category, do its work yourself under the same rules; the anchor holds the lineage, so what remains is the review threads. A lead into another category goes to that category's scout, or is reported as a gap when the category is unavailable. Done when every scout has returned evidence, negative searches, gaps, conflicts, and leads, and no category was investigated twice.

## 4. Synthesize

With [CONFIDENCE.md](CONFIDENCE.md) open, tier every claim about motivation, run its delivery check on each, and report contradictions and shifts as it directs. Done when every conclusion carries a tier and a citation or sits in Open accounts or Unknowns, every contradiction is visible, and every gap is named.

## 5. Report

Sections in order, dropping only empty ones:

1. **Scope and anchor.** The decision; files, spans, symbols, and the lineage of commits and linked records.
2. **Stated rationale.** Reasons in a participant's own words, each quoted and cited.
3. **Interpretation.** Convergent then Inferred claims, each with its evidence chain.
4. **Open accounts.** Explanations not ruled out, with what favors and what weakens each.
5. **Unknowns.** What could not be determined, with the searches that returned nothing.
6. **Coverage map.** The seven rows with their step 2 statuses; each Available row adds what it held, "empty", or "not pursued, answered by <record>".
7. **Change constraints.** Only when a change follows. Four lists: **Preserve**, commitments the history shows the code keeping; **Change**, the intended departures; **Avoid**, approaches the record shows failed or rejected, and why; **Risk**, where confidence is thin or regression exposure real. Constraints, not a design: they do not prescribe the fix.

A citation names the record precisely enough to reopen it: commit sha, PR and comment, ticket id, document and heading, thread and date, incident id. Exact quotations appear only under Stated rationale; indirect claims cite every link in their chain; a claim with no citation lives only in Open accounts or Unknowns.

## Routing

A request asking both what the code does and why gets `how` for the present and this skill for the past. When the answer feeds a change, the constraint lists are inputs to `software-design` for structure and `plan` for the route; `shakedown` presses on open accounts or risks that need adversarial review; `intent` or `spec` only when the finding exposes a product decision nobody has written down; `pr` for the review that follows.
