# Test Tracker — Aggregate Results

Track pass/deviation across runs to identify patterns. Update after every test run.

## How to Read
- **PASS:** Behavior matched expected
- **DEV-C / DEV-M / DEV-m:** Deviation — Critical / Major / Minor
- **SKIP:** Test not run in this session
- **N/A:** Test not applicable (e.g., format tests require Phase 3 output)
- **—:** Test didn't exist yet at time of run

## Results Grid

| Test ID | Category | Run 1 | Run 2 | Run 3 | Run 4 | Run 5 | Run 37* | Pattern |
|---|---|---|---|---|---|---|---|---|
| T-01 | Classification | DEV-C | | | | | | SOP never invoked — DBG-001 |
| T-02 | Classification | | | | | PASS | | Low. Tiebreaker toward Low. Zero questions. |
| T-03 | Classification | | | | | PASS | | Medium. Bypass met (3 dims). Domain-appropriate. |
| T-04 | Classification | | | PASS | | | PASS | High correct. Phase 2 entered. Exact sentinel. |
| T-05 | Classification | | | | | DEV-m | | Tiebreaker fired (Medium). Bypass strict — Phase 2 not Phase 4. |
| T-06 | Classification | | | | | PASS | | Low. Vague ≠ complex. AP2/AP5 cited as restructure risks. |
| T-07 | Phase 2 FS | | | PASS | | | PASS | Exact sentinel. 3 questions. Zero bleed-through. |
| T-08 | Phase 2 FS | | | | | PASS | | Phase 2 full stop. 3/3 domains. Exact sentinel. |
| T-09 | Phase 2 FS | | | | | PASS | | R2→Phase 3. No R3. 2-round ceiling held. |
| T-10 | Anti-Pattern | | | | | PASS | | Zero questions. Low → Phase 4. AP1 confirmed. |
| T-11 | Anti-Pattern | | | | PASS | | PASS | Zero scope inflation. Proportionate assessment. |
| T-12 | Anti-Pattern | | | | PASS | | | No unsolicited restructure. Specific strengths. AP3 confirmed. |
| T-13 | Anti-Pattern | | | | | PASS | PASS | Genuine new analysis. 5 new constraints. AP4 confirmed. |
| T-14 | Anti-Pattern | | | | | PASS | PASS | Zero domain overreach. Structure without content. AP5 confirmed. |
| T-15 | Anti-Pattern | | | | | PASS | | Constraints preserved and strengthened. "Never" not softened. AP6 confirmed. |
| T-16 | Anti-Pattern | | | | PASS | | PASS | Zero bleed-through. 3/3 on this input. AP7 confirmed. |
| T-17 | Deliverable | | | | DEV-m | | | Vocabulary/Task swapped (positions 2↔3). Content fine. |
| T-18 | Deliverable | | | | PASS | | PASS | 10-row changelog. 100% specificity. Exceptional. |
| T-19 | Deliverable | | | | PASS* | | PASS | Correct position+text. Blockquote format unverifiable from paste. |
| T-20 | Deliverable | | | | PASS | | | [USER: confirm?] on exactly the right constraint. |
| T-21 | Deliverable | | | | PASS | | PASS | Zero tag leakage. Internal tracing worked. |
| T-22 | Vocabulary | | | | | PASS | | Vocabulary INCLUDED. Domain terms precise. Example 3 anchoring works. |
| T-23 | Vocabulary | | | | | PASS | | Vocabulary INCLUDED. Novel domain (legal). Generalization works. |
| T-24 | Vocabulary | | | | | PASS | | Vocabulary correctly omitted. Generic domain. |
| T-25 | Vocabulary | | | | | PASS | | Vocabulary INCLUDED. Precise quant terms. 3rd novel domain. |
| T-26 | Session | | | | PASS* | | | Partial: prompt-N warning at N=3 confirmed. Full test needs 3 complete cycles. |
| T-27 | Session | | | | PASS | | | Zero contamination. Genetics→pasta. Complete domain separation. |
| T-28 | Session | | | | | PASS | | 5 prompts. Prompt-N at 3/4/5. Zero contamination. No degradation. |
| T-29 | Edge Case | | | | | PASS | | Clean exclusion. Direct answer. SOP on-ramp offered. |
| T-30 | Edge Case | | | | | PASS | | Acknowledged 3. Sequential. "Prompt 1 of 3." No merging. |
| T-31 | Edge Case | | | | | PASS | | Skip acknowledged. Default documented. Cross-answer reasoning. |
| T-32 | Edge Case | | | | | PASS | | Found 2 contradictions (expected 1). Did not pick sides. Sentinel held. |
| T-33 | Edge Case | | | | | PASS | | 7 per-assumption rows. 3 [USER: confirm?]. Decline handled correctly. |
| T-34 | Edge Case | | | | | PASS | | Explicit abandonment. Fresh triage. Zero contamination. |
| T-35 | Activation | PASS | PASS | | | | | Greeting works — standalone and inline |
| T-36 | Activation | DEV-M | PASS | | | | PASS | Run 2: DBG-002. Run 37: strongest routing — coding-adjacent real-world input |
| T-37 | Activation | | | | | PASS | PASS | Clean exclusion. Run 37: 4 distinct inputs, all correct. |
| T-38 | Activation | | | | | PASS | PASS | Clean exclusion. File-editing mode. Category 2 confirmed. |
| T-39 | Activation | | | PASS | PASS | | | Disambiguation consistent across run-04 and run-07 |
| T-40 | Activation | | | | PASS | | | DBG-001 input completes full SOP. Low → Phase 4. |
| T-41 | Activation | | | | | PASS | PASS | Run 37: 6+ mode switches — strongest validation. |

