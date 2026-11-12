# 00 · Spec & Conventions

**v1.0 · 6 files · 50 modes**

A prompt system is not a prompt collection. The difference: every entry follows the same grammar, so entries can be swapped, chained, and evaluated. This file is the contract.

---

## 1. Five laws

1. **One prompt = one job.** Two audiences or two output formats means two prompts. Chains beat mega-prompts.
2. **Human title, machine body.** The `Title` and `When:` line are for people scanning. Everything after `ROLE:` is for the model — no warmth, no persuasion, no meta-commentary.
3. **Contract before style.** Declare the output shape before describing quality. A model that knows the schema stops wandering.
4. **Every rule must be checkable.** "Be concise" fails. "Max 5 bullets, each a complete sentence" passes. If you cannot verify it by reading the output, delete it or make it measurable.
5. **Uncertainty is an output, not a failure.** Every prompt says what to do when it doesn't know: refuse, label confidence, ask, or name the missing evidence.

---

## 2. File layout

```
prompts/
├── 00_SPEC.md          ← this file
├── 10_core.md          ← 8   compile · route · clarify · spec · normalize · judge · decide · negotiate
├── 20_research.md      ← 9   answer · plan · brief · audit · landscape · check · monitor · learn · compare
├── 30_build.md         ← 10  app · architecture · debug · review · tests · api · schema · sql · automate · landing
├── 40_content.md       ← 10  longform · edit · summarize · translate · email · deck · repurpose · voice · positioning · naming
├── 50_data.md          ← 7   extract · classify · table · meeting · q&a · themes · clean
└── 60_agents.md        ← 6   loop · orchestrate · critic · eval · memory · gate
```

**Naming**

| Thing | Pattern | Example |
|---|---|---|
| File | `NN_domain.md` | `30_build.md` |
| Prompt id | `domain.slug` | `build.review` |
| Title | Title Case, task-shaped, ≤ 4 words | `Severity-Ranked Code Review` |

The id never changes after publishing. Titles may improve; ids are the stable key used by routers, chains, and logs.

---

## 3. Record format

Every mode in every `.md` file uses exactly this shape:

```markdown
## Human-Readable Title
<!-- id: domain.slug -->

**When:** the situation that triggers this mode, one line.

```text
ROLE: ...
MISSION: ...
INPUT: ...
PROCEDURE: ...
RULES: ...
OUTPUT: ...
```
```

Four fields. If a mode needs more documentation, write it in the PR, not in the prompt.

---

## 4. The six blocks

Order matters — each block narrows the space for the next.

| # | Block | Answers | Prevents |
|---|---|---|---|
| 1 | `ROLE:` | who is speaking | generic-assistant voice |
| 2 | `MISSION:` / `INPUT:` | what success is, with `{{vars}}` | answering the wrong question |
| 3 | `PROCEDURE:` | numbered steps | skipped reasoning |
| 4 | `RULES:` | prohibitions and hard constraints | hallucination, padding, scope creep |
| 5 | `OUTPUT:` | exact sections, order, length caps | unreadable walls of text |
| 6 | `SELF-CHECK:` *(optional)* | the verification question | silent errors |

Blocks 1–5 are mandatory. `SELF-CHECK` only where determinism matters: extraction, classification, translation, SQL.

---

## 5. Placeholder grammar

- `{{snake_case}}` — one variable, one value. Never nest.
- `{{a | b | c}}` — closed enum; the model must pick one.
- `{{#list}}` — repeated block.
- Defaults belong to the caller, not the prompt: `audience = {{audience}}` (default: technical reader).

Names must be self-explanatory out of context. `{{x}}` is a bug waiting to happen; `{{staleness_months}}` is not.

**An unfilled variable is the #1 cause of a bad answer.** The model invents a plausible value and runs with it.

---

## 6. Composition

Modes are designed to be chained. A chain is a prompt with a job, not a conversation.

| Chain | Path | Why |
|---|---|---|
| **Intake** | `task-router` → `clarifier` → target | stops wasted work on vague asks |
| **Grounded research** | `query-planner` → `grounded-answer` → `source-audit` | breadth, then citations, then trust |
| **Decision** | `deep-brief` → `compare` → `critic` | investigate, choose, attack the choice |
| **Ship** | `spec-writer` → `architecture` → `app` → `review` → `tests` | no code before a testable spec |
| **Publish** | `longform` → `edit` → `repurpose` → `voice` | draft, tighten, distribute, stay consistent |
| **Data** | `clean` → `extract` → `classify` → `themes` | raw → structure → meaning |
| **Quality** | any → `critic` → `output-judge` | find breakage, then score it |

**Handoff rule:** the upstream `OUTPUT` must be a legal `INPUT` for the next mode. If it isn't, you need an adapter step — not a longer prompt.

---

## 7. Model settings

| Task class | Temperature | Why |
|---|---|---|
| Extraction, classification, SQL, tests | 0 – 0.2 | determinism over variety |
| Research synthesis, review, debugging | 0.2 – 0.4 | reasoning without drift |
| Writing, naming, ideation | 0.7 – 0.95 | range |
| Critic / red team | 0.4 – 0.6 | enough distance to disagree |
| Judge / eval | 0 | scores must be reproducible |

Long-context modes (`deep-brief`, `landscape`, `rag-answer`) degrade in the middle of the window. Put the question first, the sources last, and demand citations so you can audit what was actually read.

---

## 8. Evaluation loop

1. **Golden set** — 10 real cases per high-value mode: 4 typical, 3 hard, 2 adversarial, 1 out-of-scope.
2. **Expected property, not expected string.** "Cites ≥ 3 real sources" beats an exact-match answer.
3. **Thresholds declared before scoring**, written down. Otherwise every score drifts toward "looks fine".
4. **Failure taxonomy** — name the patterns (*invented citation*, *merged records*, *hedged non-answer*). Patterns are what you fix; single failures are noise.
5. **Regression** — a change ships only if the golden set holds. Keep the previous version for one cycle.

---

## 9. Anti-patterns

| Anti-pattern | Why it fails | Fix |
|---|---|---|
| "You are a helpful expert…" | no constraint, no contract | `ROLE` + `OUTPUT` schema |
| Stacking 20 rules | they conflict and get dropped | 5–8 rules, each testable |
| "Be creative" | unmeasurable | name the axis of variation |
| Asking for JSON in prose | fences, preambles, trailing commentary | "JSON only, no fences, no comments" |
| One mega-prompt for a workflow | silent step-skipping | chain modes |
| No refusal clause | confident fabrication | explicit uncertainty rule |
| Length stated as "short" | unpredictable output | hard cap in words or bullets |
| Prompt edited every run | no way to attribute regressions | version the id, log the version |

---

## 10. Versioning

- `id` is permanent. Bump `prompt_version` in metadata, never in the id.
- A change that alters the output schema is a **breaking** change: new id, old one deprecated.
- Log `(id, version, model, temperature)` with every output you keep. Without that, a regression is unattributable.
- Re-run the golden set when the **model** is upgraded, not when the prompt is edited.

---

## 11. Contributing a mode

Accepted only if it:

- covers a job the library cannot already do (show the failure it fixes),
- follows the six-block anatomy exactly,
- has a checkable `RULES` section and a length-capped `OUTPUT`,
- ships with one worked example in the PR.

Formatting is reviewed by hand. No tooling.