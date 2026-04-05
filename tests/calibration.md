# Calibration Data

Collect data on numeric parameters and behavioral thresholds to tune the spec. Each parameter starts as a design choice — calibration converts it to an evidence-based setting.

## Parameter 1: Question Ceiling (currently: 3 per round, max 2 rounds)

**Spec location:** CLAUDE.md L75, L135
**Hypothesis:** 3 per round is the right balance between thoroughness and user friction.
**Decision rule:** If skip rate >40% across 10+ observations, try reducing to 2. If users consistently need more rounds, consider raising to 3 rounds.

| Run | Test ID | Questions Asked (Round 1) | Questions Answered | Questions Skipped | Round 2 Triggered? | Notes |
|---|---|---|---|---|---|---|
| | | | | | | |

**Current skip rate:** — / — (N observations)

---

## Parameter 2: Tiebreaker Aggression (currently: classify at lower level)

**Spec location:** CLAUDE.md L66
**Hypothesis:** Favoring lower classification prevents over-elicitation (AP1). Phase 4 "want me to restructure?" is the safety net.
**Decision rule:** If >30% of Phase 4 bypass users request restructuring anyway, the tiebreaker is too aggressive.

| Run | Test ID | Prompt | Classified As | Borderline? | User Requested Restructure from Phase 4? | Notes |
|---|---|---|---|---|---|---|
| | | | | | | |

**Restructure request rate from Phase 4:** — / — (N observations)

---

## Parameter 3: Vocabulary Section Inclusion Rate

**Spec location:** CLAUDE.md L30 (section order), L87 (omit if no value), Known Limitations
**Hypothesis:** Example 3 provides a positive exemplar; the agent includes Vocabulary when domain terms warrant it.
**Decision rule:** If inclusion rate <33% on domain-heavy prompts (T-22, T-23, T-25 type inputs), the omission anchor is too strong.

| Run | Test ID | Domain | Domain-Specific Terms Warranted? | Vocabulary Included? | Correct Decision? | Notes |
|---|---|---|---|---|---|---|
| | | | | | | |

**Inclusion rate (domain-heavy):** — / — (N observations)
**Omission rate (generic):** — / — (N observations)

---

## Parameter 4: Changelog Quality

**Spec location:** CLAUDE.md L40 ("cite a specific risk, failure mode, or gap — not a generic improvement claim")
**Hypothesis:** Source+gap tags during generation produce specific changelog entries.
**Decision rule:** Target >80% of "Why Added" entries citing a specific risk. If <60%, the generation-time tag mechanism isn't producing sufficient specificity.

| Run | Test ID | Total Changelog Rows | Rows with Specific Risk | Rows with Vague Justification | Specificity Rate | Notes |
|---|---|---|---|---|---|---|
| | | | | | | |

**Aggregate specificity rate:** — / — (N rows)

---

## Parameter 5: Edit Loop Frequency

**Spec location:** CLAUDE.md L103
**Hypothesis:** Phase 3 restructures are close enough to user intent that 3+ edit rounds are rare.
**Decision rule:** If >20% of restructures enter 3+ edit rounds, the Phase 3 output is consistently missing the mark.

| Run | Test ID | Edit Rounds | User Accepted Version | Reached "Restructure from Scratch" Offer? | Notes |
|---|---|---|---|---|---|
| | | | | | |

**3+ edit rate:** — / — (N observations)

---

## Parameter 6: Phase 2 Full Stop Compliance

**Spec location:** CLAUDE.md L10, L77, L127, L131
**Hypothesis:** Five reinforcement points (L10, L77, L81, L127, L131) are sufficient to hold the full stop.
**Decision rule:** Any bleed-through is a Critical deviation. If compliance <95% across 20+ observations, add a 6th reinforcement point or restructure the mechanism.

| Run | Test ID | Sentinel Exact Match? | Bleed-Through Detected? | Type of Bleed-Through | Notes |
|---|---|---|---|---|---|
| | | | | | |

**Compliance rate:** — / — (N observations)
**Sentinel exact-match rate:** — / — (N observations)
