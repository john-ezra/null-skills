How a skill in this repo is packaged and reached. Checked against the skills under `Skills/` and `Workflows/` and the harness documentation at `omp://skills.md` and `omp://context-files.md`; where those and this file disagree, they win and this file gets corrected.

## Where a skill lives

A skill is `Skills/<name>/SKILL.md` with any sibling files in the same directory. A document that sequences several skills or drives documents through Linear is a workflow and lives at `Workflows/<name>/SKILL.md` with the same frontmatter. The harness discovers skills one directory level under a root and never recurses, so a skill nested inside another skill's directory is not found.

## Frontmatter

Four fields are in use across the repo. Add no others.

- **`name`.** Matches the directory name.
- **`description`.** One or two sentences, written by the rules below.
- **`disable-model-invocation`.** Present on every skill, directly after `description`, as an explicit `true` or `false`, so listed or manual is a one-word toggle. `true` is the harness's `hide` switch, which drops the skill from the model's list but keeps it loaded. The only field that changes how a skill is reached.
- **`allowed-tools`.** Present on `linear` and `lifecycle` as a `Bash(<cmd>:*)` list. The harness documents no behavior for it: a declaration for other hosts, never a mechanism a skill body can lean on.

## How a skill is reached

- **Listed skill.** Every session's system prompt, subagent sessions included, carries one line per listed skill: `- <name>: <description>`. That line is all the model sees until it reads `skill://<name>`.
- **Manual skill.** A person runs `/skill:<name>`, which exists only while the harness setting `skills.enableSkillCommands` is on and which injects the body with the skill directory attached so relative links resolve.
- **Sibling files.** The house form is a relative markdown link from the body, `[GLOSSARY.md](GLOSSARY.md)`, resolved against the skill directory; from elsewhere, `skill://<name>/<FILE>.md`. Nothing loads a sibling automatically: it is reached by its pointer or not at all.
- **Context files.** These load into the opening project context on every session and pay standing cost. `~/.omp/agent/AGENTS.md` outranks every other. A native project file is read only from the nearest non-empty `.omp/` directory on the walk from the working directory to the repository root; a standalone `AGENTS.md` loads at every depth of that walk, farther ancestors first. `AGENTS.md` files below the working directory do not load; the agent gets their paths and an instruction to read them before editing there. `omp://context-files.md` holds the provider table and settles any case where a file's location or format decides whether it loads. Inside a context file, `@path` inlines the target before injection, so it is not a pointer; a prose "read X when Y" is.
- **`RULES.md`.** Sticky at two native locations only: the user agent directory and that same nearest non-empty project `.omp/`. It loads as an always-apply rule re-attached near the current turn, and a user copy shadows a project copy rather than joining it. It holds a few hard requirements and nothing else.

## Choosing listed or manual

Listed when the moment to use the skill is recognizable from an ordinary request without the user knowing the skill exists, or when other skills must route to it on their own. Manual when the moment is a human decision (`handoff` at the end of a session), when the user wants the choice kept in their hands, or when the skill would otherwise fire on requests it should not.

A description that claims permanent applicability ("always in force") makes the model read the body on every task; use that shape only for rules that apply to every turn.

## Writing the description

For a listed skill:

- **Name the job first.** The opening clause says what the skill does in the words a user's request would contain.
- **One trigger per path.** After the job, "Use when ..." lists the cases the body handles, one trigger each, synonyms collapsed into the trigger they share.
- **Promise exactly the body.** Every path the body handles on its own appears; nothing the body lacks is advertised.
- **No identity or explanation.** The body supplies both once opened; the description carries only what triggers.
- **"Not for" only against a real neighbour.** Add an exclusion when a neighbouring skill would otherwise catch the same request, in the neighbour's terms.

For a manual skill, one sentence naming the job; nothing matches on it, so a "Use when" clause is dead weight.

## When a skill earns separation

Split only when the halves have triggers users state differently, or when the later half must be hidden from the earlier one behind a real context boundary. File size and tidy organization are reasons for a sibling file behind a pointer, never for a skill. Merge test: two descriptions that would fire on the same requests are one skill.

## Shared material

Support that several skills use lives in one plain file with no frontmatter. When every consumer is a listed skill, the file sits inside the skill that owns the meaning, linked relatively from there and reached from the others by `skill://<owner>/<FILE>.md` behind a reach condition. When any consumer is a manual skill, the file sits outside the skill system at a path every consumer can name, because a manual skill's directory is reached only through the person who invoked it. Never create a third skill to hold shared text: listed, it pays standing cost for a file; manual, only a person can reach it.

## Menu skill

A manual skill whose body is a list: each entry names a manual skill as `/skill:<name>` and gives the one condition a person would recognize as the moment to run it. The person invokes the entry; the body never tells the agent to.

## Bar

Every packaging decision on the skill has a verdict: its mode, each description rule, whether a split was earned, where each piece of shared material lives, whether any menu entry is written as if it could dispatch, and whether the frontmatter holds only the four fields above.
