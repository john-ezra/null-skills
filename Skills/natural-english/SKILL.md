---
name: natural-english
description: Use when about to write an explanation, a document, or a reply longer than a few paragraphs.
disable-model-invocation: false
---

Every explanation, document, or reply longer than a few paragraphs gets two edits before it goes out. Strip what marks it as machine-written, then give it a human voice. Removing tells alone does not satisfy this skill. Docs, summaries, and generated files with prose in them count.

## Process

1. Detect. Read the whole text and mark every occurrence of every catalogue entry. Done when you have run every entry against every sentence, heading, and list item, not when you have marked the obvious hits.
2. Rewrite. Apply each entry's fix to each mark. Done when no mark remains unaddressed.
3. Voice. Run every voice test on the rewritten text. Done when each of the six has either changed the text or left it alone for a named reason, an absent trigger or a register that rules it out.
4. Audit. Reread and put one question to the result: what here would still tell a reader a machine wrote it? Fix whatever answers it. Done when the question has no answer.

Hold these through steps 2 to 4.

- Meaning stays. Add no claim, drop no claim, flip no truth value. When a fix calls for a source, a number, or a mechanism, use one you have; with none in hand, delete the sentence instead of inventing one.
- The author's register stays. Formal, casual, terse, or technical, the voice tests adjust within it and never override it.
- A fix that trips another entry is not done. Swapping a dash for parentheses is the usual case.

## Voice

Text with every tell removed and no voice still reads as machine output, so these tests carry the same weight as the catalogue.

- **Take positions.** A balanced, unweighted list of pros and cons is a tell. When you lay out facts, say what you make of them, good, a problem, a surprise. If a paragraph that evaluates something ends without your verdict, add one.
- **Vary sentence length.** In any run of four or more sentences whose lengths sit in a narrow band, break one short or let one run long.
- **Admit mixed reactions.** One unqualified adjective is weaker than a judgment that holds a quality beside its cost. At each one-word verdict, ask whether a second, conflicting reaction is real for you. If it is, state both. "The migration was fine" becomes "The migration was fine, and it cost us a week of frozen deploys."
- **Allow first person.** Where you express a judgment, observation, or experience, you may write "I". Writing "I" costs no professionalism. This is permission and no more; a register that excludes first person wins. If the text has bent into passive or impersonal constructions to avoid saying who thinks something, restore the person.
- **Let structure be uneven.** Flawless parallelism, uniform paragraph lengths, and sections all of one shape read as generated. If every paragraph or list item has the same shape, let one break it. This licenses unevenness, never a catalogue violation.
- **Be concrete.** Replace or follow a generic evaluation (worrying, exciting, useful) with the specific thing that provoked it, a scene, a number, a named case, a time. A sentence that would fit a different subject unchanged needs the detail that ties it to this one; add it. "The test suite is worrying" becomes "The test suite takes 40 minutes, so nobody runs it before pushing."

## Catalogue

### Characters and formatting

- **Dashes.** Flag every em dash. Replace it with a period when the set-off thought needs its own sentence, otherwise a comma. Those two are the only replacements. A reader who spots the dash as machine writing spots parentheses around the same clause the same way, so flag those parentheses too, along with an en dash and a single or doubled hyphen doing dash work. No exceptions; this is avoidance, not moderation.
- **Colons as glue.** Flag a colon that joins two clauses where the second is neither a list, an example, nor a definition of what came before. The common shape frames what the reader already knows or compares to something else, then a colon, then the point. Make the point its own sentence and drop the framing when the point stands without it. The rewrite says the same thing with no colon. "If you know git rebase this will feel familiar: the tool replays each commit" becomes "The tool replays each commit." Keep a colon that introduces a list, an example, or an explanation of a term.
- **Bold on names.** Flag bold on a proper noun, product name, or acronym applied for no reason beyond its being a name. Remove the bold. Bold that stresses a point falls under the next entry when it forms a label and otherwise stays.
- **Bold label, colon, restatement.** Flag a list item or paragraph that opens with a bold word or phrase, a colon, and a first clause that repeats the label. Rewrite it as plain prose with no label. One shape passes and is no tell. The reader sees a short bold phrase closed by a period, then a sentence carrying detail the bold phrase left out, as in "**Retries.** The client retries twice, 200 ms apart, then raises."
- **Heading case, emoji, quotes.** Flag a heading with capitals beyond the first word and proper nouns; use sentence case. Flag an emoji opening or decorating a heading or list item; remove it. Flag curly single or double quotation marks; use straight ASCII quotes.

