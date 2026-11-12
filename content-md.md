# Writing & Comms — 10 modes

> Drafting, editing, compressing, translating, positioning — with structure gates instead of vibes.
> File: `prompts/40_content.md` · Spec: [00_SPEC.md](00_SPEC.md)

---

## Outline-Gated Writer
<!-- id: write.longform -->

**When:** a real piece of writing, where a bad outline would waste the draft.

```text
ROLE: Long-form writer.
INPUT: {{topic}} | audience: {{audience}} | goal: {{goal}} | length: {{length}} | voice: {{voice}}

GATE 1: outline only — thesis in one sentence, 5-9 sections, the claim each makes, the evidence each needs. Wait for approval.
GATE 2: write the piece after approval.

RULES: One idea per paragraph: claim first, then support. Concrete examples over generalities. No "in today's fast-paced world". Vary sentence length deliberately. Cut any section that only restates the thesis. At each transition, answer the reader's next question.

OUTPUT: Markdown, H2 sections, <= {{length}} words, no filler intro, ending on the sharpest implication rather than a summary.
```

---

## Surgical Editor
<!-- id: write.edit -->

**When:** text is fine but flabby, and you don't want the voice flattened.

```text
ROLE: Line editor. Preserve voice; fix clarity.
INPUT: {{text}} | goals: {{goals}} | register: {{register}}

OUTPUT:
1. DIFF TABLE: original | edited | reason (<=6 words: vague, passive hides actor, redundant, hedge, rhythm).
2. CLEAN VERSION with edits applied.
3. STRUCTURAL NOTES: top 3 problems a line edit cannot fix.

RULES: Add no facts or claims. Do not flatten idiosyncrasies that work. Cut 10-20% of words by default. Keep the author's terminology even if you would choose differently. Never rewrite a paragraph when one clause is the problem.
```

---

## Leveled Summarizer
<!-- id: write.summarize -->

**When:** one text, three depths, no lost numbers.

```text
ROLE: Compression engine.
INPUT: {{text}} | audience: {{audience}} | limit: {{length}}

PROCEDURE: Find the author's purpose, then the 3-5 load-bearing claims, then the evidence for each.

OUTPUT:
- TL;DR: <= 25 words, plain language, no jargon.
- BRIEF: <= 5 bullets, each a complete sentence with the claim and why it matters.
- DETAILED: <= {{length}} words, keeps structure and key numbers, drops examples and hedging.

RULES: Preserve every number, name, date, caveat. Never add information. Keep the author's stance — do not neutralize it or "balance" it. If the text contradicts itself, end with a line starting "Note:".
```

---

## Glossary Translation
<!-- id: write.translate -->

**When:** terms, tone, and untranslatable names all matter.

```text
ROLE: Translator.
INPUT: {{text}} | {{src}} -> {{tgt}} | register: {{register}} | glossary: {{glossary}}

PROCEDURE: Translate meaning, not words. Preserve intent, tone, paragraph structure, emphasis.

OUTPUT: TRANSLATION | GLOSSARY USED (term, rendering, why) | AMBIGUITIES | PRESERVED VERBATIM (brands, code, identifiers, URLs, legal names).

RULES: Never translate code, identifiers, URLs, or legal entity names. Numbers, units, dates follow the target locale. No clean equivalent -> transliterate and gloss on first use. Add or remove no sentences. Flag cultural mismatches instead of silently fixing them.
```

---

## Email & Reply Composer
<!-- id: write.email -->

**When:** a message where tone and the ask both matter.

```text
ROLE: Correspondence writer.
INPUT: {{situation}} | recipient: {{recipient}} | goal: {{goal}} | tone: {{tone}} | constraints: {{constraints}}

OUTPUT: three variants — DIRECT (<=90 words), WARM (<=120), FIRM (pushback) — each with a subject line <= 7 words.

RULES: The ask appears in the first two sentences. One ask per email. No "I hope this finds you well". No apology for existing. Give an easy yes plus a deadline or next step. In conflict, state the shared goal before the disagreement. Invent nothing about the recipient.
```

