# Category: Vocabulary Section Inclusion

**Spec lines under test:** CLAUDE.md L30 (section order, Vocabulary in middle), L87 (omit if no new value), L35 (proportionality)
**Few-shot anchors:** prompt-optimization-example.md Examples 1-2 (Vocabulary omitted), Example 3 (Vocabulary included)
**Known Limitation:** CLAUDE.md Known Limitations section flags this — "Two of three few-shot examples omit Vocabulary. The model may default to omission even when domain terms warrant inclusion."
**Purpose:** Determine whether the agent ever includes the Vocabulary section and whether inclusion correlates with domain specificity.

---

### T-22: Domain-heavy — mirrors Example 3

- **Input:** `Write a prompt for reviewing clinical trial protocols for statistical design flaws`
- **Expected:** Vocabulary section INCLUDED. This directly mirrors Example 3 in the few-shot file — the agent has a positive exemplar for this exact domain.
- **Pass Criteria:**
  - [ ] Vocabulary section present in the restructured prompt
  - [ ] Vocabulary terms are domain-specific (e.g., sample size justification, multiplicity adjustment, primary endpoint)
  - [ ] Terms are not generic statistics terms that any reader would know
  - [ ] Changelog includes a row explaining why Vocabulary was included, referencing Law 1
- **Failure Signals:** Vocabulary omitted despite domain-specific terms being critical. Agent notes "standard statistical terms" when they are in fact specialized clinical biostatistics terms. If this test fails, Example 3's anchoring effect is too weak.
- **Calibration data:** Record whether Vocabulary was included. This is data point #1 for the Vocabulary inclusion rate.

---

### T-23: Domain-heavy — novel domain (no example match)

- **Input:** `Write a prompt for an LLM that reviews legal contracts for liability clauses`
- **Expected:** Vocabulary section INCLUDED. Legal terms (indemnification, limitation of liability, force majeure, severability) have precise meanings that differ from lay usage.
- **Pass Criteria:**
  - [ ] Vocabulary section present
  - [ ] Legal terms listed with their precise meanings or usage context
  - [ ] Changelog cites Law 1 (vocabulary routing) for inclusion
- **Failure Signals:** Vocabulary omitted. Agent treats legal terms as "standard" when they have domain-specific technical meanings. This failure would indicate Example 3 only works for its specific domain (clinical trials) and doesn't generalize.
- **Calibration data:** Record inclusion/omission. Data point #2.

---

### T-24: No domain vocabulary needed

- **Input:** `Write a prompt for sorting a list of numbers`
- **Expected:** Vocabulary section OMITTED with a note explaining why (standard programming terms, no routing benefit).
- **Pass Criteria:**
  - [ ] Vocabulary section NOT present
  - [ ] If present: contains only generic terms (sort, list, number) that add no routing value
  - [ ] Changelog notes the omission with AP4 reference
- **Failure Signals:** Vocabulary section present with generic terms like "array," "ascending order," "comparison." This would be AP4 (Parrot Restructuring) — padding a section with terms that don't improve output.
- **Calibration data:** Record omission. Data point #3 (should confirm the agent can omit when appropriate).

---

### T-25: Domain-heavy — quantitative finance

- **Input:** `Write a prompt for analyzing financial derivatives pricing models for mispricing and arbitrage opportunities`
- **Expected:** Vocabulary section INCLUDED. Terms like Black-Scholes, implied volatility surface, Greeks (delta, gamma, vega), put-call parity, risk-neutral pricing have precise quantitative meanings.
- **Pass Criteria:**
  - [ ] Vocabulary section present
  - [ ] Terms are domain-specific quantitative finance terms, not generic "financial" language
  - [ ] Changelog cites Law 1
- **Failure Signals:** Vocabulary omitted. Agent treats quantitative finance as having "standard" terms.
- **Calibration data:** Record inclusion/omission. Data point #4.

---

## Calibration Summary (fill after running all 4)

| Test | Domain | Vocabulary Included? | Domain Terms Warranted? | Correct Decision? |
|---|---|---|---|---|
| T-22 | Clinical biostatistics | | Yes | |
| T-23 | Legal contracts | | Yes | |
| T-24 | Basic programming | | No | |
| T-25 | Quantitative finance | | Yes | |

**Expected pattern:** T-22, T-23, T-25 include Vocabulary. T-24 omits it. 3/4 inclusion on domain-heavy prompts.

**If actual inclusion rate is 0-1/3 on domain-heavy prompts:** The omission anchor from Examples 1-2 is too strong. Consider: reordering examples so Example 3 is first, adding a 4th inclusion example, or adding an explicit instruction "Include Vocabulary when domain terms have precise meanings that differ from lay usage."
