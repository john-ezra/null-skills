---
name: software-design
description: Design or review code structure using A Philosophy of Software Design. Use when designing or reshaping a module, class, API, interface, or seam; choosing adapters or making code testable or AI-navigable; reviewing complexity in code, diffs, or PRs; or when the user mentions Ousterhout's design philosophy, deep modules, or codebase design. Design quality, not bug hunting.
disable-model-invocation: false
---

# A Philosophy of Software Design

Everything here serves one goal: **reduce complexity**. That goal outranks every individual rule below, every move carries its limit, and the tiebreaker is always total system complexity: best information hiding, fewest dependencies, deepest interfaces.

## The complexity model

**Complexity** is anything about the system's structure that makes it hard to understand and modify. Size, feature count, and sophistication are not complexity; the question is whether the next reader can understand and change the code. Design for that reader, not for the writer: a named type, a spelled-out structure, or a complete comment costs the author once and saves every reader. The reader is the judge: if someone reading the code calls it complex or non-obvious, it is, clarify instead of arguing.

Three symptoms, tie every problem you raise to one:

- **Change amplification.** One simple change forces edits in many places.
- **Cognitive load.** How much a reader must know to complete a task. Not line count: sometimes more lines is the simpler design. Usual sources: interfaces with many methods, global state, inconsistency, cross-module dependencies, and a resource the caller must remember to release, so the module that acquires a resource releases it.
- **Unknown unknowns.** It isn't obvious what must change or what must be known. The worst symptom, because it surfaces as bugs after the change.

Two causes: **dependencies** (code that can't be understood and changed in isolation) and **obscurity** (important information that isn't visible). Dependencies produce change amplification and cognitive load; obscurity produces unknown unknowns and cognitive load. Name the cause with the symptom, because the cause picks the fix: a dependency calls for restructuring or hiding, obscurity for a simpler design first and names or comments second. Dependencies can't all be removed, every interface creates one; reduce their number, then make each survivor obvious, carried in a signature, a shared name, or a type the compiler or a search will catch, never a convention two places must both remember.

Two ways to attack any complexity: eliminate it (fewer special cases, one name per concept) or encapsulate it behind a module so nobody meets it all at once. Try elimination first; a module that hides removable complexity is interface spent on nothing. Complexity is weighted by the developer time spent in a part: a complicated piece nobody touches barely counts, so isolating complexity where no one will read it is almost as good as removing it. And it is incremental: it arrives in hundreds of small dependencies and obscurities, so sweat the small stuff; kludges compound.

## Strategic, not tactical

Working code isn't enough. The primary goal is a great design that also happens to work; most code is written by extending existing code, so what you leave behind outweighs how fast you finished. The bar for modifying existing code: **when the change is done, the system has the structure it would have had if it had been designed from the start with that change in mind.** Reach it by asking, before the first edit, whether the current design is still the best one given this change; if not, refactor to the design that is, then make the change on top of it. A flaw on the path of the change, one the change would otherwise have to code around, gets fixed, since a workaround is a second flaw that hides the first. A flaw beside the path is named to the user with what fixing it would cost and waits for their call; it is never fixed unasked and never silently coded around. What was left alone is listed in the report.

Grow the system by abstractions, not by features: the first time a feature needs an abstraction, design that abstraction cleanly and whole, not as a minimal special-purpose version to generalize later, and not by letting a sequence of tests discover it one passing case at a time; settle the interface first, then test against it. Work in the project's terms: before designing or reviewing, read its domain glossary and relevant ADRs where they exist, and use those terms for the domain and [MODULE-DESIGN.md](MODULE-DESIGN.md)'s for the architecture.

*Too far:* a whole-system design up front; the right structure emerges from real changes to running code, so invest in small improvements tied to each change, never a speculative redesign. A deadline, or a refactor that would break other teams, can force the quick fix; then ask what the cleanest design is within that constraint, look for an approach nearly as clean at a fraction of the cost, and name the deferred refactor so time gets allocated for it, rather than letting the patch stand silently.

## Branch: focused design guidance

For a question about module depth, an interface, seam placement, adapters, testability, or AI-navigability that does not ask for a full design or review:

