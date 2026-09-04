---
name: spec
description: Write specs from an intent. Use when an intent is ready to break into work items, when its frontier needs slicing or re-slicing after work lands, or when specs need splitting, merging, re-scoping, or killing before pickup.
disable-model-invocation: false
---

Slice an intent into thin, session-sized **specs**: what each slice delivers plus its acceptance criteria, nothing more. How a slice gets built is not decided here.

## 1. Ground in the intent

Read the intent, especially Stop criteria, Constraints & decided, Out of scope, and Open questions. Then survey the existing specs and what blocks what, to see what is done, in progress, or already specced. Done when you can name the **frontier**: the work whose blockers are done or absent.

## 2. Slice the frontier

Cut the frontier into **vertical slices**: each spec delivers a narrow but complete, independently verifiable path through the work, never a horizontal layer ("all the models", "all the endpoints") that proves nothing on its own. Size each slice to one session: one agent can plan, build, and verify it without a handoff. One task per slice: if the title needs an "and", it is two slices.

Decomposition is progressive. Slice finely only at the frontier; leave work behind unfinished blockers as coarse placeholders, a title and a sentence, until the frontier reaches it. An open question in the intent blocks the same way: work that depends on its answer stays a placeholder, and the question is answered when the frontier reaches it, not before. Returning to re-slice as work lands is the mechanism working, not a planning failure. Until a spec is picked up, it is malleable: split, merge, re-scope, or kill freely.

Done when every piece of the frontier sits in exactly one slice, each slice is vertical and session-sized, and everything behind an unfinished blocker is a placeholder.

## 3. Get approval

Present the proposed breakdown to the user. Per spec: **title · what blocks it · what it delivers**. Iterate, splitting, merging, re-scoping, or killing, until the user approves the set.

## 4. Write the specs

Spec template:

```markdown
# <What this slice delivers, in one imperative sentence>

Intent: <the intent this slice serves>

## Delivers

<What exists and works when this slice is done: the observable result, 1 to 3 sentences.>

## Acceptance criteria

- <Checkable condition an implementer can verify.>
- <...>
```

Done when every approved frontier slice has a spec from the template, every placeholder is still a title and a sentence, and the blocking order between them is stated once, outside the spec bodies. A spec body is the requirements contract for one slice; ordering does not belong in it.
