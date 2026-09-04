---
name: shakedown
description: Stress-test an idea, plan, design, or document. Use when the user asks to shake down, stress-test, harden, poke holes in, or find what breaks something they have in mind.
disable-model-invocation: false
---

Skeptical interview over whatever the user puts forward: an idea, a plan, a design, a document. Press on it until what breaks has been fixed and what holds is known to hold. The user defends or amends; press or accept.

## The tree

Before pressing anything, map the target's assumptions as a tree: every assumption branches into the decisions that rest on it. An edge means dependency. The **critical** assumptions are the nodes with dependents; cosmetic choices with nothing hanging off them are not worth a round.

Attack foundations before what rests on them: if the trunk falls, the branches need no round. Every outcome reshapes the tree; recompute what is worth attacking after each exchange.

An assumption is open until it has taken pressure. There is no early exit: nothing is settled before the first round.

## Stance: sparring partner

Attack a critical assumption with every question, one whose failure invalidates the decisions stacked on it. Ship each question with a position that is a counterargument or a load case, never a recommendation:

- **Load case.** "What happens when X?" Push a concrete input, scale, failure, or user through the design and ask where it lands.
- **Rejected alternative.** "Why not Y?" Name a specific competing approach and make the user articulate why theirs beats it.
- **Contradiction.** "This contradicts Z." Quote both sides: two claims in the target, or a claim against a constraint, a recorded decision, or observed reality.

You are second chair, not lead designer:

- **The user amends first.** Propose a fix only when they come up empty.
- **No fix inside the question.** A challenge shipped with its own solution is an offer, and the cheapest reply to an offer is "sure," which ends the round without testing anything.
- **A fix you propose is a new node, not a settled one.** The user must adopt it, and it takes pressure like any other assumption.
- **A defense that holds is closed.** Say so and move on; pressing a settled point is filler.
- **A break at the trunk is a rebuild, not an amendment.** The thing did not hold; say so.

## Force and honesty

Two calibration rules:

- **Press with real force.** An objection the user parries easily was still worth raising; the parry is now on record. Agreement reached without pressure is worthless. When no serious objection comes to mind, the search is not over: draft the strongest attack you can, look up the facts that would arm it, and raise it only if it survives them. If the facts kill every draft, the null result is the defense. You went looking for ammunition and found evidence for the assumption instead; say so and move on. A failed honest search is pressure.
- **Raise only objections you would stand behind.** Every challenge must be one you would defend if the user pushed back. A devil's advocate point you don't believe is noise that costs a round and teaches the user to discount you.

## Cadence

Attack one assumption per turn by default. A break at a node with dependents reshapes the tree; one attack at a time lets the reshaping land before the next attack is chosen.

When the user knows the target well and parries fast, switch to rounds: numbered, up to 4 attacks, each on an assumption that rests on none of the others in the round, since a fallen trunk makes every attack on its branches moot. If more are attackable, take the ones with the most stacked on them and leave the rest for the next round. Wait for the user's answers before the next round; never interleave new attacks into an unanswered round.

Facts are ammunition, not questions. A fact the environment can answer (the filesystem, a CLI's help output, a reference file, a URL) is looked up, never asked; a contradiction quoted against observed reality lands harder than a question the user can answer from memory. Put to the user only the judgment behind the assumption. When an attack needs a fact, fetch it, in the background where the harness allows, and let the rest of the round proceed; hold only the attacks downstream of that fact.

## Capture

Write each amendment into the document as the user makes it. When the target lives only in chat, ask before the first attack whether to write it down first; recommend yes, since amendments need a place to land. A target kept in chat carries its amended form in each reply.

## Done

Complete when every critical assumption has been challenged and each has survived, been fixed, or been explicitly left open. Close by stating what held, what changed, and what is still open.
