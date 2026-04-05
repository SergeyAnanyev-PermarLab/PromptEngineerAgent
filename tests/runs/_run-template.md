# Test Run: run-YYYY-MM-DD-NN

## Run Metadata
- **Date:** YYYY-MM-DD
- **Spec version:** [first 8 chars of CLAUDE.md hash or last-modified timestamp]
- **Model:** [model ID used, e.g., claude-opus-4-6]
- **Tester:** [name]
- **Session type:** [fresh conversation | continued session | multi-prompt session]
- **Tests run:** [list test IDs, e.g., T-01 through T-09]
- **Notes:** [any conditions that may affect results — e.g., "context was already long from prior tests"]

---

## Results

<!-- Copy one block per test case run. Delete unused blocks. -->

### T-XX: [test name]
- **Input used:** [exact prompt submitted, or "as specified in test case"]
- **Actual classification:** [Low | Medium | High | N/A]
- **Actual phase reached:** [Phase 1 | Phase 2 | Phase 3 | Phase 4]
- **Pass criteria checklist:**
  - [ ] [copy criteria from test case, check each]
  - [ ] ...
- **Deviation:** None | [describe what differed from expected]
- **Deviation severity:** N/A | Critical | Major | Minor
- **Sentinel check (if Phase 2):**
  - Exact match: [Yes | No]
  - Actual final sentence: `[paste exact text]`
- **Agent's actual response (key excerpt):**
```
[paste the relevant portion — classification statement, questions asked, restructured output, etc.
keep it to the minimum needed to evaluate pass/fail. full responses can be stored separately if needed.]
```
- **Notes:** [observations, surprises, patterns]

---

## Run Summary

| Test ID | Result | Deviation Severity | Key Observation |
|---|---|---|---|
| T-XX | PASS / DEVIATION | N/A / Critical / Major / Minor | [one-line note] |
| ... | | | |

## Deviations Requiring Investigation

<!-- List any deviations that need debug-log entries -->

| Test ID | Deviation | Suspected Root Cause | Debug Log Entry? |
|---|---|---|---|
| | | | [ ] Created in debug-log.md |

## Calibration Data Collected

<!-- Copy any calibration-relevant observations to calibration.md -->

| Parameter | Observation | Transferred to calibration.md? |
|---|---|---|
| | | [ ] |
