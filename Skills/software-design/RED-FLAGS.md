# Red-flag catalog

A red flag is a prompt to redesign, not a complaint: when one fires, sketch an alternate design that eliminates it, try more than one, before writing the finding. Judge every candidate fix by total complexity; a fix that grows the interface more than it shrinks the problem is worse than the flag.

Each named flag entry gives what it is, how to check it, and the fix move. The exhaustive sweep covers the named flags from **Shallow Module** through **Nonobvious Code**. The final supplemental checks apply where relevant but are not additional catalog flags.

## Shallow Module
An interface complicated relative to the functionality it provides: the cost of learning it cancels the benefit of not reading inside. Small modules tend to be shallow; so are getter/setter pairs and one-line wrappers.
Check: state what a caller must know versus what they get back. Would inlining the body at the call sites cost anything? Two fast signs: a complete interface comment would run longer than the body, or the call costs more to write than the body it wraps.
Fix: deepen, raise the interface to one call per whole computation, hide more, generalize, or merge into a neighbor: information hiding often improves by making a class slightly larger. When no deeper alternative exists, the shallow module stays and is counted as interface cost, not split further.

## Information Leakage
Anything in an interface is leaked by definition; the flag proper is the same knowledge living in the implementations of two modules, two classes that both know a file format, so one design change means N coordinated edits. Back-door leakage, where the shared knowledge appears in no interface, is worse: nothing marks it.
Check: for each design decision in scope, format, protocol, representation, policy, count the modules that would change if it changed.
Fix: ask "how can I reorganize so this knowledge affects only one class?" Merge the sharers, or extract the knowledge behind its own interface, worthwhile only if that interface is genuinely simpler than the knowledge it hides.

## Temporal Decomposition
Structure mirrors execution order, read-class, parse-class, write-class, instead of knowledge, so knowledge used at several moments gets encoded at each of them.
Check: do names describe phases (prepare/process/finish)? Do two stages know the same thing, a format or representation? Stages that share no knowledge may stay in phase order; the flag fires only on the shared knowledge.
Fix: structure around the knowledge needed for each task, not the order tasks run, one module per piece of knowledge, however many times it is used.

## Overexposure
The interface of a common operation forces callers to learn rarely used features, Java file streams that lose buffering unless you remember the wrapper.
Check: walk the most common call. How much of what the caller had to know did they actually want?
Fix: defaults that do the right thing unasked; rare features behind separate methods where common-case users never meet them.

## Pass-Through Method
A method that does nothing but forward its arguments to a similar signature: interface without functionality, signaling that no one decided which class owns the job, and a coupling, because a signature change below forces a matching edit above. Kin: adjacent layers exposing the same abstraction; piles of thin decorators; a variable threaded unused through every signature between top and bottom.
Check: does each method along the chain contribute significant functionality? Dispatchers do; so do N implementations of one interface, which sit in one layer and never call each other, so a same-signature chain across layers is never that case. Then write one sentence per class naming what it is responsible for; overlap between the sentences is the finding.
Fix: the interface to a piece of functionality belongs in the class that implements it, so expose the lower class directly or redistribute the work; merge the two only when they cannot be disentangled. Threaded state moves into a context object, per [DESIGN.md](DESIGN.md), "Different layer, different abstraction."

## Repetition
The same nontrivial code appears over and over: the abstraction beneath it hasn't been found.
Check: near-sameness counts, not just literal copies. A one or two line snippet, or one that would need several of the caller's locals passed in and out, is cheaper repeated than extracted.
Fix: factor it out, pays when the snippet is long and the replacement signature simple, or restructure so it executes in exactly one place (one error path instead of a copy per case).

## Special-General Mixture
A general-purpose mechanism contains code specialized for one particular use of itself, so the use case's future changes reach into the mechanism.
Check: could a second user adopt this class tomorrow without edits? What inside it names one caller's concepts?
Fix: pull the special-purpose code up into the layer that owns that purpose; leave the mechanism general below.

## Conjoined Methods
Understanding one method requires reading another and back again, or any two separated pieces of code readable only jointly.
Check: read each in isolation and count the jumps you had to take.
Fix: rejoin them, or re-cut so each side is understandable alone.

