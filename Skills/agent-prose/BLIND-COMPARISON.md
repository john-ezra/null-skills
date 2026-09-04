The experiment that settles whether an instruction changes behavior when reading cannot. The only admissible evidence is what candidate agents produced and did; a candidate's account of what it followed counts for nothing.

## 1. State the claim

Write down the variant under test and what it is compared against (the sentence and its absence). Write the observable behavioral claim: what an artifact or a recorded decision looks like when the variant works. Then write three to six criteria a judge can score from artifacts alone; candidates never see them, except any a normal user request would have stated on its own. Done when a stranger could score a result against the criteria without asking you anything.

## 2. Build the task

Write a request a user would plausibly send that exercises the claimed behavior. Scrub every cue from the prompt, directory and file names, project and branch names, and any artifact a candidate can read: no word that names the experiment, a variant, a comparison, a rubric, or a wanted outcome. Done when everything a candidate can read reveals only an ordinary task in an ordinary project.

## 3. Isolate

Give each candidate its own worktree or temporary project holding exactly one variant, with no trace of the other and no marker of which one this is. Hold constant across candidates the starting code, dependencies, tools, environment, prompt, and every other piece of context. Allow no shared writable state. Keep the mapping from run label to variant in your own notes and never in a tree. Done when the only difference between any two candidate trees is the variant.

## 4. Run the candidates

Dispatch one isolated subagent per candidate, all in one batch, with the same agent configuration for every run unless configuration is the question. The prompt is the task and ordinary project context; never ask a candidate to narrate compliance. When each finishes, keep its artifact, its verification evidence, and its execution history (`history://<id>`), or record that a history is unavailable. Done when every run has its artifact captured and its history captured or its absence noted.

## 5. Judge blind

Give a single judge, a fresh subagent that ran no candidate, the sanitized run labels, every artifact, the hidden criteria, and the common project context it needs. Knowing that it is judging is fine; knowing which variant a label carries, which model produced a run, or which result you hope for is not. It scores every run in one pass on one scale and returns, per run and per criterion, the evidence it found, the failures, and a recommendation; it edits nothing. If a pass is interrupted, rerun the whole pass. Done when the judge's record holds criterion-level evidence for every run.

## 6. Verify independently

Read the outputs and histories yourself. For each run, decide whether the instruction under test was loaded (the read appears in the history), whether its decision rules were applied (the decisions appear in the history or the artifact), and whether the artifact shows the claimed behavior rather than a description of it. When histories are unavailable, say so and limit conclusions to what artifacts show. Done when every run has your verdict on loading, application, and artifact.

## 7. Decide

Promote the variant only when the blinded results support the stated benefit with no material regression on any other criterion. A tie, inconsistent scores, a regression, an ambiguous criterion, or a contaminated run set means the experiment is revised (sharper criteria, a cleaner task, more runs) and rerun, never picking the prose you prefer.

## Contamination

Any of these disqualifies the run set, and the comparison starts over from step 2:

- **A candidate could infer the experiment.** Its variant, the criteria, a competing run, or a wanted result was inferable from a label, path, artifact, or prompt.
- **Candidates shared writable state.** One run could see or alter another's work.
- **The judge learned an identity.** Which variant or model a label carried, or which result was wanted.
- **The judge scored in separate passes.** The scores are not one ranking.
- **A conclusion rests on a self-report.** A claim of having read or followed the instruction stood in for artifact evidence.

## Record

Report the experiment in this order: the behavioral claim; the task as sent; the hidden criteria; the isolation arrangement and the constants held; the sanitized result of each run, cited by label, artifact, and trace; the judge's evidence and recommendation; your own verification against artifacts and histories; the threats to validity you could not remove; and the decision to promote or to revise. Done when the record is complete and the decision follows from it.
