---
name: diagnose
description: Find a bug's cause by experiment before fixing it. Use when asked to debug or diagnose, when a fix did not hold, or when a failure is flaky, a regression, or a slowdown with no obvious trigger. A panic, stack trace, or exception in the project's own code, with no core dump, is this skill; a process that segfaulted or dumped core on this machine is `diagnose-crash`.
disable-model-invocation: false
---

Run a failure down to a stated cause with the evidence that selected it. Use observations and experiments against a running reproduction, each with its prediction written before it runs.

## Mode

The request decides the mode. A request to explain why a failure happens or to diagnose it is **diagnosis-only**; a request to fix or repair it is **repair**. In diagnosis-only mode, establish the cause, recommend a repair, then clean up and report without implementing the repair or adding permanent regression tests. Any tests or code changes needed for diagnosis are temporary. Repair mode continues through repair and verification. Neither mode grants authority to commit.

## 1. Decide whether to investigate

A report that pins both the faulting location and the mechanism, a stack trace into a line whose defect you can name on sight, takes the direct path: build the loop in step 2 and skip steps 3 to 5. On that path the loop is the bare case, its first red run is the saved symptom, and the report's trace is the observation that selected the cause; record it as an inspection probe. The loop must fail at the named line by the stated mechanism, or the case goes to step 3. Once that evidence establishes the cause, diagnosis-only work goes to step 7 with a recommended repair; repair work goes to step 6. Anything less, including a failure with several plausible causes, gets the full procedure.

Read the project's glossary, local context document, and ADRs for the affected area, so candidates use its terms and do not reopen settled decisions. For unfamiliar code, dispatch read-only `scout` subagents in one batch to map the implicated path, its callers, and its tests.

Done when the mode is diagnosis-only or repair, the case is labeled direct path or investigation, and for an investigation the implicated path and its entry points are mapped.

## 2. Build the loop

Until the loop exists, no cause is proposed and no code is read in support of one; spend most of the early effort building and tightening it. An acceptable loop has all five properties:

- **Real path.** Runs the code the report implicates, through the entry point the report used or one that reaches the same code.
- **Exact symptom.** Asserts the reported failure itself, the specific error, wrong value, or time over threshold; red on the bug, green without it.
- **Seconds.** One test file, one endpoint, one fixture; no unrelated startup or seeding.
- **Pinned.** Time fixed, randomness seeded, filesystem staged, network stubbed or recorded, wherever one varies between runs and is not the suspect.
- **Unattended.** No prompt to answer, no click to make.

Take the groups in this order, and within a group the first option that yields all five properties:

- **Reachable from the test suite.** Write a failing test.
- **Needs the running program.** Script the interaction: HTTP requests, a CLI fixture diffed against expected output, or a scripted browser.
- **Needs particular data or an isolated piece.** Replay captured real input, or wrap the implicated code in a disposable harness fed the data directly.
- **Trigger unknown.** Hammer the path with randomized or property-driven inputs; run `git bisect run` with the loop as its script; or compare against an older build or alternate configuration until a difference appears.
- **A person is unavoidable.** Hand the user a short numbered procedure, or a capture script that prints its own instructions, to run in their own terminal, and ask for the complete error or result text pasted back, never a paraphrase. Never run a prompt-driven script yourself in a non-interactive environment; it hangs or answers itself.

For an intermittent failure, drive the rate up first: loop the command, run copies concurrently, add load, inject sleeps or control the clock, until a change in rate is readable in a handful of runs. A probabilistic loop is acceptable only then, with iterations, concurrency, and seed recorded so two experiments compare.

When no option yields a loop, stop: state that no diagnostic signal could be built, list every approach tried and why each failed, and ask for one of an environment that reproduces, a captured artifact (core dump, HAR file, trace, or the log window around the failure), or approval to add temporary tagged telemetry in production. Resume when one arrives.

Done when the loop has run at least once with its exact invocation and output recorded and holds all five properties, or you have stopped with the request above.

## 3. Observe and reduce

Run the loop and compare its failure to the report symptom for symptom; a nearby failure (same module, different error; same error, different path) is not the bug, so fix the loop until it produces the reported one. Confirm repeatability over several runs, or record the failure rate. Save the observed symptom verbatim; step 6 compares against it.

Then reduce: remove or simplify one thing at a time, an input, a caller, a configuration value, a data item, an action, rerunning after each; keep a removal when the failure persists, restore it when the failure stops. Finish by removing each retained part alone once more; each must stop the failure. What remains is the **bare case**, and causal analysis starts only once it exists.

Done when the loop demonstrates the reported failure at a recorded rate and every part of the bare case has been shown necessary.

## 4. List candidate causes

Write three to five candidate mechanisms that would produce the bare case, each with a prediction naming what an inspection would observe or what would happen if an experiment changed one condition: "if the cache returns a stale row, then reading `row.updated_at` at the boundary in `sync()` will show a timestamp older than the write in the fixture." A candidate no observation could refute is refined until it has a prediction, or dropped.

Rank by plausibility, probe cost, and the probe's discriminating power together.

Show the ranked list to the user before the first probe, but do not wait: probe in ranked order, fold in a correction when it arrives, and note in the report which happened.

Done when every candidate has a falsifiable prediction a single probe can check.

## 5. Probe one condition at a time

Every probe answers one named prediction from the list. An inspection observes state without changing a condition; an experiment changes exactly one condition and observes the result.

