# Deepening: dependencies, seams, and tests

This file uses the vocabulary of [MODULE-DESIGN.md](MODULE-DESIGN.md). MODULE-DESIGN.md decides whether a deepening, merging shallow modules behind one deeper interface, improves the design; this file decides how the chosen design is implemented and tested given its dependencies.

## Dependency categories

Sort every dependency of the candidate module into one of four categories. Each category fixes two verdicts: whether the seam gets a port or stays internal, and what the test supplies there.

- **Pure computation.** In-process work with no I/O: parsing a query string, pricing a cart under tax rules, diffing two trees. The seam stays internal and the test supplies nothing; call the interface with inputs and check outputs.
- **Locally substitutable.** A real dependency with an equivalent that runs inside the test process: a relational store that also runs embedded, a filesystem pointed at a temporary directory, a clock read through one call. The seam stays internal and the test supplies a local substitute, so the production code path runs unchanged against it. Prefer this category over a port whenever a substitute exists, because the test then exercises the dependency's real behavior.
- **Owned remote.** A system your team controls, reached across a network: a rate limiter you deploy, an internal ledger, a worker pool behind a queue. Put a port at the seam and keep the logic in the deep module; a production adapter carries the transport (client, serialization, retry and timeout policy); a test adapter satisfies the same port in memory. The logic stays in one deep module although its deployment spans the network, and the tests exercise it without a socket.
- **Foreign.** A vendor system outside your control: a payment processor, a mapping provider, a mail relay. Place a port at the seam, sized to what the module needs rather than to the vendor's surface. The production adapter parses the vendor's representation and errors into the port's terms; the test adapter answers in those terms, including every failure mode the module must handle. Checking the production adapter against the live vendor is a separate contract check, outside the module's tests.

## Seam discipline

- A port is justified by the two-adapter rule, and production plus test are the usual two. When a local substitute would serve in place of the test adapter, the substitute wins and the seam stays internal.
- A module has one external seam, its interface. Seams inside the implementation are internal; tests may inject a test adapter or local substitute at an internal seam and observe results only through the external interface.
- An internal seam enters the interface only when a production caller needs to choose the adapter. Wire the test adapter where the module is assembled and keep the port out of the operations callers invoke.

## Testing after a deepening

- New tests sit at the deepened module's interface and assert observable results: return values, state readable through the interface, and effects that arrive at an adapter through a port.
- Retire the tests of the merged shallow modules once interface-level tests cover the same behavior; coverage is replaced rather than layered. Any survivor that fails on an implementation-only change was pinned to the implementation: rewrite it against the interface or remove it.

The dependency question is settled only when every dependency of the candidate has a category, each seam has its port-or-internal verdict and its named test adapter or local substitute, and the retired and new tests are listed.
