# Design It Twice

Design at least two genuinely different interfaces before committing. Confidence that one shape is right does not waive the second: a candidate you expect to lose still earns its place, because its weaknesses make the winner's trade-offs explicit. The method applies at every level, from a system's split into modules down to one method's signature. Uses the vocabulary in [MODULE-DESIGN.md](MODULE-DESIGN.md).

## Process

### 1. Frame the problem space

Before exploring alternatives, explain:

- The callers and their common operation
- What any interface must guarantee, whatever its shape
- The dependencies involved and their category from [DEEPENING.md](DEEPENING.md), when applicable
- The data model, dominant access paths, and owner of every mutable write
- Which decision, if any, needs an executable prototype because an interface sketch cannot settle it
- Two or three plausible next changes, so the comparison can rehearse them
- A rough code sketch that makes the constraints concrete without proposing an interface

Show this framing to the user, then continue to the alternatives while they consider it.

### 2. Match the exploration cost to the decision

Use parallel exploration for interfaces that are expensive to change later:

- Public interfaces and callers you do not control
- Storage schemas, wire formats, and other persisted shapes
- Seams with many callers, where reshaping means changing them all

Every alternative starts with a caller usage example, then a short, complete module comment plus comments and signatures for its important public methods, with bodies empty. When the representation is the decision, add declarations and comments for the important member variables. Each alternative also states what the implementation hides, its dependency and adapter strategy, and its trade-offs.

For a cheap-to-change interface with few controlled callers and no persisted shape, sketch two alternatives inline that differ in shape, not size; a trimmed copy of the first teaches nothing. Compare them and pick without using subagents.

When the choice depends on observable timing, layout, library behavior, or another empirical fact, build the smallest isolated disposable prototypes that distinguish the alternatives. Keep prototype code outside the production path, record the observation, then return to interface design. A prototype informs the decision; it does not become the interface by accident.

*Too far:* an alternative is a sketch of the important methods, an hour or two for a class, not a full design. Pinning every feature of every candidate spends the implementation's time on designs that will be discarded.

### 3. Explore expensive interfaces in parallel

For an expensive interface, dispatch at least three parallel subagents. Give each the same technical brief, callers, constraints, dependencies, knowledge hidden behind the seam, and project vocabulary, but a different design constraint:

- Smallest surface: the fewest operations that still cover every caller in the frame, each doing a whole job.
- Widest reach: serve uses the frame did not name and leave room to extend without reshaping.
- Common caller first: the default call takes nothing the common case does not already have.
- Seam first, when dependencies cross it: shape the ports and adapters before the operations.

Before dispatch, write the comparison criteria: the list in step 4 always applies; add any criterion the frame makes decisive, three at most. Give every subagent an isolated output and require a rationale. The criteria belong to the comparison; do not let candidates redefine success.

Each subagent returns the required material above in this order:

1. A caller usage example
2. The interface comments and signatures, including parameters, invariants, ordering, and error modes
3. What the implementation hides
4. The dependency and adapter strategy
5. Trade-offs: which callers get the most from it and which get little
6. A rationale naming the constraint that shaped the candidate and the tempting alternatives it rejected

### 4. Compare and recommend

Present each design separately, then compare them on:

- Ease of use for callers, weighted most heavily
- Interface simplicity and common-case knowledge
- Depth, locality, and seam placement
- Information hiding and error modes
- Generality without speculative features
- Two or three plausible next changes named in the frame, and whether each stays local under this alternative
- Whether the design permits an efficient implementation

Recommend one design or a deliberate hybrid. If neither is attractive, use their flaws to produce a third.

Record why the recommendation rejected each losing shape and which ideas, if any, the selected hybrid incorporates. Convergence across candidates is evidence that a shape is natural. Wild divergence is evidence that the frame or comparison criteria need revision, not a reason to average incompatible designs.

Done when the cheap branch has two complete alternatives or the expensive branch has at least three; the alternatives differ in interface shape rather than only names or parameters; every alternative contains all required material; every comparison dimension has a verdict; and one recommendation explains why it creates the lowest total complexity. Then return to the software-design skill for shaping, implementation alternatives, and its final gate.
