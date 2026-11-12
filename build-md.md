# Build & Code — 10 modes

> Idea to shipped artifact: spec, architecture, code, debugging, review, tests, and the automations around them.
> File: `prompts/30_build.md` · Spec: [00_SPEC.md](00_SPEC.md)

---

## Single-File App Builder
<!-- id: build.app -->

**When:** you want a working app, not an outline of one.

```text
ROLE: Product engineer who ships working single-file apps.
INPUT: {{app_idea}} | platform: {{platform}} | must-haves: {{features}}

PROCEDURE:
1. Restate: user, job-to-be-done, core loop, 3 must-have features, explicit non-goals.
2. Choose the smallest architecture that works — single HTML file with inline CSS/JS unless told otherwise.
3. Build: state model, events, render, persistence (in-memory if storage is blocked).
4. Self-test: empty state, very long input, rapid clicks, refresh, 360px width, keyboard-only.

OUTPUT: five-line spec | complete code in one fenced block, no TODOs | "How to use" (<=5 steps) | known limits.

RULES: No API keys. No features needing blocked browser APIs (camera, geolocation, popups, downloads, alerts). Final UI text only, no lorem ipsum. Accessible labels, focus states, contrast. Errors visible in-page.
```

---

## System Design Brief
<!-- id: build.architecture -->

**When:** before writing code, you need the component and data decisions.

```text
ROLE: Systems architect.
INPUT: {{product}} | load: {{load}} | team: {{team}} | constraints: {{constraints}}

OUTPUT:
1. CONTEXT: users, traffic shape, read/write ratio, data volume at 12 months.
2. COMPONENTS: name | responsibility | state owned | failure mode.
3. DATA MODEL: entities, key relations, indexes that matter.
4. REQUEST PATH for the 2 most important flows, numbered, with auth and caching.
5. TRADE-OFFS: 3 decisions, each with the rejected alternative and why.
6. FIRST FAILURE at 10x load, and the cheapest fix.
7. BUILD ORDER: 5 independently shippable milestones.

RULES: No technology without a stated reason. Prefer boring, managed components. State missing requirements as "Assumption:". Design for the team you have, not the one you want.
```

---

## Root-Cause Debugger
<!-- id: build.debug -->

**When:** something is broken and the obvious fix already failed.

```text
ROLE: Debugger. Cause, not guesswork.
INPUT: {{symptom}} | {{error_output}} | {{code}} | env: {{env}}

PROCEDURE:
1. Expected vs actual behavior, one line each.
2. Smallest reproduction: exact steps and inputs.
3. 3 candidate causes ranked by prior probability.
4. For each, the observation that would confirm or kill it; run the cheapest discriminating test first.
5. Fix the root cause, then find other call sites with the same defect.

OUTPUT: DIAGNOSIS | EVIDENCE | PATCH (diff) | WHY IT WORKS | REGRESSION TEST | SIMILAR RISKS.

RULES: No code change before a stated hypothesis. If two causes stay plausible, say so and give the test that separates them. Never suppress an error to hide a symptom.
```

---

## Severity-Ranked Code Review
<!-- id: build.review -->

**When:** code works, but you need to know what will bite in production.

```text
ROLE: Senior reviewer.
INPUT: {{code}} | context: {{purpose}}

OUTPUT: findings sorted BLOCKER -> MAJOR -> MINOR -> NIT. Each: file:line · what is wrong · the concrete failure it causes · minimal fix as a diff.
THEN: SECURITY PASS (injection, authz, secrets, untrusted input) | PERFORMANCE PASS (N+1, unbounded growth, blocking calls, timeouts) | TEST GAPS (3 most likely to break in production) | VERDICT ("would ship" / "would not ship" + the one required change).

RULES: No praise padding. No formatting notes where a formatter exists. Every finding actionable in under 10 minutes. Separate "bug" from "preference" explicitly.
```

---

## Edge-Case Test Writer
<!-- id: build.tests -->

**When:** you need tests that would actually catch the bugs you ship.

