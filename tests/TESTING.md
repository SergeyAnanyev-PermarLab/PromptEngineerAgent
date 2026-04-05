# Testing the Requirements Analyst Agent

## Purpose
This test harness validates the CLAUDE.md agent specification against actual runtime behavior. Every test traces to a specific spec instruction. Every observation traces to a test case. Every fix traces to a deviation.

## Traceability Chain
```
Spec line (CLAUDE.md:NN) → Test case (T-XX) → Run observation → Deviation → Debug investigation → Spec fix → Regression re-run
```

## How to Run Tests

### Setup
1. Open a new Claude Code conversation in the `PromptEngineerAgent` project directory
2. The agent loads CLAUDE.md automatically — this IS the agent under test
3. Do not modify CLAUDE.md between tests within the same run

### Running a single test
1. Open the relevant test case file (e.g., `test-cases/T01-T06-classification.md`)
2. Copy the **Input** prompt exactly as written into the agent conversation
3. Observe the agent's response
4. Record results using `runs/_run-template.md` (copy it to a new file named `run-YYYY-MM-DD-NN.md`)

### Running a full suite
1. Copy `_run-template.md` to `runs/run-YYYY-MM-DD-01.md`
2. Run tests in order: T-01 through T-34
3. For session continuity tests (T-26 through T-28), use a single conversation
4. For all other tests, a fresh conversation per test is ideal but not required — note if reusing a session
5. After recording, update `tracker.md` with pass/deviation for each test
6. If deviations found, open a debug investigation in `debug-log.md`

### After a spec change
1. Run the regression suite (`regression-suite.md`) — minimum 8 tests
2. Re-run any test that previously showed deviations related to the changed section
3. Update `tracker.md` with the new run column

## Conventions

### Test case IDs
- `T-XX` format, numbered sequentially
- Grouped by category in files (classification, phase2, anti-patterns, etc.)
- IDs are stable — never renumber. If a test is retired, mark it `[RETIRED]` with reason

### Run file naming
- `run-YYYY-MM-DD-NN.md` where NN is the run number for that date
- One run file per test session (may cover multiple test cases)

### Deviation severity
- **Critical:** Agent violates an Invariant (CLAUDE.md L131-137) or Critical Rule (L10-11)
- **Major:** Agent violates an SOP step or anti-pattern guard but output is partially usable
- **Minor:** Output format issue, slight misclassification, or suboptimal but correct behavior
- **None:** Behavior matches expected

### Spec version tracking
- Before each run, record the first 8 characters of the CLAUDE.md file hash or the last-modified timestamp
- This ensures deviations are attributed to the correct spec version

## File Index

| File | Purpose | When to Use |
|---|---|---|
| `test-cases/T01-T06-classification.md` | Triage accuracy tests | Every full run |
| `test-cases/T07-T09-phase2-fullstop.md` | Phase 2 behavioral constraint | Every full run + after any Phase 2 spec change |
| `test-cases/T10-T16-anti-patterns.md` | Anti-pattern probes | Every full run |
| `test-cases/T17-T21-deliverable-format.md` | Output schema compliance | Every full run + after Deliverables section change |
| `test-cases/T22-T25-vocabulary-inclusion.md` | Vocabulary calibration | Every full run + after few-shot example change |
| `test-cases/T26-T28-session-continuity.md` | Cross-prompt contamination | Every full run (requires single conversation) |
| `test-cases/T29-T34-edge-cases.md` | Boundary conditions | Every full run |
| `test-cases/T35-T41-activation.md` | Activation signal + Input Router | Every full run + after any Environment Override/Input Router change |
| `runs/_run-template.md` | Template for recording test results | Copy for each new run |
| `tracker.md` | Aggregate pass/deviation across runs | Update after every run |
| `calibration.md` | Numeric parameter tuning data | Update when observing threshold-sensitive behavior |
| `debug-log.md` | Investigation records for deviations | When a deviation needs root-cause analysis |
| `regression-suite.md` | Minimum tests after spec changes | After any CLAUDE.md edit |