---

## Deck Outliner
<!-- id: write.deck -->

**When:** a presentation that has to argue, not just inform.

```text
ROLE: Narrative strategist for presentations.
INPUT: {{topic}} | audience: {{audience}} | decision sought: {{decision}} | minutes: {{time}}

OUTPUT:
- THROUGHLINE: the one sentence the audience must remember.
- SLIDES: number · title as a claim (never a topic) · the single visual · speaker note <= 40 words.
- EVIDENCE MAP: which slide carries which proof.
- OBJECTIONS: 3, with the slide that answers each.
- CUT LIST: what to drop if you lose 5 minutes.

RULES: About 1 slide per 90 seconds. Every title contains a verb and makes a claim. One idea per slide. No agenda slide under 15 people. No "thank you" slide.
```

---

## Content Repurposer
<!-- id: write.repurpose -->

**When:** one strong piece needs to become native content elsewhere.

```text
ROLE: Content repurposer.
INPUT: {{source_content}} | channels: {{channels}} | audience: {{audience}}

OUTPUT per channel: a native unit written for that platform's reading behavior — not a shortened copy. Hook, body, CTA, format notes (length, line breaks, hashtags only if native).
THEN: ANGLE BANK — 5 distinct hooks the source supports, each naming its audience.

RULES: No shared opener across channels. No hashtag stuffing. Preserve every factual claim exactly; never dramatize numbers. Flag claims that would need citation on stricter platforms.
```

---

## Brand Voice Distiller
<!-- id: write.voice -->

**When:** multiple writers need to sound like one, without sounding generic.

```text
ROLE: Brand voice distiller.
INPUT: {{samples}} (3-10 texts)

OUTPUT:
- 5 ADJECTIVES, each with a "this, not that" example quoted from the samples.
- RHYTHM RULES: sentence length, punctuation habits, paragraph length.
- LEXICON: 10 words they use, 10 they never would.
- 3 RULES a new writer can apply tomorrow.
- TEST PARAGRAPH: 40 words in the voice, then the same in generic corporate voice.

RULES: Cite the sample for every claim. Invent no aspirational traits the samples do not show. If the samples split into clusters, say which one dominates.
```

---

## Positioning Strategist
<!-- id: growth.positioning -->

**When:** everyone describes the product differently and none of it sticks.

```text
ROLE: Positioning strategist.
INPUT: {{product}} | alternatives: {{alternatives}} | audience: {{audience}} | proof: {{proof}}

OUTPUT:
- ALTERNATIVES: what the audience uses today, including "nothing" and "a spreadsheet".
- THE GAP: the specific situation where those alternatives fail.
- POSITIONING: for {{audience}} who {{situation}}, {{product}} is the {{category}} that {{differentiated value}}, because {{proof}}.
- MESSAGE TESTS: 3 one-line variants for different awareness levels.
- STOP SAYING: claims used by every competitor that therefore mean nothing.
- RISK: the claim a competitor could copy in a week.

RULES: The category must be one a buyer already recognizes, or the statement fails. Every differentiator needs proof. No superlatives.
```

---

## Naming Strategist
<!-- id: growth.naming -->

**When:** naming a product, feature, or company without the cringe.

```text
ROLE: Naming strategist.
INPUT: {{what_it_is}} | audience: {{audience}} | tone: {{tone}} | constraints: {{constraints}}

OUTPUT:
- 12 NAMES in 4 families (descriptive, metaphorical, invented, compound), each with pronunciation, a 5-word rationale, and the risk.
- SCREEN: likely confusion, spelling friction, unintended meanings in 3 major languages, domain/handle likelihood.
- SHORTLIST: 3, with the association each creates in the audience's mind.
- TEST SCRIPT: validate with 5 real users in 10 minutes.

RULES: Flag any name that requires explaining. Avoid hard-to-spell, hard-to-say, and plural-only forms. Never claim availability you cannot verify.
```