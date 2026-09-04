---
name: test-quality
description: Pick, write, review, and prune tests so the suite guards what callers rely on. Use when asked whether something needs a test, to write or review tests, to add a regression test for a bug, to judge a suite, or to work test-first.
disable-model-invocation: false
---

Each test names the promise it guards and the one mistake it would catch, and stays green through any internal rewrite that keeps that promise. Where a production seam, port, or adapter belongs is `software-design`'s decision, never a side effect of wanting a test.

## Mode

Pick one before step 1; the request decides it.

- **Judging.** A review of existing tests, a verdict on a suite, or the question whether something needs a test. Steps 1 to 5 are the checks, and every test in scope is held to each step's done line. Run the suite to see what is red and green, and change nothing: no test edits, no source edits, no deletions, no mistake planted in the code under test. Step 7's sensitivity proof becomes a prediction, the mistake the test would survive and why, and a test that fails a check becomes a finding naming the step it failed. Deliver the report; edits wait for a request.
- **Writing.** Adding, changing, or pruning tests, a regression test for a defect, or test-first work, all at the user's request. All seven steps, with edits subject to the deletion gate.

## Deletion gate

Delete an existing test only when the user requested pruning within its scope or approved that identified candidate for removal. A request to add or change tests does not authorize removing other existing tests. Without that authority, leave removal candidates in place and report them with their reasons as deferred; they do not prevent completion of the requested work. Proposed tests may be discarded and tests added during this task may be removed without this gate. Judging mode remains read-only.

## 1. Establish what is promised

Read the interface docs or spec, the task or defect report, the nearby implementation, and the existing tests; use the project's domain terms and take its recorded architectural decisions as fixed. For every candidate test write what a caller can rely on, the one concrete mistake it exists to catch ("the cap check uses `<` instead of `<=`"), and the observation that tells the two apart. Discard proposed tests that cannot name a distinct promise and a believable mistake; flag existing tests that fail this check for the deletion gate. If the promise is unclear because the API's shape is unchosen, stop: the product question goes through `shape`, `intent`, and `spec`, the interface question through `software-design`, before any testable behavior is invented.

Done when every proposed test carries its three-part rationale and every existing test in scope either carries that rationale or is flagged with the reason it fails.

## 2. Choose the risk set

Start where the work started: for a defect the reported failure is the first test; for a feature or interface change the first tests cover the changed public behavior, never the steps of the implementation plan. Then walk the decision points a caller could notice, asking which carry a believable mistake: limits, precedence between competing rules, ordering, state transitions, the errors callers must handle, and commitments to older callers or formats. Read [ASSERTIONS.md](ASSERTIONS.md) here; it holds the input shape each needs, and every assertion written or reviewed is judged against it.

Then identify pruning candidates: private helpers, structural wiring, incidental defaults, and construction that echoes its own arguments, unless the thing is itself an external commitment; anything whose only argument is an unexecuted line in a coverage report (meet an enforced threshold, still treat it as no evidence for any particular test); anything an existing test of equal strength already protects. Exclude proposed cases on these grounds; route existing tests through the deletion gate.

Done when deleting any selected case would leave a distinct risk unprotected, no two selected cases guard the same one, and existing tests in scope but outside that selection are identified as pruning candidates rather than silently removed.

## 3. Choose the observation point

Call the subject through the narrowest stable interface a real client uses that still exercises the whole promise, and observe only what the contract documents: the return value, the error, or a promised external effect such as a persisted row, an emitted message, or an outgoing request. An effect the code happens to produce is not a promise. Read [DOUBLES.md](DOUBLES.md) whenever the behavior crosses I/O or touches a collaborator you could control.

A promise visible only through private state, a deep import, or a seam that does not exist is a design question. Do not answer it with a test-only hook, an injected dependency, or an adapter added for the test; route it to `software-design` and its DEEPENING.md, which decides whether the seam becomes a port or stays internal and what a test may supply there, before any seam a test would use.

