# Module design

A deep module offers a lot of behavior behind an interface a caller can hold in their head. That is the shape this skill wants from every module at every scale: callers get leverage, maintainers get locality, and tests drive the module through the same interface callers use.

## Vocabulary

Use these terms for the architecture.

- **Module**: a unit of code with an interface and an implementation, at any scale: a function, a class, a package, a slice of a service. Say module; component, service, and unit each name only one scale.
- **Interface**: everything someone working outside the module must know to use it correctly. The formal part is what the language can see and check: signatures, types, declared exceptions, public fields. The informal part is whatever else a correct call depends on: behavior, invariants, ordering, undeclared error modes, required configuration, performance expectations, and only a comment can state it. Say interface; API and signature name only the formal part, and the informal part is where most of the cost hides.
- **Implementation**: the code behind the interface.
- **Abstraction**: the view of the module its interface presents, with every detail that does not matter to a caller left out. It fails two ways: a detail no caller needs is still in it, which is cost, or a detail a caller must act on is missing, which is a false abstraction, simple to read and obscure in use. Before hiding a detail, check that no caller has to react to it (when buffered writes become durable stays in the interface for a caller that orders its writes, whatever it costs); then look for the design under which the fewest details matter to anyone.
- **Depth**: functionality gained per interface learned. A module is deep when a small interface yields a lot of behavior and shallow when learning the interface costs about as much as the behavior is worth. Interface size is the cost and implementation size is evidence of what is hidden, so a line count never by itself decides a split or a merge, but a large body behind a small interface is what deep looks like and a small interface over a few lines hides little. Score an interface by what the common caller must learn: features the common caller never meets add almost nothing to its cost. An interface with several real implementations is deeper for each of them, since its knowledge is learned once and reused; an abstract interface over one implementation adds no depth.
- **Seam** (Feathers): a place where behavior can be changed without editing that place; a module's interface sits at one. Placing a seam is a decision on its own, separate from what goes behind it. Say seam; boundary carries no notion of substitution.
- **Port**: the interface at a seam that adapters satisfy, in the sense of Cockburn's ports and adapters.
- **Adapter**: a concrete implementation plugged in at a port, named for the role it fills (the mail sender, the clock), never for what it contains.
- **Test adapter**: the adapter a test supplies at a port. Use this one term for whatever a test plugs in at a port, whatever its internals.
- **Local substitute**: a real dependency swapped for an equivalent that runs inside the test process, such as an in-memory database engine or a temporary directory. No port is involved and the seam stays internal.
- **Leverage**: what callers gain from depth: each fact of interface they learn buys them more behavior.
- **Locality**: what maintainers gain from depth: a change or a bug fix lands in one place because the knowledge lives in one place.

## Information hiding

Each module encapsulates design decisions that appear nowhere in its interface: a storage layout, a wire format, a retry policy, an algorithm, an assumption about the workload. When the decision changes, the module changes and its callers do not. Organize modules around knowledge, not around the order in which work runs: the module that knows a format owns every use of that format, however many phases touch it.

The common case must need the least knowledge. Compute what the module can compute and let the caller override only when the caller knows better. Keep rare features out of the common call and put them behind their own methods. Return results built for the caller's purpose rather than the module's internal structures: a returned internal type makes the representation part of the interface, and a returned reference to live internal state adds an unwritten rule, do not modify this, that every caller must learn. Marking a field private hides nothing by itself; the test is whether any public method lets a caller depend on the field's nature or use, and a getter and setter pair fails it, exposing the representation it claims to encapsulate one field at a time.

Hiding improves when every piece of code that knows one decision sits together, so a class that grows to gather them hides more than the two smaller classes that shared the knowledge. The same rule runs inside the module: give each private method one piece of knowledge to keep from the rest of the class, and read or write each field in as few places as possible, since every extra use site is a dependency inside the class. Information Leakage and Temporal Decomposition in [RED-FLAGS.md](RED-FLAGS.md) are the named failures of this section.

*Too far:* hiding is only for knowledge no caller needs. A value a caller's correctness or performance depends on is exposed, after first trying to let the module compute it; a hidden detail a caller needed is a false abstraction, not depth.

## Judging depth

Write down two lists: what a caller must know, formal and informal, and what they get back. Then push on the interface: can methods be fewer, can parameters be simpler, can more of the work move inside, can the call go away? The deepest shape has no interface at all: a module that does its job unasked removes an obligation from every caller, and removing a call beats simplifying it. Internal parts do not count against depth: a module built from many pieces costs the caller nothing as long as none of them show through.

Deletion test: imagine the module gone and its callers doing the work. If nothing gets harder, the module was a pass-through and its interface was pure cost. If every caller now repeats its work, the interface was paying for itself. Run it both ways: on a module you doubt and on a merge you propose.

Tests enter where callers enter. A test that must reach past the interface, or that breaks when only the implementation changes, is testing the wrong shape. A module may have internal seams where a test injects a test adapter or a local substitute; the observation still comes back through the external interface. [DEEPENING.md](DEEPENING.md) decides which dependencies get a port and what a test supplies at each.

## Seam placement

Put the seam where knowledge changes hands: one side owns a decision, the other side needs only its result. Among those places choose the one where callers need to know the least, which usually means moving the seam outward until the whole computation is one call and the representation stays inside. Callers on both sides shape themselves to the seam, so moving it later costs more than moving what sits behind it. A seam with a single adapter is indirection, not a port; a port is justified only when at least two adapters are real, typically production and test.

## Over-deepening brake

Merging can go too far. When a caller of one operation must learn concepts that belong only to another operation, the module absorbed two pieces of knowledge whose callers do not overlap. Split when each half's callers can then ignore the other half and no complexity reappears at the call sites; run the deletion test on the proposed split before making it. Depth is per unit of interface, so a module that doubles its behavior and doubles its interface is no deeper than before, only bigger.

A design question about a module is answered only when four things each have a verdict: the interface, formal and informal; the knowledge the module hides; the seam placement; and the brake.
