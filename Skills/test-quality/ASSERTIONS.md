Assertion shapes for `test-quality`. Every assertion in scope gets a verdict against every entry in both lists. Examples are invented.

## Required patterns

- **Caller's outcome.** Assert what the caller receives: the return value, the raised error, or the external effect the contract promises. `assert repo.save.call_count == 1` where the saved record can be read back through the public interface is a weaker statement of the same fact; interaction assertions belong only at external boundaries, under [DOUBLES.md](DOUBLES.md).
- **Independent expected value.** The expected side is a literal from the spec, a hand calculation, a trusted fixture, or a separately validated implementation. `assert total == 1071` protects the pricing rule; `assert total == round(subtotal * (1 + rate))` agrees with every bug the rule has.
- **Configuration proven by effect.** Change the setting and assert a different observable output: two retries are proven by a server that fails twice then succeeds and one that fails three times then raises. `assert client.timeout == 5` proves the constructor stores an argument.
- **Sit on the limit.** Where strict and inclusive comparison could be swapped, the input is the limit itself: a 10 MB cap is tested with 10 MB accepted and 10 MB plus one byte rejected.
- **Precedence-separating inputs.** Where rules compete, each plausible ordering picks a different winner: a 10% loyalty rule, a 15 unit coupon, and a 5% seasonal rule on a 100 unit cart give 90, 85, and 95, so one assertion tells "largest wins", "first match wins", and "last match wins" apart.
- **Order-sensitive data.** Insertion order, natural sort order, and expected order all differ. `[b, a]` sorted to `[a, b]` cannot tell a correct comparator from an alphabetical fallback; `[(2, "b"), (1, "a"), (2, "a")]` ordered by count then name can.
- **Transition plus resulting state.** One scenario asserts the transition's own result and the state readable afterwards: `close()` returns the final balance and a following `deposit()` raises `AccountClosed`.
- **Usable error structure.** Assert the error's type and the fields a caller acts on: a code, the offending field name, a retry-after value. Exact wording only where it is an explicit product commitment such as a documented CLI message; `assert "not found" in str(err)` breaks under copy edits and passes when the wrong error carries similar words.
- **One reachable failure.** A failure-path test sets up exactly one failure and asserts it; if two could fire, fix the setup. `assert status in (400, 422)`, `assert result in (None, [])`, or a try/except that passes on both paths proves neither outcome.
- **One scenario per test.** Several assertions belong together only when they jointly establish the one promise the test's name states.
- **Parameterize identical contracts only.** Rows of a case table share one setup shape and one promise and vary data only; a row that needs its own explanation or secures a different promise is its own test.

## Rejected patterns

- **Private reach-through.** A private field, an underscored method, or a deep internal import used so a line becomes reachable. The effect either shows at the interface or is not a promise; if it should show and cannot, the test waits on `software-design`.
- **Broad snapshot for a narrow promise.** A whole-payload golden file guarding one field fails on every unrelated change and passes once regenerated over the wrong value. Assert the field.
- **Incidental order.** The sequence of internal calls, log lines, or dictionary keys no caller depends on.
- **Copy-pasted equivalents.** Two tests whose inputs differ without changing which mistake they catch. Keep the one whose data separates right from wrong more sharply.