## Comment Repeats Code
The comment adds nothing over the adjacent code, often it reuses the very words of the name it annotates.
Check: could someone who has never seen this codebase write the comment from the adjacent code alone? Two usual shapes: a comment on every line, each restating its line; and a note before a call restating the callee's interface comment.
Fix: different words at a different level, add precision (units, bounds, meaning of null, resource ownership, invariants) or intuition (purpose, why, when invoked); the call-site note is deleted, the callee's comment is its single source.

## Implementation Documentation Contaminates Interface
An interface comment describes internals a caller never needs.
Check: would a caller need each statement to use the thing correctly or to predict its cost?
Fix: interface comment for callers; implementation notes inside the body. An implementation fact stays when callers experience it, such as concurrency that sets performance expectations, and a failure is described as callers see it, never as the recovery works. An interface comment that *can't* avoid the implementation is diagnosing a Shallow Module, redesign, not rewrite.

## Vague Name
A name broad enough to mean many things, `data`, `info`, `process`, `status`, conveys nothing and invites misuse.
Check: image test, from the name alone, would a stranger guess the referent?
Fix: precise, intuitive, few words; booleans as predicates; logically different things get different names (`fileBlock` vs `diskBlock`, one shared `block` cost six months of debugging).

## Hard to Pick Name
No simple name paints a clear picture, which hints the entity itself has no clear definition or purpose.
Check: try to describe the entity's single purpose, then name it. If every accurate name is vague or joins unrelated concepts, the entity is unclear.
Fix: treat as design feedback, split the variable doing two jobs; re-cut the method with no single purpose.

## Hard to Describe
A complete comment for the thing has to be long. A long interface comment is a complex interface; one that must recount the implementation's major features is describing a shallow method.
Check: write the complete interface comment before the implementation. Count the concepts and implementation details it must expose.
Fix: redesign until a short comment covers it completely, the design expressible in the fewest words is usually the best one. A variable that needs a long comment is usually holding two things; re-cut the decomposition.

## Nonobvious Code
Meaning or behavior can't be understood on a quick read. The reader is the judge: if a reviewer says it's not obvious, it isn't.
Check the usual causes: event handlers with no note on when they fire; generic containers (`Pair`, tuples) where a named type belongs; declared type differing from allocated type; behavior that violates conventions, undocumented at the interface and at the surprising site; a method's major blocks run together with no blank line between them and no leading comment saying what each does; parameter docs run together so a reader cannot tell where one ends.
Fix, in order: reduce the information the reader needs (better abstraction, fewer special cases); lean on conventions they already know; supply what remains through names and comments.

## Supplemental checks
- **Exception-heavy interface.** An interface throws or returns many error conditions, or reports ones the module could handle or define away. Fix with the ladder in [DESIGN.md](DESIGN.md), "Define errors out of existence."
- **Exported configuration.** Callers choose values the module could compute itself. Fix by computing the value or providing an automatic default.
- **Documentation-dependent design.** The interface is usable only with extensive documentation. Fix the design until a short, complete interface comment is enough.
- **Diff-only knowledge.** Future developers need information recorded only in the commit message, or the diff leaves comments invalid. Put the knowledge in the code and update every affected comment.
- **Invalid states in the representation.** Synchronized booleans, optional-field bags, repeated shape checks, casts, or partial operations encode a missing domain structure. Use a union, state machine, constructor, registry, or collection only when it removes those invalid states or repeated rules.
- **External representation leakage.** A module interface exposes wire, database, configuration, or framework types that callers should not know. Parse or adapt at the seam and keep the representation inside its owner.
- **Unowned shared mutation.** Several actors write one file, branch, key, or object without a real singleton invariant. Separate their write targets and aggregate at the read seam; when sharing is real, enforce one writer or structural synchronization.
- **Nonconvergent mutation.** A command or lifecycle operation changes outcome when repeated or resumed after a partial failure. Redesign around desired-state reconciliation and test reruns plus crash points.
- **Unpinned refactor.** A structural diff claims behavior preservation without a stable observable contract or equivalence baseline. Establish the smallest valid pin before moving the structure, per [DESIGN.md](DESIGN.md), "Pin behavior before structural movement"; the pin stays in the permanent suite only when it defends an observable contract, otherwise it is disposable.
