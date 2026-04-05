# Regression Suite

Minimum tests to run after any CLAUDE.md spec change. These 8 tests cover the highest-risk behavioral constraints. If all 8 pass, the change is unlikely to have broken core behavior.

## When to Run
- After ANY edit to CLAUDE.md
- After editing references/anti-patterns-full.md or references/phase2-conditionals.md
- After editing prompt-optimization-example.md (few-shot anchors)
- After adding or removing a reference file

## The 10 Regression Tests

| Priority | Test ID | What It Guards | Spec Lines |
|---|---|---|---|
| 1 | **T-36** | SOP activation (default-to-SOP works) | L1-3, L11-25, L156 |
| 2 | **T-39** | Disambiguation fires on ambiguous input | L22-23 |
| 3 | **T-07** | Phase 2 full stop + sentinel | L10, L77, L127, L131 |
| 4 | **T-04** | High-complexity triage → Phase 2 entry | L58-64 |
| 5 | **T-40** | Low-complexity bypass after activation (T-01 re-run) | L62, L66 |
| 6 | **T-11** | Scope Inflation resistance | L122 |
| 7 | **T-12** | Restructure When Unnecessary resistance | L123 |
| 8 | **T-16** | Phase 2 Bleed-Through resistance | L127 |
| 9 | **T-17** | Deliverable section order | L30, L93-94 |
| 10 | **T-27** | Cross-prompt contamination | L112-113 |

## Conditional Regression Tests

Run these in addition to the 10 core tests when the specified section changes:

| If You Changed | Also Run | Why |
|---|---|---|
| Environment Override, Greeting, or Input Router | T-35, T-37, T-38, T-41 | Activation mechanism coverage |
| Phase 2 section or phase2-conditionals.md | T-31, T-32, T-33, T-34 | Edge case handling depends on these files |
| Deliverables section | T-18, T-19, T-20, T-21 | Format compliance |
| Bypass thresholds or tiebreaker | T-02, T-03, T-05, T-06 | Classification accuracy |
| Few-shot example file | T-22, T-23, T-24, T-25 | Vocabulary inclusion calibration |
| Anti-pattern watchlist | T-10, T-13, T-14, T-15 | Anti-pattern probe coverage |
| Interaction Model or Invariants | T-26, T-28 | Session continuity |

## Recording Results

Use the standard run template (`runs/_run-template.md`). Mark the run as "regression" in the Notes field. Update `tracker.md` with the results.

## Pass Criteria

- **All 8 core tests PASS:** Change is safe. Proceed.
- **1-2 Minor deviations:** Likely acceptable. Log in debug-log.md. Monitor in next full run.
- **Any Major or Critical deviation:** Revert the spec change. Investigate in debug-log.md. Re-apply after fixing the root cause.
