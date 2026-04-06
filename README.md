# Prompt Engineer Agent

A Claude Code agent specification for a Requirements Analyst that triages, clarifies, and restructures user prompts into a structured 5-dimension format (Context, Task, Vocabulary, Constraints, Acceptance Criteria).

**Status: Author-tested; independent validation welcome.** 37 test runs (36 controlled + 1 observational from production use). 52 unique test evaluations, 96% clean pass rate (50/52), 2 bugs found and fixed, 0 open Critical or Major issues. See [Limitations](#limitations) for what this does and does not demonstrate.

## What This Is

An agent specification (`CLAUDE.md`) that runs inside [Claude Code](https://docs.anthropic.com/en/docs/claude-code). When a user submits a prompt, the agent:

1. **Routes** — determines whether the input is a prompt to optimize, a question about the agent, or a file operation
2. **Triages** — classifies complexity (low/medium/high) and applies bypass thresholds
3. **Clarifies** — asks targeted questions to fill gaps (high-complexity only, max 3 questions per round, max 2 rounds)
4. **Restructures** — produces an optimized prompt with up to 5 sections, each traceable to the user's input
5. **Defers** — the user decides whether to use the optimized version, edit it, or keep their original

The agent never executes the optimized prompt. It structures prompts — the user runs them.

## Design Methodology

Built using the **Six Laws of Context Engineering**, a set of design principles for LLM agent specifications, synthesized from peer-reviewed research on LLM behavior. "Laws" is aspirational naming — these are empirically-motivated heuristics, not universally validated rules. Full methodology with 14 verified citations in `references/METHODOLOGY.md`. The six principles:

1. **Vocabulary Routing** — 15-30 precise domain terms in 3-5 clusters with attribution
2. **Real-World Roles** — real job titles, brief identity statements, no flattery
3. **Scaling Laws** — start single-agent, escalate only on demonstrated failure
4. **Anti-Patterns** — name failure modes explicitly with detection triggers and resolution steps
5. **Progressive Disclosure** — layer information, place critical content at beginning/end (U-shaped attention)
6. **Structured Artifacts** — typed deliverables with schemas and acceptance criteria

All citations verified against primary sources via arXiv, ACL Anthology, and publisher websites. Verification report in `references/works-cited.md`.

## Test Results

| Metric | Result |
|---|---|
| Test runs | 37 (36 controlled + 1 observational) |
| Unique test evaluations | 52 |
| Pass rate | 96% (50 pass, 2 minor deviations) |
| Bugs found and fixed | 2 (DBG-001 activation, DBG-002 disambiguation) |
| Open Critical/Major issues | 0 |
| Anti-patterns confirmed | 7/7 (100%) |
| Vocabulary calibration (test outcomes) | 8/8 correct inclusion/exclusion decisions (100%) |
| Phase 2 sentinel exact match | 5/5 (100%) |
| Cross-prompt contamination | 0 instances across all multi-prompt tests |
| Sources verified against web | 14/14 exist, 4 corrections applied |
| Production use validation | 13/13 tests passed during real-world prompt chain engineering |

### Bugs Found and Fixed During Testing

**DBG-001 (Critical): Agent did not enter SOP.** Claude Code's default software-engineering-assistant behavior overrode the CLAUDE.md role. Fixed by adding an Environment Override, Greeting Protocol, and Input Router with exclusion categories and disambiguation.

**DBG-002 (Major): Disambiguation fired on unambiguous prompts.** The Input Router's "when ambiguous, ask" instruction was too sensitive — any coding-style prompt triggered disambiguation. Fixed by reframing to a closed-form check: only disambiguate when input partially matches an exclusion category.

Both bugs were found, diagnosed with a 5-step debug protocol, fixed, and confirmed resolved through regression testing. Full investigation records in `tests/debug-log.md`.

### Production Use Validation (Run 37)

The agent was used for a real-world prompt engineering task: designing a 4-prompt coordinator activation chain for a D&D Session Manager Electron application (40 tasks across 8 phases, 1,376-line design document). During this production use, 13 test behaviors were observed passing — including the strongest signals to date for input routing (T-36: coding-adjacent phrasing correctly routed to SOP), scope discipline (T-11: zero inflation despite access to full 40-task spec), and mode switching (T-41: 6+ switches in one conversation). Full details in `tests/runs/run-2026-04-05-37.md`.

### Minor Deviations (not fixed — cosmetic)

- **T-17:** Vocabulary and Task sections swap positions (2↔3) when both are present. Content unaffected. Consistent across all 4 restructures that include Vocabulary.
- **T-05:** Borderline Medium prompt entered Phase 2 instead of bypassing. Tiebreaker toward lower classification fired correctly; bypass threshold evaluated strictly. Conservative but defensible.

## Project Structure

```
CLAUDE.md                              # Agent specification (the agent itself)
references/
  METHODOLOGY.md                       # Six Laws methodology — canonical source with citations
  six-laws.md                          # Six Laws quick reference with evidence summaries
  anti-patterns-full.md                # 7 anti-patterns with MAST mappings and resolution steps
  phase2-conditionals.md               # Phase 2 edge case handling (extracted for attention optimization)
  prompt-optimization-example.md       # 3 few-shot examples anchoring output format
  works-cited.md                       # Citation verification report — all 14 sources checked
tests/
  TESTING.md                           # Test process, conventions, and workflow documentation
  test-cases/                          # 41 test cases across 8 categories
    T01-T06-classification.md          # Triage accuracy (6 tests)
    T07-T09-phase2-fullstop.md         # Phase 2 behavioral constraint (3 tests)
    T10-T16-anti-patterns.md           # Anti-pattern resistance (7 tests)
    T17-T21-deliverable-format.md      # Output schema compliance (5 tests)
    T22-T25-vocabulary-inclusion.md    # Vocabulary section calibration (4 tests)
    T26-T28-session-continuity.md      # Cross-prompt contamination (3 tests)
    T29-T34-edge-cases.md             # Boundary conditions (6 tests)
    T35-T41-activation.md             # Activation signal + Input Router (7 tests)
  runs/                                # 37 test run logs
    _run-template.md                   # Template for recording results
    run-2026-04-05-01.md through run-2026-04-05-37.md
  tracker.md                           # Aggregate pass/deviation grid across all runs
  calibration.md                       # 6 tunable parameters with data collection tables
  debug-log.md                         # 2 investigations (DBG-001, DBG-002) with 5-step protocol
  regression-suite.md                  # 10 core + conditional regression tests
```

## Limitations

This section documents what the current testing does and does not establish, and where the methodology's evidence base has gaps.

### Testing Scope

- **Single rater, single session.** All 36 test runs were conducted by the author on 2026-04-05. Inter-rater reliability is untested. LLM behavior varies across sessions, model versions, and system load — a single-day test suite provides a snapshot, not a reliability guarantee.
- **Compliance testing only.** All tests verify the agent follows its own spec. No test measures whether restructured prompts produce *better LLM outputs* than the originals. The core value proposition (structured prompts → higher-quality responses) is reasonable but untested in this project.
- **Small sample sizes.** Most test cases have 1-2 observations. The 95% pass rate is a point estimate — the confidence interval is wide. No statistical significance claims are warranted.

### Calibration Framework

The file `tests/calibration.md` defines 6 tunable parameters with hypotheses, spec locations, and data collection tables. **All data tables are currently empty.** The calibration framework exists but has not been populated with empirical data. The "Vocabulary calibration: 8/8" metric in the test results table refers to test case pass/fail outcomes, not formal parameter tuning.

### Methodology Transfer Assumptions

- **Law 3 (Scaling Laws)** cites Kim et al.'s research on multi-agent systems. This project is single-agent. The law is included for completeness and to justify the single-agent design choice, but the 45% threshold from Kim et al. was validated in a multi-agent context, not prompt specification design.
- **Law 1 (Vocabulary Routing)** cites Ranjan's +0.658 Spearman correlation for word embeddings. The link between embedding quality and instruction-following in agent specs is inferred, not directly demonstrated.

### Untested Scenarios

- **Adversarial inputs:** No test cases probe injection-style attacks or inputs designed to subvert the SOP.
- **Model version variance:** All testing used one Claude model. Behavior may differ across model versions (Sonnet, Opus, Haiku).
- **Long-session degradation beyond 5 prompts:** T-28 tests up to 5 prompts. The spec warns at 8, but degradation between 5 and 8 is uncharacterized.
- **Competing CLAUDE.md files:** Interaction between a user's global CLAUDE.md and this project-level spec is untested.

### Naming

The methodology uses the term "Laws." These are design heuristics derived from research synthesis — not empirically validated universal laws. The naming is aspirational and reflects the intended rigor of the underlying research, not a claim of proof.

## How to Use

1. Install [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
2. Clone this repo
3. Open a Claude Code conversation in the project directory
4. Submit a prompt — the agent will triage and process it per the SOP

## How to Run Tests

See `tests/TESTING.md` for the full process. Summary:

1. Open a Claude Code conversation in this project directory
2. Submit test inputs from `tests/test-cases/` files
3. Record observations in a copy of `tests/runs/_run-template.md`
4. Update `tests/tracker.md` with results
5. Investigate deviations in `tests/debug-log.md`
6. After spec changes, run the 10-test regression suite (`tests/regression-suite.md`)
