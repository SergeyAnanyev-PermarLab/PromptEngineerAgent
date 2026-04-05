# Category: Complexity Classification (Phase 1 Triage)

**Spec lines under test:** CLAUDE.md L55-72
**Governing rules:** Bypass thresholds (L58-64), tiebreaker (L66), edge cases (L68-72)
**Anti-patterns tested:** AP1 (Over-questioning), AP3 (Restructure When Unnecessary)

---

### T-01: Low complexity — trivial task

- **Input:** `Fix the typo in line 3`
- **Expected Phase:** Phase 4 (bypass)
- **Expected Behavior:** One-sentence classification stating "low complexity" → brief assessment → "Want me to restructure this, or is it ready to use as-is?"
- **Pass Criteria:**
  - [ ] Classification states low complexity
  - [ ] No clarifying questions asked
  - [ ] No restructure generated unless user requests it
  - [ ] Phase 4 dialogue matches L97-99
- **Failure Signals:** Agent asks clarifying questions (AP1). Agent restructures without asking (AP3). Agent classifies as medium or high.

---

### T-02: Low complexity — simple script request

- **Input:** `Write a Python script to parse JSON and extract emails`
- **Expected Phase:** Phase 4 (bypass)
- **Expected Behavior:** Low or Medium classification (tiebreaker favors Low per L66). Brief assessment. Phase 4 bypass.
- **Pass Criteria:**
  - [ ] Classified as Low or Medium (not High)
  - [ ] If Medium: bypass triggered (Context implicit + Task present + Constraint absent = borderline — tiebreaker should push to Low)
  - [ ] No Phase 2 entry
- **Failure Signals:** Agent enters Phase 2. Agent asks more than 0 questions.
- **Note:** This tests the tiebreaker at L66. If classified as Medium, that's acceptable — but it should still bypass to Phase 4 since Task + implicit Context satisfies Medium threshold.

---

### T-03: Medium complexity — detailed but clear

- **Input:** `I have a PostgreSQL database with customer records. Write a query that finds customers who haven't ordered in 90 days and generates a re-engagement email list with their name, last order date, and total spend.`
- **Expected Phase:** Phase 4 (bypass)
- **Expected Behavior:** Medium classification. Context (PostgreSQL, customer records) + Task (query) + Constraints (implicit: 90-day window, specific columns) satisfy Medium threshold.
- **Pass Criteria:**
  - [ ] Classified as Medium
  - [ ] Bypass to Phase 4
  - [ ] Brief assessment notes what's strong (context is clear, task is specific)
  - [ ] Optional improvements mentioned without forcing restructure
- **Failure Signals:** Agent enters Phase 2. Agent classifies as High.

---

### T-04: High complexity — system design

- **Input:** `Design a microservices architecture for our payment processing system that handles PCI compliance, supports 3 payment providers, and needs to process 10K transactions per second`
- **Expected Phase:** Phase 2 (Clarify)
- **Expected Behavior:** High classification. Proceed to Phase 2. Ask up to 3 clarifying questions. End with exact sentinel.
- **Pass Criteria:**
  - [ ] Classified as High
  - [ ] Enters Phase 2
  - [ ] Asks 1-3 clarifying questions (not more)
  - [ ] Response ends with exact string: "Waiting for your answers before I restructure."
  - [ ] No restructured content in this response
- **Failure Signals:** Agent bypasses to Phase 4 (threshold override not applied). Agent asks 4+ questions (AP1). Bleed-through (AP7).

---

### T-05: Borderline — tiebreaker test

- **Input:** `Refactor the auth module to use JWT tokens`
- **Expected Phase:** Phase 4 (bypass via tiebreaker)
- **Expected Behavior:** Could be Medium (refactor, feature) or High (architecture change). Tiebreaker at L66 should classify at the lower level (Medium). Medium threshold: Context (auth module) + Task (refactor to JWT) — needs 1 more for bypass. "JWT tokens" implies Constraint (use JWT specifically). Should bypass.
- **Pass Criteria:**
  - [ ] Classified as Medium (not High)
  - [ ] Bypass to Phase 4
  - [ ] If classified as High: tiebreaker failed — record as deviation
- **Failure Signals:** Agent classifies as High and enters Phase 2. This means the tiebreaker isn't firing.
- **Calibration data:** Record the classification and reasoning. This is a key data point for tiebreaker tuning.

---

### T-06: Vague but low — tests over-classification

- **Input:** `Write a prompt that helps me brainstorm`
- **Expected Phase:** Phase 4 (bypass)
- **Expected Behavior:** Low classification. Task is present ("write a prompt that helps me brainstorm"). Vagueness alone should not trigger Phase 2 — the task is clear even if underspecified.
- **Pass Criteria:**
  - [ ] Classified as Low
  - [ ] Bypass to Phase 4
  - [ ] Brief assessment acknowledges vagueness as an optional improvement area
  - [ ] Does NOT enter Phase 2 to resolve vagueness
- **Failure Signals:** Agent enters Phase 2 because the prompt is "too vague." This conflates vagueness with complexity — the prompt is vague but not complex.
- **Note:** If the agent consistently over-classifies vague prompts, the bypass threshold definitions may need examples distinguishing "vague" from "complex."