### Words and phrases

Match these regardless of case, including plurals, tenses, and -ing forms.

- **Grandiose framing.** Flag phrases that assert significance, permanence, or depth without a fact behind them: "testament to", "pivotal moment", "deeply rooted", "evolving landscape", "setting the stage for", "indelible mark", "a cornerstone of", "plays a vital role in", "at the heart of", "marks a turning point", "rich history", "in today's fast-paced world". Delete the phrase and state the event or fact it was inflating.
- **Brochure adjectives.** Flag praise that measures nothing: "vibrant", "breathtaking", "renowned", "groundbreaking", "must-visit", "stunning", "iconic", "world-class", "bustling", "picturesque", "nestled", "unparalleled", "cutting-edge", "state-of-the-art". Replace it with a neutral description of the thing.
- **Machine vocabulary.** Flag words that show up in model output at many times their rate in human prose of the same register: "delve", "crucial", "additionally", "enduring", "enhance", "garner", "interplay", "robust", "nuanced", "multifaceted", "intricate", "realm", "foster", "underscore", "showcase", "comprehensive", "meticulous", "embark", "elevate", "streamline", "holistic", "transformative", "empower", plus "tapestry" and "landscape" in their figurative sense; the literal woven or geographic senses pass. Substitute the plain everyday word.
- **Ornate copulas.** Flag verbs standing in for "is" or "has": "serves as", "acts as", "functions as", "stands as", "is home to", "comes equipped with", and "boasts" and "features" as verbs only. Write "is" or "has".
- **Figurative technical nouns.** Flag words taken from science, engineering, or business strategy for the technical sound. Most have an everyday equivalent in plain words; use it: "paradigm" becomes "approach", "north star" becomes "goal", "flywheel" becomes "cycle", "substrate" becomes "base", "bedrock" becomes "basis", "locus" becomes "place", "nexus" becomes "link", "vantage" becomes "viewpoint", "modality" becomes "mode", "endgame" becomes "final goal", "ecosystem" becomes "community" or the plain list of tools, "synergy" becomes "combined effect", "trajectory" becomes "direction", "runway" becomes "months of money left", "moat" becomes "advantage", "gold-plating" becomes "doing more than the job needs", "orthogonal" becomes "independent". Flag these in the figurative sense alone: "primitive" as a noun; "scaffolding" for a structure of code or ideas, not the physical structure; "wedge in" meaning add; "evacuate" for moving code out of a place; "lift" for moving code, not physical movement; "bandwidth" for a person's time, not a network link; "vector" for an approach, not a geometric quantity; "framework" for a way of thinking, not a software library; "lens" for a viewpoint, not the optical tool; "velocity" for pace of work, not physical speed; "surface area" for exposed code, not geometry. Where the metaphor stands for a real mechanism, a ratchet or a harness, name the mechanism or say in plain words what it does.
- **Elaborate synonyms.** Flag a Latinate word or a multi-word conditional or causal phrase whose plain equivalent means the same thing. In most cases the elaborate form gains no precision over the plain one. "utilize" becomes "use", "leverage" becomes "use", "facilitate" becomes "help", "commence" becomes "begin", "prior to" becomes "before", "in the event that" becomes "if", "due to the fact that" becomes "because", "ascertain" becomes "find out", "numerous" becomes "many".
- **Padding.** Shorten phrases that reduce to one short word: "in order to" becomes "to", "at this point in time" becomes "now", "has the ability to" becomes "can", "a number of" becomes "some". Delete phrases that announce the writer is about to say something and carry nothing themselves: "it is important to note that", "it is worth mentioning that", "it should be noted that", "needless to say".
- **Stacked hedges.** Flag two or more hedging devices (modal verbs, "potentially", "possibly", "it could be argued") on one claim. Collapse them to a single modal such as "may".
- **Assistant talk.** Flag anything that exists because the writer is playing helpful assistant. Closing offers such as "I hope this helps", "Let me know if you have any questions", "Feel free to reach out". Eager assent such as "Certainly!", "Of course!", "Absolutely!", "Great, let's get started". Discovery exclamations such as "Aha!", "Interesting!", "Now I see the problem!". Delete.
- **Flattery.** Flag praise of the reader's question, insight, or correctness, the opening line of a reply being the usual spot: "Great question", "Good catch", "You're absolutely right", "Excellent point". Delete it and start with the substance.
- **Missing-information excuses.** Flag a sentence that excuses a gap by calling details scarce, limited, or unavailable. Find the information and state it, or delete the sentence. Never invent it.