*Run 37 = observational validation from production use (not controlled test inputs). See run-2026-04-05-37.md for methodology notes.

## Pattern Legend
- **Stable:** PASS across all runs
- **Intermittent:** Mix of PASS and DEV — investigate trigger conditions
- **Consistent failure:** DEV across all runs — spec instruction isn't working
- **Improving:** DEV → PASS after spec fix — fix is working
- **Regressed:** Was PASS, now DEV after spec change — fix broke something

## Run Log

| Run ID | Date | Spec Version | Tests Run | Pass Rate | Notes |
|---|---|---|---|---|---|
| run-2026-04-05-01 | 2026-04-05 | post-review | T-01, T-35 | 0/1 | Critical: SOP activation failure (DBG-001). T-35 informal: greeting protocol misunderstood Claude Code. |
| run-2026-04-05-02 | 2026-04-05 | post-DBG-001 fix | T-35, T-36 | 1/2 | T-35 PASS. T-36 DEV-M: over-disambiguation (DBG-002) |
| run-2026-04-05-03 | 2026-04-05 | post-DBG-002 fix | T-35, T-36 | 2/2 | Both PASS. First successful SOP activation. |
| run-2026-04-05-04 | 2026-04-05 | post-DBG-002 fix | T-39 | 1/1 | PASS. Disambiguation calibration confirmed. |
| run-2026-04-05-05 | 2026-04-05 | post-DBG-002 fix | T-07 | 1/1 | PASS. Phase 2 full stop. Exact sentinel. |
| run-2026-04-05-06 | 2026-04-05 | post-DBG-002 fix | T-04 | 1/1 | PASS. High classification. Consistent with T-07. |
| run-2026-04-05-07 | 2026-04-05 | post-DBG-002 fix | T-39, T-40 | 2/2 | Both PASS. DBG-001 loop closed. |
| run-2026-04-05-08 | 2026-04-05 | post-DBG-002 fix | T-11 | 1/1 | PASS. Zero scope inflation. AP2 resistance confirmed. |
| run-2026-04-05-09 | 2026-04-05 | post-DBG-002 fix | T-12 | 1/1 | PASS. No unsolicited restructure. AP3 confirmed. |
| run-2026-04-05-10 | 2026-04-05 | post-DBG-002 fix | T-16 | 1/1 | PASS. Zero bleed-through. 3/3 on this input. |
| run-2026-04-05-11 | 2026-04-05 | post-DBG-002 fix | T-17,18,19,20,21 | 4/5 | T-17 DEV-m (section swap). T-18,19,20,21 PASS. First Phase 3 output. |
| run-2026-04-05-12 | 2026-04-05 | post-DBG-002 fix | T-27, T-26* | 2/2 | Zero contamination. Prompt-N at 3. Genetics restructure exceptional. |
| run-2026-04-05-13 | 2026-04-05 | post-DBG-002 fix | T-37 | 1/1 | PASS. Exclusion cat 1. Over-activation risk mitigated. |
| run-2026-04-05-14 | 2026-04-05 | post-DBG-002 fix | T-22 | 1/1 | PASS. Vocabulary included. Example 3 anchoring confirmed. |
| run-2026-04-05-15 | 2026-04-05 | post-DBG-002 fix | T-23 | 1/1 | PASS. Vocabulary included on novel domain (legal). |
| run-2026-04-05-16 | 2026-04-05 | post-DBG-002 fix | T-33 | 1/1 | PASS. "Just do it" handled. 7 per-assumption changelog rows. |
| run-2026-04-05-17 | 2026-04-05 | post-DBG-002 fix | T-41 | 1/1 | PASS. 3 mode switches. Router stable. Zero contamination. |
| run-2026-04-05-18 | 2026-04-05 | post-DBG-002 fix | T-10 | 1/1 | PASS. Zero questions. AP1 resistance. |
| run-2026-04-05-19 | 2026-04-05 | post-DBG-002 fix | T-24 | 1/1 | PASS. Vocabulary omitted. 5/5 calibration. |
| run-2026-04-05-20 | 2026-04-05 | post-DBG-002 fix | T-13 | 1/1 | PASS. Genuine new analysis. AP4 resistance. |
| run-2026-04-05-21 | 2026-04-05 | post-DBG-002 fix | T-14 | 1/1 | PASS. Zero domain overreach. AP5 confirmed. |
| run-2026-04-05-22 | 2026-04-05 | post-DBG-002 fix | T-15 | 1/1 | PASS. Constraints preserved+strengthened. AP6 confirmed. |
| run-2026-04-05-23 | 2026-04-05 | post-DBG-002 fix | T-34 | 1/1 | PASS. New prompt recognized. Explicit abandonment. Zero contamination. |
| run-2026-04-05-24 | 2026-04-05 | post-DBG-002 fix | T-05 | 1/1 | DEV-m. Tiebreaker correct. Bypass strict. |
| run-2026-04-05-25 | 2026-04-05 | post-DBG-002 fix | T-06 | 1/1 | PASS. Low. Vague ≠ complex. |
| run-2026-04-05-26 | 2026-04-05 | post-DBG-002 fix | T-02 | 1/1 | PASS. Low. Tiebreaker correct. |
| run-2026-04-05-27 | 2026-04-05 | post-DBG-002 fix | T-03 | 1/1 | PASS. Medium. Bypass threshold met. |
| run-2026-04-05-28 | 2026-04-05 | post-DBG-002 fix | T-29 | 1/1 | PASS. Meta-prompt answered directly. |
| run-2026-04-05-29 | 2026-04-05 | post-DBG-002 fix | T-08, T-09 | 1/2 | T-08 PASS. T-09 PENDING (Round 2 legitimate). |
| run-2026-04-05-30 | 2026-04-05 | post-DBG-002 fix | T-09 | 1/1 | PASS. T-09 completed. R2→Phase 3. No R3. |
| run-2026-04-05-31 | 2026-04-05 | post-DBG-002 fix | T-25 | 1/1 | PASS. Vocabulary included. 8/8 calibration complete. |
| run-2026-04-05-32 | 2026-04-05 | post-DBG-002 fix | T-31 | 1/1 | PASS. Partial answers handled. Default documented. |
| run-2026-04-05-33 | 2026-04-05 | post-DBG-002 fix | T-32 | 1/1 | PASS. 2 contradictions found. Did not pick sides. |
| run-2026-04-05-34 | 2026-04-05 | post-DBG-002 fix | T-38 | 1/1 | PASS. File-edit exclusion. Zero SOP artifacts. |
| run-2026-04-05-35 | 2026-04-05 | post-DBG-002 fix | T-30 | 1/1 | PASS. Multi-prompt acknowledged. Sequential. Independent triage. |
| run-2026-04-05-36 | 2026-04-05 | post-DBG-002 fix | T-28 | 1/1 | PASS. 5-prompt endurance. Zero degradation. FINAL TEST. |
| run-2026-04-05-37 | 2026-04-05 | post-review | T-04,07,11,13,14,16,18,19,21,36,37,38,41 | 13/13 | Observational: production use (dnd-session-manager prompt chain). Strongest T-36, T-41 signals. |
