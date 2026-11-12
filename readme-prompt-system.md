# Prompt System

**50 models prompts, 6 files, one grammar.** A copy-paste library for ChatGPT, Claude, Gemini, or any local model — plus a zero-dependency website to browse, search, and copy from.

No install. No API key. No build step. Open `index.html` and it works.

```text
prompt-system/
├── index.html              ← the website (self-contained, works offline)
├── README.md
├── LICENSE
└── prompts/
    ├── 00_SPEC.md          ← conventions, chaining, eval rules
    ├── 10_core.md          ← 8   compile · route · clarify · spec · judge · decide · negotiate
    ├── 20_research.md      ← 9   answer · plan · brief · audit · landscape · check · monitor · learn · compare
    ├── 30_build.md         ← 10  app · architecture · debug · review · tests · api · schema · sql · automate · landing
    ├── 40_content.md       ← 10  longform · edit · summarize · translate · email · deck · repurpose · voice · positioning · naming
    ├── 50_data.md          ← 7   extract · classify · table · meeting · q&a · themes · clean
    └── 60_agents.md        ← 6   loop · orchestrator · critic · eval · memory · safety gate
```

---

## Quick start (pick one)

**1. Browse the site**
Open `index.html` in any browser. Search with `/`, filter by file, click **copy**, paste into your AI. Works offline, from a USB stick, or on GitHub Pages.

**2. Use a file directly**
Open any `prompts/*.md`, copy the block under `## Title` into your model's **system prompt** or first message, replace `{{variables}}`, send.

**3. Deploy on GitHub Pages**
Push the repo → **Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)`** → save. Your site is live at `https://<user>.github.io/<repo>/`.

---

## Why this is a system, not a list

| Law | Meaning |
|---|---|
| **One prompt = one job** | Two audiences or two output formats means two prompts. Chains beat mega-prompts. |
| **Human title, machine body** | Title and `When:` are for scanning. Everything after `ROLE:` is for the model. |
| **Contract before style** | Declare the output shape first; the model stops wandering. |
| **Every rule is checkable** | "Be concise" fails. "Max 5 bullets" passes. |
| **Uncertainty is an output** | Every prompt says what to do when it doesn't know: refuse, label confidence, or name the gap. |

---

## Anatomy of every prompt

```text
ROLE:        who is speaking
MISSION:     what success is, with {{variables}}
PROCEDURE:   numbered steps, in order
RULES:       prohibitions — what not to do
OUTPUT:      exact sections, order, length caps
SELF-CHECK:  the verification question (only where determinism matters)
```

Same order, every file. That's what lets you swap modes and chain them.

---

## Chains

A chain is a prompt with a job, not a conversation. The output schema of one mode is a valid input for the next.

| Chain | Path | Why |
|---|---|---|
| **Intake** | `task-router` → `clarifier` → target | stops wasted work on vague asks |
| **Grounded research** | `query-planner` → `grounded-answer` → `source-audit` | breadth, then citations, then trust |
| **Decision** | `deep-brief` → `compare` → `critic` | investigate, choose, attack the choice |
| **Ship** | `spec-writer` → `architecture` → `app` → `review` → `tests` | no code before a testable spec |
| **Publish** | `longform` → `edit` → `repurpose` → `voice` | draft, tighten, distribute, stay consistent |
| **Data** | `clean` → `extract` → `classify` → `themes` | raw records → structure → meaning |
| **Quality** | any mode → `critic` → `output-judge` | find breakage, then score against a rubric |

**Handoff rule:** if the upstream output isn't a legal input for the next prompt, you need an adapter step — not a longer prompt.

---

## Using it with your AI

**System prompt (recommended)**
Paste the full prompt from the `.md` file into the system/instructions field. Keep `{{variables}}` as the only thing you change per run.

**Chat message (fast)**
Paste the prompt, then add your inputs below it in the same message. Works everywhere, including models without a system field.

**Variables**
Replace every `{{snake_case}}` before sending. An unfilled variable is the #1 cause of a bad answer — models invent a plausible value and run with it.

**Temperature**

| Task | Temp |
|---|---|
| Extraction, classification, SQL, tests | 0 – 0.2 |
| Research synthesis, review, debugging | 0.2 – 0.4 |
| Writing, naming, ideation | 0.7 – 0.95 |
| Critic / red team | 0.4 – 0.6 |
| Judge / eval | 0 |

---

## Evaluating a prompt

1. **Golden set** — 10 real cases: 4 typical, 3 hard, 2 adversarial, 1 out-of-scope.
2. **Expected property, not exact string** — "cites ≥3 real sources" beats a memorized answer.
3. **Declare thresholds before scoring.** Otherwise every score drifts toward "looks fine."
4. **Name the failure patterns** (*invented citation*, *merged records*, *hedged non-answer*). You fix patterns, not individual failures.
5. **Ship only if the golden set holds.** Keep the previous version for one cycle.

---

## Contributing

A new mode is accepted if it:

- covers a job the library cannot already do (paste the failure it fixes),
- follows the six-block anatomy exactly,
- has a checkable `RULES` section and a length-capped `OUTPUT`,
- includes one worked example in the PR description.

Formatting is enforced by hand and by review — no build tooling required.

---

## License

MIT. Use it commercially, fork it, ship it inside your product. Attribution appreciated, not required.

---

*Built to be copied. If a prompt here saves you a bad afternoon, it's doing its job.*