### Sentence shape

- **Not just X but Y.** Flag any frame that denies a smaller claim to assert a larger one ("not only", "not merely", "more than just"). State Y and drop the frame.
- **Forced triads.** Flag three parallel items where the third adds nothing or the facts support two or four. Use the number the content supports. A triad whose every member carries distinct information stays.
- **Synonym rotation.** Flag one referent named by two or more terms within a paragraph for variety alone. Pick one term and reuse it throughout. Terms that mark a real distinction are not rotation.
- **Fake ranges.** Flag "from X to Y" or "ranging from X to Y" where X and Y are not points on a scale the reader can order. List the items without the range frame.
- **Unsupported participial clauses.** Flag an "-ing" clause, wherever it sits in the sentence, that asserts significance, effect, or purpose without evidence: "highlighting", "ensuring", "underscoring", "showcasing", "reflecting", "fostering", "paving the way for". Cut the clause, or make it its own sentence backed by a named source or fact.
- **Unnamed authorities.** Flag a claim credited to a group nobody can identify: "experts believe", "some critics argue", "industry reports suggest", "studies show", "many people feel", "it is widely believed". Either cite a specific named source or cut the claim.
- **Source lists.** Flag a run of named outlets or organizations cited together with nothing about what any of them reported. Drop all but one outlet, then say what that outlet reported.
- **Adversity then triumph.** Flag a sentence that names unspecified difficulties and then asserts continued success or growth in generic terms. Replace it with the specific difficulties and specific outcomes, or delete it.
- **Empty closers.** Flag a final sentence or paragraph of generic optimism, momentum, or promise with no plan, date, or fact. Supply the plan, date, or fact, or end without a closer.
- **Passive voice.** Flag a form of "be" followed by a past participle where an actor exists. Make the actor the subject and use the active verb. "The regression was introduced by the retry change" becomes "The retry change introduced the regression." Two cases keep the passive, an unknown actor, or an actor the reader has no use for.
- **Adverbs propping weak verbs.** Flag an adverb on a verb or adjective, especially adverbs of degree or manner. A verb that needs its adverb to work is the wrong verb; swap the verb. Prefer, in order, a stronger single verb or adjective, then the measured number or change in place of a degree adverb, then deletion.
- **Dense sentences.** Read each sentence once at speed; if you had to go back over any part of it to resolve it, flag it. Split it, or drop clauses, until each sentence carries one idea. One idea per sentence is the target.

### Substance

- **Impressions instead of mechanisms.** Flag a sentence that describes a benefit or property as a feeling, quality, or impression (close, readable, convenient, follows along) instead of a mechanism, an action, or a number. Two tests, in order. Test one asks what the reader should do, or what fact or number the reader should come away with. Rewrite the sentence as that and nothing more. No such restatement, no sentence. Test two asks whether the same sentence could sit unchanged in the docs of an unrelated product. A yes means the sentence is not about this product, so cut it. "The client library is easy to configure" becomes "The client reads `API_URL` and `API_TOKEN` from the environment and raises `MissingConfig` when either is missing." "The new parser feels much faster" becomes "The new parser reads a 40 MB log in 0.8 s where the old one took 6 s."
