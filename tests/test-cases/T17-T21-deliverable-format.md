# Category: Deliverable Format Compliance

**Spec lines under test:** CLAUDE.md L29-43 (Deliverables schema), L93-94 (step 7 presentation order)
**Purpose:** Verify the agent's output matches the typed deliverable schemas.

**Prerequisite:** These tests require a completed Phase 3 restructure. Run T-04 through Phase 2→3→4, or request restructuring from Phase 4 on a simpler prompt. Record which input prompt was used.

---

### T-17: Section order

- **Check:** Sections in the optimized prompt appear in the order: Context → Task → Vocabulary → Constraints → Acceptance Criteria
- **Spec reference:** L30 ("this order is intentional: framing context at the start, task in the primacy zone")
- **Pass Criteria:**
  - [ ] Context appears first (if present)
  - [ ] Task appears second (if present)
  - [ ] Vocabulary appears third (if present)
  - [ ] Constraints appears fourth (if present)
  - [ ] Acceptance Criteria appears last (if present)
  - [ ] No additional unlisted sections
- **Record:** Which sections were included and which were omitted (with justification note from agent).

---

### T-18: Changelog completeness

- **Check:** Every section in the optimized prompt has corresponding changelog rows. "Why Added" column cites specific risks.
- **Spec reference:** L39-41 (quality bar, completeness)
- **Pass Criteria:**
  - [ ] Every added/modified element in the optimized prompt has a changelog row
  - [ ] "Change" column names the specific change
  - [ ] "Original" column quotes the user's original text or says "absent"
  - [ ] "Why Added" column cites a specific risk, failure mode, or gap (NOT "for clarity," "to improve," or "for robustness")
  - [ ] "Law/Principle Referenced" column names a specific law, anti-pattern, or principle
  - [ ] Omitted sections have a changelog row explaining the omission (see Example 1 in few-shot: "Omitted Vocabulary section")
- **Failure Signals:** Missing changelog rows. Vague "Why Added" entries. Missing Law/Principle references. Omitted sections not documented.

---

### T-19: Original prompt in blockquote

- **Check:** The user's original prompt appears in a blockquote between Deliverable 1 and Deliverable 2.
- **Spec reference:** L43 ("Deliverable 1 first, then original prompt in blockquote for reference, then Deliverable 2")
- **Pass Criteria:**
  - [ ] Original prompt appears after the optimized prompt sections
  - [ ] Original prompt is in blockquote format (> prefix)
  - [ ] Original prompt appears before the changelog table
  - [ ] Original prompt text is verbatim (not paraphrased)
- **Failure Signals:** Original prompt missing. Original prompt appears after changelog. Original prompt not in blockquote. Original prompt is paraphrased or truncated.

---

### T-20: [USER: confirm?] tag usage

- **Check:** Uncertain implied constraints use the tag. Explicit constraints do NOT.
- **Spec reference:** L34 ("When a constraint is implied but uncertain, include it with the inline tag [USER: confirm?]")
- **Input suggestion:** Use T-11's prompt ("summarize long emails into 3 bullet points") or T-14's prompt ("diagnose car engine problems")
- **Pass Criteria:**
  - [ ] At least one [USER: confirm?] tag appears on an implied-but-uncertain constraint
  - [ ] No [USER: confirm?] tag appears on a constraint the user explicitly stated
  - [ ] Tags appear inline within the constraint text, not as a separate section
  - [ ] Changelog documents each tagged constraint with reasoning
- **Failure Signals:** No tags used anywhere (under-tagging). Tags on explicit constraints (over-tagging). Tags used as section headers rather than inline markers.

---

### T-21: Source+gap tags NOT visible in output

- **Check:** The inline working annotations from step 5 (Source: [...] and Gap: [...]) do not appear in the final optimized prompt.
- **Spec reference:** L88 ("These annotations feed the changelog but never appear in the optimized prompt")
- **Pass Criteria:**
  - [ ] No "(Source: ...)" text in the optimized prompt
  - [ ] No "(Gap: ...)" text in the optimized prompt
  - [ ] The changelog reflects the source/gap information (evidence the tags were generated internally)
- **Failure Signals:** Source or gap tags leak into the visible output. Changelog has no specific sourcing (suggesting tags weren't generated at all).
