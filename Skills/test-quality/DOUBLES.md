Collaborator control for `test-quality`: how real each collaborator must be, what a double may do, and when an interaction may be asserted. Every dependency the behavior crosses gets a rung and every double one of the two allowed jobs. Examples are invented.

## The ladder

Take the highest rung that stays controlled and deterministic; moving down a rung needs a reason you can state.

1. **Real in-process code.** Owned collaborators with no I/O run as they are. Pure computation, value objects, parsers, pricing rules, and the subject's own helpers are never doubled.
2. **Local or framework facility.** A temporary directory for a filesystem, an embedded or disposable database, a clock the test sets, an in-process HTTP transport, the framework's test application host.
3. **In-memory implementation behind an existing port.** Only when the system already owns the port, so substitute and production adapter satisfy the same interface. A port introduced so a test can inject something is a design change and goes through `software-design` and its DEEPENING.md before any test uses it; where the verdict allows injection at an internal seam, results are still observed only through the module's external interface.
4. **Stand-in for a genuine external system.** A vendor API, mail relay, payment processor, or mapping provider, and only when the real one cannot be reached from the test, answers differently run to run, would cause harm, bills per call, or would slow the test past usefulness.

## What a double may do

- **Supply controlled inputs.** The response, timestamp, or failure the scenario needs from outside the subject: a payment simulator answering "declined, insufficient funds".
- **Capture contractual effects.** Record the outgoing request, message, or write the contract promises so the test can assert on it.

Nothing else: not that method A called method B, not how many times a formatter ran or whether the cache was consulted before the store, and never the subject or its pure logic. A refactor that inlines an internal call must stay green; a test that needs the discount rule to return a fixed value is about something else and should say so.

## Interaction assertions

- **Boundary only.** Assert an interaction only at an established external boundary where the interaction is the contract: the confirmation email is sent, the webhook is posted, the ledger entry is written. A boundary the module reaches through its own internals does not qualify.
- **Fields and count.** The fields a consumer depends on and how many times the effect happened; not the full payload, argument order, or headers no consumer reads.
- **Read-back wins.** An effect observable through the subject's own interface is asserted there instead.

## Complexity cap

A double returns canned data or records calls and never branches on its inputs the way production does. When it needs conditional behavior, a state machine, or setup longer than the test body, replace it with a built-in testing aid such as the library's test mode or the framework's fake transport, a compact stateful substitute behind the existing port such as an in-memory map honoring put and get, or a broader integration-level test against the real dependency. A substitute that needs methods the production adapter lacks, or the reverse, means the port is wrong or the test asks the wrong question; neither is fixed inside the test.