1. Read [MODULE-DESIGN.md](MODULE-DESIGN.md), plus [DEEPENING.md](DEEPENING.md) when dependencies, adapters, I/O, or testing affect the answer, and inspect the named code when there is any.
2. Explain what callers must know now, what knowledge the module hides, and where change or testing complexity currently spreads.
3. Recommend one structure and state why it reduces total complexity; mention a competing structure only when the trade-off is real.

Done when the recommendation accounts for callers, hidden knowledge, dependencies, and test observations that apply to the question. Continue into the designing branch only when the request calls for a complete redesign or implementation.

## Branch: designing

For a new module or interface, a redesign, or an implementation request that changes code structure. One level up, for a request that splits a system into major modules or fixes a feature set, compare at least two decompositions before shaping any single module.

1. **Design the interface.** Follow [DESIGN-IT-TWICE.md](DESIGN-IT-TWICE.md), which owns problem framing, caller-usage-first interface sketches, alternatives, comparison, and recommendation.
2. **Shape it.** Work through every applicable move in [DESIGN.md](DESIGN.md), using [MODULE-DESIGN.md](MODULE-DESIGN.md) for depth, information hiding, seam placement, and the over-deepening brake, and [DEEPENING.md](DEEPENING.md) when dependencies, adapters, I/O, or testing affect the design.
3. **Design the implementation twice.** Compare at least two implementation structures on simplicity and performance without adding either to the caller's interface.
4. **Gate before implementing.** Done when: the selected exploration branch in [DESIGN-IT-TWICE.md](DESIGN-IT-TWICE.md) is complete; two implementation structures were compared; the winning interface's comment is simple and complete; the common case asks the least possible of the caller; applicable dependency and testing choices are settled; and every named flag plus applicable supplemental check in [RED-FLAGS.md](RED-FLAGS.md) has a verdict. Eliminate each hit and rerun its check, or explicitly accept it because the best fix would increase total complexity; no unexplained hit may pass the gate.

This branch reaches a pre-implementation design. Implement after the gate only when the user asked for code changes; otherwise stop with the recommendation.

When implementation was requested, write each new method's interface comment before its body and each member variable's comment with its declaration, so the code and its comments finish together and no comment backlog forms. Deviations from the chosen interface are design evidence, so read them before absorbing them. Take a single extra parameter or unplanned edge case as a requirement the frame missed. The gate stops holding once deviations form a pattern: two independent ones of the same shape, a cast repeated across call sites, optional fields that always arrive together, or callers each learning the same hidden rule. That pattern sends you back to the constraint: re-ground it and rerun the relevant alternatives; an escape hatch bolted onto the selected shape is not a fix. Before handing off, read the entire diff: update every comment the change invalidates and remove leftover debugging code and unresolved TODO markers.

## Branch: reviewing

For a diff, PR, module, or file: a design review, not a bug hunt. Correctness bugs require a separate code review. Deliver design findings; apply fixes only when asked.

1. **Scope.** List the modules the target touches, classes, functions, interfaces. For a diff, that means the modules the changed code lives in, not just the changed lines. Read [MODULE-DESIGN.md](MODULE-DESIGN.md), plus [DEEPENING.md](DEEPENING.md) when dependencies or testing affect the design.
2. **Sweep.** Read [RED-FLAGS.md](RED-FLAGS.md), check every module in scope against every named flag, and run each supplemental check where it applies. The sweep is done only when every named flag × module pair has a verdict, a skipped flag is a missed finding.
3. **Diff checks** (diff and PR targets only). Does the change clear the strategic bar above, or is it the smallest patch that bends the design? Is every comment the change invalidates updated? Is anything future developers will need recorded only in the commit message, if so, it belongs in the code. When a convention nit can be checked mechanically, propose the lint rule or pre-commit check instead of the nit.
4. **Report.** Order findings by complexity cost. Each finding names its source check, a catalog flag or supplemental check, the symptom it causes (change amplification, cognitive load, unknown unknowns) and the cause behind it (dependency or obscurity), and a concrete alternate design that eliminates it. Use the check's fix move; a finding is a prompt to redesign, not a complaint. Drop any finding whose best fix adds more interface than it removes.

> Distilled from John Ousterhout, *A Philosophy of Software Design* (2019, v1.01).
