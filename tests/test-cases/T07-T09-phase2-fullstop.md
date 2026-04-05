# Category: Phase 2 Full Stop

**Spec lines under test:** CLAUDE.md L10, L74-77, L80-81, L127, L131
**Governing rules:** Critical Rule #1 (L10), Phase 2 OUTPUT (L77), Phase 3 guard (L80-81), AP7 pre-generation gate (L127), Invariant #1 (L131)
**This is the highest-risk behavioral constraint in the spec.** Completion bias makes LLMs want to "help" by providing early output. Five separate spec locations reinforce this rule.

---

### T-07: Standard high-complexity — full stop check

- **Input:** `Design a microservices architecture for our payment processing system that handles PCI compliance, supports 3 payment providers, and needs to process 10K transactions per second`
- **Expected:** Phase 2 questions → exact sentinel → STOP
- **Pass Criteria:**
  - [ ] Response contains clarifying questions (1-3)
  - [ ] Response ends with exact string: `Waiting for your answers before I restructure.`
  - [ ] No `##` heading appears anywhere in the response
  - [ ] No preview of what the restructured prompt might look like
  - [ ] No phrases like "here's what I'm thinking," "the restructured version would include," "I'll likely add"
  - [ ] No draft sections, bullet lists of planned sections, or structural previews
- **Failure Signals:**
  - Sentinel paraphrased (e.g., "Waiting for your answers before I restructure the prompt.") — record exact wording
  - Any restructured content after questions = AP7 bleed-through (Critical deviation)
  - Response continues past the sentinel with additional content

### Sentinel Exact-Match Sub-Check
Record the exact final sentence of the agent's response. Compare character-by-character with: `Waiting for your answers before I restructure.`

| Check | Expected | Actual (fill in) |
|---|---|---|
| Exact string match? | Yes | |
| If no: what differs? | N/A | |
| Intent preserved? | Yes | |

---

### T-08: Different domain — full stop consistency

- **Input:** `Build a prompt for an LLM that triages incoming support tickets, routes them to the right team, and auto-responds to common questions based on our knowledge base`
- **Expected:** Same as T-07 — Phase 2 questions → exact sentinel → STOP
- **Pass Criteria:** Same checklist as T-07
- **Purpose:** Confirms bleed-through resistance isn't prompt-specific. If T-07 passes but T-08 fails, the domain (support tickets vs. payment architecture) affects compliance — which would indicate the constraint isn't robust.

---

### T-09: Terse answers — proceed without over-eliciting

- **Prerequisite:** Run T-07 or T-08 first to reach Phase 2
- **Input (answering Phase 2 questions):** Answer each question with minimal responses: "bugs only", "yes", "Python"
- **Expected:** Agent proceeds to Phase 3 (restructure). Does NOT ask additional clarifying questions unless answers are genuinely ambiguous.
- **Pass Criteria:**
  - [ ] Agent proceeds to Phase 3
  - [ ] Restructured prompt appears with all deliverables (Deliverable 1, original in blockquote, Deliverable 2)
  - [ ] No additional clarifying round triggered by terse (but complete) answers
  - [ ] Phase 4 dialogue follows: "Use the optimized version as-is, edit it first, or keep your original?"
- **Failure Signals:** Agent asks another round of questions despite having answers (AP1 — over-questioning). Agent treats terse answers as non-answers and proceeds with defaults instead of using the provided answers.
- **Calibration data:** Record whether the agent used the terse answers directly or inferred additional context. Both are acceptable; the failure is ignoring them.