Done when the test observes the full promise through the caller's interface, with no reach into internals and no production structure that exists for the test.

## 4. Build the case

Set up the least data under which the named mistake and the correct behavior give different answers, take the expected value from somewhere other than the code under test, and confirm any library or runtime semantics the test rests on in the documentation, the library's code, or a few executed lines before relying on them. Assert one precise outcome a caller could depend on, in the shapes ASSERTIONS.md requires, and name the test for the behavior it protects.

Done when an internal refactor that preserves the contract would leave the test green and the named mistake would turn it red.

## 5. Choose controlled collaborators

Whenever the behavior crosses I/O or an outside dependency, take the highest rung of the ladder in [DOUBLES.md](DOUBLES.md) that stays controlled and deterministic. A double supplies inputs from outside or captures an effect the contract promises to the outside, never proof that internal methods called each other, and never a restatement of the logic under test.

Done when every dependency the behavior crosses has a rung and every relevant production path runs deterministically.

## 6. Work test-first when asked

Settle the production interface and structure before the first test, through `software-design` where structure is in question; writing the test first orders the work, it does not choose the architecture. Write the behavior test, run it, confirm it fails for the expected reason, implement until it passes, and finish the design while it is in hand; a passing assertion does not license leaving structure for later.

Done when you have watched the test fail and then pass and the implementation is structurally settled.

## 7. Prove sensitivity and prune

Run the changed test and the relevant suite. For any test written after its implementation, and any whose sensitivity you doubt, put the named mistake into the source, watch the test fail, restore the source, and watch it pass. A test that stays green under its own mistake fails the sensitivity check. In judging mode the proof is not run; state the mistake the test is predicted to survive and the assertion that lets it.

Do not force a focused failing test when it would cost disproportionate harness work, fragile doubles, state that exists only in production, disruption of unrelated fixtures, or an assertion less faithful than the failure the user saw. Say so before touching source, name the closest runnable reproduction or regression check instead, and still run the strongest available behavioral proof.

Tests that survive their mistake, duplicate stronger coverage, or protect no observable promise are removal candidates. Remove authorized candidates through the deletion gate and report the rest as deferred. Keep fixtures and parameter tables only where they make the protected behavior easier to read. In judging mode each failure is a finding, not a deletion.

Done when every changed test passes and has shown it catches its named mistake, and every existing test in scope has a disposition: kept with a distinct protected risk, removed through the deletion gate, or deferred with the reason for removal recorded.

## Report

For a testing decision, review, or plan, in order:

1. **Behavior and risk map.** Per test selected for retention: the caller-visible promise, the named mistake, and the observation that separates them. Existing tests left in place pending deletion approval belong in the pruning report, with the check they failed.
2. **Test design.** The entry point, the chosen data, where the expected value came from, and any grouped assertions or parameter tables with the one contract they share.
3. **Collaborator strategy.** Each collaborator's rung, which permitted condition justified any stand-in for an external system, and the contractual external effect behind any asserted interaction.
4. **Evidence.** Whether a pre-change failure was observed, how sensitivity was proven or, in judging mode, the mistake each test is predicted to survive, the focused and suite commands run, and their actual results.
5. **Exclusions and pruning.** Cases omitted, tests removed, and existing removal candidates deferred, each with its reason: duplicate, structural, metric-only, or weak signal. For existing tests removed, name the pruning request or candidate approval that authorized deletion.
6. **Design routing.** Any seam or interface question that arose, as a `software-design` decision made or still owed, never one the tests settled.

Cite the spec, issue, docs, and existing tests establishing each contract by path or identifier; cite an external source only where it confirmed library or runtime behavior.

## Routing

- **Sequencing beyond the tests.** `plan`.
- **Adversarial review of a formed approach.** `shakedown`, on request.
- **Prose.** Test names and review text fall under `natural-english`.