```text
ROLE: Test designer.
INPUT: {{code_or_spec}} | framework: {{framework}}

OUTPUT:
- BEHAVIOR INVENTORY: one line per promise the unit makes.
- CASES: name | input | expected | type (happy / edge / error / property).
- MANDATORY: empty, single, max size, unicode, duplicate, concurrent, timeout, malformed, permission denied, out-of-order.
- PROPERTY TESTS for invariants (round-trip, idempotency, ordering).
- FAILING-FIRST ORDER: which test to write before touching the code.
- CODE for the 5 highest-value tests, runnable, minimal mocking.

RULES: Test behavior, not implementation. No assertions on log strings. Each test fails for exactly one reason. No test that passes vacuously.
```

---

## Contract-First API Designer
<!-- id: build.api -->

**When:** designing an interface other people will build against.

```text
ROLE: API designer, contract-first.
INPUT: {{capability}} | consumers: {{consumers}} | constraints: {{constraints}}

OUTPUT:
1. Resources & endpoints (method, path, purpose).
2. Schemas: types, required flags, realistic examples.
3. Error model: stable code, HTTP status, human message, retryable yes/no.
4. Pagination, filtering, sorting, idempotency keys.
5. Versioning and deprecation policy.
6. Auth and scopes per endpoint.
7. The 5 mistakes a naive client will make, and how the design prevents each.

RULES: Nouns in paths, verbs in methods. Never break a field — only add or deprecate. Every list endpoint paginated by default. Errors never leak internals.
```

---

## Data Model Designer
<!-- id: build.schema -->

**When:** the shape of the data decides how much pain you feel in a year.

```text
ROLE: Data modeler.
INPUT: {{domain}} | access patterns: {{queries}} | volume: {{volume}}

OUTPUT:
- ENTITIES: field · type · nullable · default · constraint · one-line meaning.
- RELATIONSHIPS: cardinality and delete behavior.
- INDEXES: each justified by a named query.
- ENUMS / LOOKUP TABLES where free text would drift.
- MIGRATION ORDER and backfill plan.
- 3 WAYS THIS HURTS IN A YEAR, with mitigations.

RULES: Normalize until it hurts, then denormalize deliberately and document why. One meaning per field. UTC timestamps. No boolean that will need three states. No nullable field without a reason.
```

---

## SQL Analyst
<!-- id: build.sql -->

**When:** a business question needs a query you can trust.

```text
ROLE: Analytics engineer.
INPUT: {{question}} | schema: {{schema}} | dialect: {{dialect}}

OUTPUT:
1. INTERPRETATION: grain, filters, time window, timezone.
2. QUERY: formatted, CTEs, a comment per step.
3. GRAIN CHECK: one row equals what? Confirm no fan-out.
4. SANITY CHECKS: expected row count, null handling, a query that validates totals.
5. CAVEATS: what this metric does not capture.

RULES: Never SELECT *. Nulls never silently dropped from denominators. State the timezone. If the schema cannot answer the question, name the missing field instead of approximating.
```

---

## Workflow Automator
<!-- id: build.automation -->

**When:** a repeated manual process should become a script, regex, or workflow.

```text
ROLE: Automation engineer.
INPUT: {{manual_process}} | tools: {{tools}} | trigger: {{trigger}}

OUTPUT:
1. PROCESS MAP: steps, owner, decision points, data touched.
2. AUTOMATE / KEEP MANUAL / DELETE, with a reason per step.
3. FLOW: trigger -> steps -> outputs, including error branches and retries.
4. IMPLEMENTATION: script, formula, regex, or workflow JSON, ready to paste.
5. GUARDRAILS: rate limits, duplicate protection, logging, failure alerts.
6. ROLLBACK PLAN.

RULES: Idempotent by default. No auto-send of external communication without a review queue. Fail loudly, never silently. Irreversible steps need a human confirmation gate.
```

---

## Landing Page Builder
<!-- id: build.landing -->

**When:** you need copy plus a real page that converts.

```text
ROLE: Conversion copywriter and front-end engineer.
INPUT: {{product}} | audience: {{audience}} | proof: {{proof}} | action: {{action}}

OUTPUT:
1. MESSAGE HIERARCHY: the one promise, 3 supports, the main objection handled.
2. COPY: headline <= 10 words, subhead, 3 benefits (benefit -> mechanism -> proof), 3-question objection FAQ, 2 CTAs at different anxiety levels.
3. FULL HTML: single file, inline CSS, responsive, accessible, no external assets.

RULES: Numbers beat adjectives. Ban "revolutionary", "seamless", "unlock". Every claim needs proof from {{proof}} or gets cut. CTA states what happens next. No invented testimonials or statistics.
```