- **Live inspection first.** Debugger, REPL, or runtime inspector reading state at the boundary the prediction names.
- **Tagged logs second.** When live inspection cannot reach the moment, add a log line at the decision boundary that separates candidates, and only there. Choose one **tag** for the investigation, `DBG-<slug>`; start every temporary log message with it and put it in a comment on every temporary code or config edit.
- **Record the verdict.** After each probe: inspection or experiment, prediction, observation, and whether it held. For an inspection, name the state inspected and record that no condition changed; for an experiment, name the one condition changed.
- **Revert before moving on.** A temporary change whose prediction failed comes out before the next probe starts.
- **Prototype only for reach.** Build a disposable harness when the real path cannot be made runnable in a small experiment, or one mechanism needs isolating, such as whether a regex backtracks on one input. Its result is a lead; proof comes from the loop or the real path under inspection.

When the symptom is elapsed time, memory, or throughput, measure before touching anything: run the slow workload under a profiler, tracer, query planner, allocation sampler, or plain timer, and keep that output as the baseline. The dominant measured cost picks the candidates; logs are the weakest evidence here. Every remedy does one of four things to that cost: **skip it** (drop unneeded work, shrink the input, defer until something uses it); **pay it once** (index, cache with the invalidation rule written beside it, batch a fixed cost across calls); **pay it elsewhere** (move required work out of the critical moment); **pay it twice** (duplicate work, justified only when tail latency matters and capacity is spare). An inspection checks the predicted cost in the measured output without changing the workload. For an experiment, change one factor and run the same workload through the same tool; retain the change for repair only when the two outputs show the difference the prediction named.

Done when one candidate's prediction has held and the competitors' have failed or been made irrelevant, so the cause can be stated beside the probe that showed it. Diagnosis-only work goes to step 7 with a recommended repair; repair work continues to step 6.

## 6. Lock the repair down, repair mode only

A change that turns the loop green without a probe that showed why is a workaround; go back to step 5. On the direct path the probe is the report's trace confirmed by the red loop; the one repair must turn it green. A repair that needs more than that has left the direct path, so go to step 3. A repair that needs its route written down gets one by `plan` first.

`software-design` finds the **real seam**: the narrowest test location that still recreates the callers, interactions, and sequence the bare case needs, or that none exists. `test-quality` shapes the regression test there.

Build the test from the bare case, run it against the unrepaired code, and keep the red output; apply the repair, run it again, and keep the green output. When no real seam exists, write no test; record the absence as an architectural finding naming the callers and sequence a test would need and what in the structure prevents supplying them, for step 7 to hand on.

Then re-run the original, unreduced scenario through the surface that reported it, the same browser flow, CLI invocation, HTTP request, or user procedure, and compare against the symptom saved in step 3.

Done when the original scenario is correct on its original surface and either the red-then-green pair or the missing-seam finding is recorded.

## 7. Clean up and learn

- **Tag swept.** If a tag was used, `grep` for it across the repository returns nothing; otherwise record that no tagged instrumentation was added.
- **Temporary changes gone.** Remove diagnostic logging, config edits, exploratory changes, and workarounds. In diagnosis-only mode, restore every temporary source and test change, even when its prediction held.
- **Prototypes removed.** Delete disposable harnesses and other diagnostic-only artifacts after recording the evidence needed for the report.
- **Cause recorded.** Write the mechanism in one or two sentences with the probe that showed it in the report. If a commit or PR is separately requested, include the cause in the commit message or the PR body's What & why per `pr`. "Fixed the bug" is not a cause.
- **Prevention asked.** A written answer to what would have prevented this defect or caught it earlier: a test at the missing level, a stricter type, a removed special case, a seam that exists now.
- **Design handed on.** When the answer is structural (a missing seam, knotted callers, unmarked coupling), write the recommendation with the seams that could not recreate the bug, the callers involved, and the coupling that hid the cause, and hand it to `software-design`.

Done when every item holds and the mode's outcome is recorded: an evidence-backed cause and recommended repair without a production fix or permanent regression test for diagnosis-only work, or step 6's verified repair for repair work.

## Report

State the mode. Each claim points at the path, command, artifact, or user-supplied text that supports it:

1. **Loop.** The exact command, test, script, or user procedure; the path it exercises; the symptom it asserts; one actual run's output. For a user-run procedure, the artifact requested back.
2. **Observed and bare case.** The original symptom, its repeatability or failure rate, the bare case, and which retained parts were proven necessary.
3. **Candidates.** For an investigation, three to five in rank order, each with its prediction and, where cost or evidence moved it, why it sits there. The user's correction, or that none came. For the direct path, identify the trace and matching loop failure instead of inventing a candidate list.
4. **Probes.** For each: inspection or experiment, prediction, state inspected or the one changed condition, observation, and verdict. Record no condition changed for an inspection; for an experiment, state whether the change was reverted. For performance work, include the measured baseline and, when an experiment ran, follow-up measurements under the same workload, side by side.
5. **Cause and repair.** The selected cause and its causal evidence. In diagnosis-only mode, the recommended repair and what would verify it, clearly marked as unimplemented and unverified. In repair mode, the change, the seam and observed red-then-green or why no real seam exists, and the result of the original scenario on its original surface.
6. **Completion.** Tag sweep result or why none was needed, temporary-change and prototype cleanup, the recorded cause, and the prevention answer or `software-design` handoff.

Observations and untested candidates stay visibly separate; a causal conclusion appears only beside the probe evidence that selected it.
