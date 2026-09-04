---
name: shape
description: Shape a nebulous idea into a concrete one through a collaborative interview. Use when the user wants to shape or brainstorm an idea, want, design, or document that is not yet clear.
disable-model-invocation: false
---

Collaborative interview that turns fog into a clear picture.

## The tree

Before asking anything, map the whole decision space as a tree: every decision branches into the decisions that hang off it. An edge means dependency. "Where does the spec live" cannot be asked before "what is a spec to you" has an answer.

The **frontier** is the set of decisions whose prerequisites are all settled, the ones askable right now without guessing at answers not yet heard. Hold a question behind its open prerequisite. Every answer reshapes the tree and pushes the frontier outward; recompute it after each exchange.

Walk the tree once before the first question. If that pass finds every branch already settled by what the user has said plus what the environment answers, say so and stop.

## Stance: collaborative architect

Ask generative questions, "what should X be?", never "is X sound?". Ship every question with a recommended answer: your best position given everything settled so far, stated so the user can confirm with a word, correct with a sentence, or park it; a parked decision holds its subtree. Recommend honestly; a hedge ("could go either way") wastes the user's turn.

You are co-designing, so propose structure the user hasn't asked for: name the decisions the idea implies but the user hasn't seen yet, and put them on the tree.

## Guard: the user decides

Recommendations lead; the user rules. A recommendation the user has not confirmed is a proposal, not a decision. Never record it as decided, never treat it as a settled prerequisite, and never let it narrow a downstream question as if it were the user's choice. When an answer surprises you, reshape the tree around their answer, not your prior.

## Cadence

Ask one question per turn by default. Shaping starts in fog, and in fog any answer can reshape the tree; one question at a time lets each answer land before the next is chosen.

When the user knows the domain and most answers will be quick, switch to rounds: ask the frontier, numbered, up to 4 questions per round. If the frontier is larger, take the highest-leverage subset and leave the rest for the next round. A round holds the frontier, never a quota. Wait for the user's answers before the next round; never interleave new questions into an unanswered round.

Only judgments reach the user. A fact the environment can answer (the filesystem, a CLI's help output, a reference file, a URL) is looked up, never asked. When a question needs a fact, fetch it, in the background where the harness allows, and let the rest of the round proceed; hold only the questions downstream of that fact.

## Capture

Before the first question, establish capture permission once. Use the request when it explicitly authorizes document edits or asks for discussion only. Otherwise, ask whether to update the existing document; when there is none, ask whether to create one, recommending yes when the tree is too large to hold in a reply. Agreement with a proposed decision is not permission to write a document.

With capture permission, record each decision as the user confirms it and each branch as they park it, without asking again for each edit. Without permission, leave documents untouched and carry the decided and parked lists in each reply. Capture immediately in the chosen place, not at the end.

## Done

The decision frontier is empty: every branch of the tree visited, every decision either resolved by the user or explicitly parked as open, nothing silently assumed. Close by stating what was decided and what was parked.
