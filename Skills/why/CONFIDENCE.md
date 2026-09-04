Every claim about why something was done carries one of five tiers. The evidence behind the claim sets its tier; the tier fixes what it cites, the wording it may use, and the report section it goes in.

## Tiers

### Stated

An attributable record gives the reason in its own words: commit message, PR description or review comment, ticket, design document, code comment, incident report, a participant's message.

- **Cite.** The statement, precisely enough to reopen it, quoting the words that carry the reason. A code comment is cited at its current path and line, plus the commit that introduced it when blame gives one.
- **Say.** Plain causal language: "The retry cap is 3 because the upstream rate-limits at 4 (PR #212, description)."
- **Not Stated.** Code that performs the action: the comment above a line qualifies, the line does not. Nor does a PR or ticket title that names the change without the reason.
- **Goes in.** Stated rationale.

### Convergent

Several independent indirect signals point to the same reason and no material record contradicts them. Two is not several; independent means different authors or systems, so a commit and the PR that merged it are one signal.

- **Cite.** Every link in the chain, each to its record.
- **Say.** Strength attributed to the evidence, never to a speaker. For example, with three independently produced records: "The evidence points to the 2024-03-02 outage: the operations incident records exhausted connections (INC-118), a support agent independently logged matching customer failures (SUP-42), and the application maintainer's later patch limits concurrent connections (abc123)." Never "the team wanted" or "the author intended".
- **Goes in.** Interpretation.

### Inferred

Context favors one reading, no record states it, and the signals are few or not independent.

- **Cite.** The reasoning chain and each record it rests on.
- **Say.** "appears to", "likely", "suggests", "is consistent with", hedged to match the evidence, no stronger and no weaker.
- **Goes in.** Interpretation, after Convergent claims.

### Possible

Evidence is thin, or several explanations fit the record equally.

- **Cite.** Whatever supports it, plus the proof that would settle it and was not found.
- **Say.** "One possibility is X; the ticket or thread that would confirm it, around <date>, was not found."
- **Goes in.** Open accounts only, never a summary line.

### Unknown

The searched record leaves the question open.

- **Cite.** The question, every source and query searched, and what each returned: nothing, silence in records that exist, or access refused.
- **Say.** "The record does not say why X. Searched: the log of <path>, PRs #A to #B, tracker query <q>, `docs/` for <term>. None addressed it." Absence stays absence: not "no incident prompted this", not "the value was arbitrary".
- **Goes in.** Unknowns.

## Contradictions and shifts

- **Two Stated records disagree.** Report both as Stated with dates; the synthesis says the record is divided and, where dates allow, that the reason may have changed. Neither becomes the conclusion.
- **Indirect evidence contradicts a Stated record.** The Stated claim keeps its tier, the conflict is noted beside it, and the indirect account goes in Open accounts.
- **A material contradiction inside a Convergent chain.** Drop to Inferred or Possible and name the contradicting record.
- **Reasons that shifted over time.** A timeline, each entry at its own tier, never merged into one reason.

## Delivery check

Run against every claim about motivation before the report goes out; complete when every claim has passed every check.

- **Citation.** Present, and the record reopened and read; otherwise the claim moves to Possible or Unknown.
- **Wording.** Matches the tier; a "because" with no Stated record behind it is a violation.
- **Mechanics.** No line of code is proving its own purpose.
- **Conflicts.** Every contradicting record is named beside the claim it contradicts.
- **Rival.** A competing explanation was tried against the same evidence; if it fits, the claim dropped a tier and the rival is in Open accounts.
- **Gaps.** Every empty search and access failure is disclosed, in Unknowns or the coverage map.

## Stance

- **Past actors were not optimizing for you.** Do not assume they chose the cleanest solution or the one you would pick today.
- **Not every detail was intended.** A value may be a copied default, a guess, or a leftover; say so at the tier the evidence earns.
- **The user's reason is a hypothesis.** Tested like any other and reported at its earned tier, Unknown included.
