# Data & Extraction — 7 modes

> Deterministic transforms: text to JSON, records to tables, transcripts to decisions, context to answers.
> File: `prompts/50_data.md` · Spec: [00_SPEC.md](00_SPEC.md)

---

## Schema-Locked Extractor
<!-- id: data.extract -->

**When:** unstructured text must become reliable structured data.

```text
ROLE: Deterministic information extractor.
SCHEMA: {{json_schema}}
INPUT: {{raw_text}}

RULES:
- Output valid JSON only. No prose, no fences, no comments.
- Missing scalar -> null. Missing list -> []. Never infer or fill in.
- Copy values verbatim: original spelling, diacritics, casing.
- Ambiguous field -> null, and add {"field":"...","reason":"..."} to _issues.
- Dates ISO 8601. Numbers plain, no units, no separators.

OUTPUT: {"data": <object>, "_issues": []}

SELF-CHECK: every non-null value must appear in the source text.
```

---

## Taxonomy Classifier
<!-- id: data.classify -->

**When:** labelling many items against a fixed set of categories.

```text
ROLE: Classifier.
INPUT: {{items}} | taxonomy: {{taxonomy}} | rules: {{rules}}

OUTPUT per item, input order preserved:
{"id":"...","label":"...","confidence":0.0,"evidence":"<=12 words from the item","alt":null}

RULES: Only labels from the taxonomy; nothing fits -> "other" with confidence <= 0.3. Confidence reflects ambiguity, not enthusiasm; 0.9+ only when one label clearly applies. No text outside the JSON. Never reorder, merge, or drop items.

THEN: label distribution + the 5 lowest-confidence items for human review.
```

---

## Table Reconstructor
<!-- id: data.table -->

**When:** messy text (emails, PDFs, notes) needs to become a clean table.

```text
ROLE: Table reconstructor.
INPUT: {{messy_text}} | target columns: {{columns}}

OUTPUT: markdown table with exactly those columns, one row per record, plus a SOURCE MAP showing which input fragment fed each row.

RULES: Never merge two records to fill a row. Missing cell is "—", never a guess. Units in the header. If a column cannot be derived, say so once and leave it empty. Report input fragments vs output rows and explain any difference. Keep original identifiers visible for verification.
```

---

## Meeting → Decisions & Actions
<!-- id: data.meeting -->

**When:** a transcript or notes need to become accountability.

```text
ROLE: Meeting analyst.
INPUT: {{transcript_or_notes}} | attendees: {{attendees}}

OUTPUT:
- DECISIONS: what, by whom, stated reason.
- ACTIONS: owner · action · due date · dependency. Write "unassigned" / "no date" rather than inventing.
- OPEN QUESTIONS + who owns the answer.
- RISKS RAISED + who raised them.
- DISSENT: unresolved objections — never omit this section.

RULES: Only what was said. Attribute quotes where wording matters. Flag decisions that were implied rather than stated. Do not summarize discussion that produced no decision or action.
```

---

## Context-Bound Q&A
<!-- id: data.rag-answer -->

**When:** answering strictly from supplied documents, with refusal built in.

```text
ROLE: Closed-book assistant. Answer only from CONTEXT.
CONTEXT: {{context}}
QUESTION: {{question}}

RULES:
- Every sentence traceable to the context; cite [doc-id, section].
- Not present -> reply exactly "Not in the provided context." then name the missing information.
- Never use outside knowledge, even when confident.
- Internal conflict -> present both readings with citations and say they conflict.
- Never blend two documents into one unattributed claim.

OUTPUT: ANSWER (<=120 words) | CITATIONS (quote <=20 words each) | GAPS.
```

---

## Qualitative Theme Coder
<!-- id: data.themes -->

**When:** interview notes or reviews need honest themes, not invented ones.

```text
ROLE: Qualitative coder.
INPUT: {{corpus}} | research question: {{rq}}

OUTPUT:
- CODEBOOK: 5-9 themes, each with definition, inclusion/exclusion rule, 2 example quotes.
- FREQUENCY: counts with the denominator stated.
- NEGATIVE CASES: quotes that contradict each theme.
- TENSION MAP: which themes conflict.
- LIMITS: what this corpus cannot tell you.

RULES: Themes grounded in quotes, not in your expectations. Do not merge distinct meanings to reach a round number. Quotes verbatim, including errors. "3 of 40" is not "many".
```

---

## Record Normalizer & Deduper
<!-- id: data.clean -->

**When:** the same customer arrives five times in five formats.

```text
ROLE: Record normalizer and deduper.
INPUT: {{records}} | key fields: {{keys}}

OUTPUT:
1. NORMALIZATION RULES applied (case, whitespace, phone/date/currency formats, name order), as an explicit list.
2. CLEANED RECORDS as JSON.
3. DUPLICATE GROUPS: cluster id, member ids, matching evidence, confidence, merged yes/no.
4. AMBIGUOUS PAIRS left separate, with the field that would resolve each.
5. COUNTS: in, out, merged, flagged.

RULES: Never merge on a single fuzzy field. Keep the original value of any field you changed in _original. Deterministic rules only — no probabilistic guessing you cannot explain.
```