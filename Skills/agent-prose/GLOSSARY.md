The domain model behind `agent-prose`. Read the entry for each term a decision turns on, plus every neighbouring entry it names; the distinctions are the content, not the definitions. Complete when the decision can be stated in these terms and no neighbouring entry contradicts it.

## Reachability

- **Repeatable process.** The quality every rule here serves: two runs of the same instructions walk the same decision procedure even when their correct outputs differ. It is not output uniformity; a document that sends deliberately divergent work through one procedure is well written. Reliability and cost are effects of it, not the target.
- **Listed skill.** A skill whose description sits in the model's skill list on every turn. The model can pick it from a request, another skill can route to it by name, and a person can still run it by hand. It pays a standing cost for that reach.
- **Manual skill.** A skill with `disable-model-invocation: true`. Its description is not in the model's list, so nothing picks it and it pays no standing cost. Only a person runs it, by name; another document may tell the person which one to reach for but never tells the agent to open it.
- **Description.** The `description` frontmatter line. For a listed skill it is the text the model matches a request against, which makes it the top-level pointer to the skill's body rather than a summary for people. For a manual skill only people read it.
- **Pointer.** Loaded text that names material outside the context and says when to open it. Its wording decides whether the material is ever reached, which is what separates it from a bare link. A link with no condition is a pointer that fires on nothing.
- **Standing cost.** The tokens and attention that always-present text consumes on every turn of every session, whether or not it is used. Listed descriptions and context files pay it. Recall burden is the other side of the same trade.
- **Recall burden.** What a person must remember about which manual skills exist and when each applies. Paid by the person, not the model.
- **Menu skill.** A manual skill whose only content lists other manual skills and the moment a person would want each. It lowers recall burden but cannot run what it lists; the person still invokes them, so it is not a dispatcher.
- **Granularity.** How finely behavior is split across skills. Each extra listed skill adds standing cost; each extra manual skill adds recall burden.
- **Path.** One substantively different case a document handles, with one trigger. Different phrasings of the same case are one path and share that trigger.

## Content placement

- **Placement ladder.** Three rungs in order of immediacy: steps in the main file, then support every path needs after them, then support only some paths need in a sibling behind a pointer. Independent of listed against manual; either mode can use any arrangement.
- **Step.** An ordered unit of work the agent performs, carrying a done condition.
- **Support.** Definitions, facts, parameters, examples, and conditional rules consulted as needed rather than executed. Local in the main file, disclosed in a sibling, shared in a plain file several documents point to.
- **Shared file.** A plain file with no frontmatter holding support that more than one document uses. It cannot fire on its own, which is the point.
- **Disclosure.** Moving support out of the main file behind a pointer. The gain is attention and reliable execution on the main path, not tokens saved. Disclosing material every path needs is fragmentation, not disclosure.
- **Co-location.** Keeping a concept's definition, its operating rules, and its exceptions under one heading. It joins complementary pieces once; stating one meaning in two places is duplication.
- **Overlength.** The main file is too long even though every line is live and unique. A failure on its own, separate from stale buildup and duplication, which also add length and get fixed differently.

## Runtime steering

- **Anchor.** A short, well-known term whose learned meaning pulls behavior the way you want when it is repeated: "code review", "load case", "vertical slice". A coined label has no such pull until it is defined.
- **Done condition.** The observable condition that ends a step, or the coverage bar a flat rule set must meet. Two independent dimensions: whether completion can be recognized, and how much work it demands. A scope boundary is different; it names work that never belonged to the task.
- **Depth.** How much unlisted investigation and work an agent does inside one step instead of handing it back to the user. A step can meet its done condition and still be shallow. Stronger coverage bars and anchors raise it.
- **Lookahead.** The later steps an agent can see while it works the current one. Their visibility pulls attention forward; hiding them behind a real context boundary removes the pull. Ordinary planning ahead is not the problem.
- **Early exit.** Ending a step before the result is complete because lookahead pulled attention forward and the done condition was too vague to hold it. It needs an ordered sequence and is not the same as low depth.
- **Backfire.** A prohibition that names the unwanted pattern and makes it more salient. The decision is written down, which separates it from a silent default. The remedy is to state the wanted behavior, except for a hard safeguard, which keeps its prohibition next to the positive target.
- **Silent default.** A decision the document never states, so the model's defaults fill it and scope or authority drifts. Nothing was written, which separates it from backfire; it concerns what is excluded or routed, which separates it from a done condition.

## Maintenance

- **One home.** Each meaning has exactly one definitive location. Co-location may put complementary pieces together under it; a second home for the same meaning is duplication.
- **Duplication.** The same instruction or meaning stated in more than one place. It costs maintenance, context, and unintended emphasis. Repeating an anchor repeats a term, not a rule.
- **Live.** A line that still bears on the document's task and still matches the environment. A live line can still be inert. Liveness is lost through irrelevance or staleness.
- **Stale buildup.** Old material kept because deleting felt riskier than adding. Its cause is retention of the obsolete, which separates it from duplication and overlength even though it feeds length.
- **Inert.** A line that does not change what the target model would do without it. A live line can be inert, and an anchor is inert until tested. The verdict depends on the model; when the default is contested, settle it by blind comparison rather than argument.
