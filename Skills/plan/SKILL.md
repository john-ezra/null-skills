---
name: plan
description: Implementation planning. Use when converting a spec into an implementation plan, picking up specced work, or deciding whether a spec needs a plan at all.
disable-model-invocation: false
---

Convert a spec into the literal implementation plan: a frozen snapshot of *how*, taken at pickup, after all decisions are made. The spec stays the requirements contract; the plan is the route. Non-goals: writing or re-scoping the spec, and implementing.

## When to skip

Trivial work runs off the spec alone. Trivial means the spec's acceptance criteria already imply the obvious implementation: one clear change, one obvious place, no ordering or risk worth writing down. If you would restate the spec in different words, skip the plan. Done deciding when you can say which side the spec falls on and why.

## Steps

1. **Read the spec.** Take in what it delivers, its acceptance criteria, and what blocks it. Read the code the change touches. Resolve every open decision now: look up what the environment can answer, put every judgment call to the user. The plan comes after decisions, not to defer them.

2. **Draft the plan** using the template below. Fill every section with specifics: real file paths, real commands, real expected outputs.

3. **Interrogate the draft before accepting it.** Answer in the plan itself, not in your head:
   - What could this break?
   - Which step is riskiest, and what is the fallback if it fails?
   - Which approaches were rejected, and why?

   Then apply the completion test: **an agent that has never seen this conversation could implement the spec from the plan alone.** If any step needs the conversation to make sense, the plan is not done.

## Template

```markdown
# Plan: <spec title>

## Files that change
<each file touched, what changes in it; files created, and why>

## Order of work
<numbered steps, in dependency order>

## Risks
<what this could break; the riskiest step and its fallback; approaches rejected and why>

## Proof
<one check per acceptance criterion in the spec, quantifiable: which test passes, what the screenshot matches, what the endpoint returns>
```
