# Category: Session Continuity and Contamination

**Spec lines under test:** CLAUDE.md L112-113 (re-entry invariant, contamination detection), L137 (traceability invariant)
**Known Limitation:** CLAUDE.md Known Limitations flags context degradation at scale as untested.
**IMPORTANT:** These tests MUST run in a single conversation. Do not start a new session between prompts.

---

### T-26: Prompt-N warning at session threshold

- **Procedure:**
  1. Submit prompt 1: `Write a prompt that summarizes long emails into 3 bullet points`
  2. Complete the full SOP cycle (through Phase 4)
  3. Submit prompt 2: `Write a prompt that generates SQL queries from natural language`
  4. Complete the full SOP cycle
  5. Submit prompt 3: `Write a prompt for reviewing code for security vulnerabilities`
- **Expected at prompt 3:** Agent states "This is prompt 3 in this session — I'm resetting context. Flag any carryover from earlier prompts if you spot it." (per L113)
- **Pass Criteria:**
  - [ ] Prompt-N warning appears at prompt 3
  - [ ] Warning uses language close to the L113 template
  - [ ] Agent proceeds with normal triage after the warning
  - [ ] No email or SQL vocabulary from prompts 1-2 appears in prompt 3's restructure
- **Failure Signals:** No warning at prompt 3. Warning appears at prompt 2 (too early). Warning doesn't appear until prompt 4+ (too late). Email/SQL terms contaminate prompt 3.

---

### T-27: Cross-domain contamination — explicit check

- **Procedure:**
  1. Submit prompt 1: `Write a prompt for diagnosing rare genetic disorders from patient symptom profiles, using HPO ontology terms and OMIM database references`
  2. Complete Phase 2→3→4 (this loads medical/genetic vocabulary)
  3. Submit prompt 2: `Write a prompt for generating creative pasta recipes from available ingredients`
- **Expected:** Prompt 2's restructure contains ZERO medical/genetic vocabulary or constraints from prompt 1.
- **Pass Criteria:**
  - [ ] No HPO, OMIM, genetic, diagnostic, or clinical terms in prompt 2
  - [ ] No constraints about accuracy, patient safety, or clinical validation in prompt 2
  - [ ] Prompt 2 stands alone — a reader with no context would not know a medical prompt preceded it
  - [ ] Changelog for prompt 2 traces entirely to "pasta recipes from available ingredients"
- **Failure Signals:** Medical vocabulary leaks (e.g., "ensure accuracy" constraint that came from the diagnostic context). Constraints from prompt 1's domain appear in prompt 2. Changelog references risks from prompt 1's domain.
- **Contamination trace:** If contamination is found, record the exact term/constraint and which prompt-1 element it maps to. This helps diagnose whether the contamination comes from vocabulary routing (Cluster residue) or constraint carryover.

---

### T-28: Context degradation at scale

- **Procedure:** Submit 5 prompts sequentially in a single session, completing each cycle:
  1. `Write a prompt that summarizes research papers`
  2. `Write a prompt for generating test data for a database`
  3. `Write a prompt for translating technical documentation`
  4. `Write a prompt for prioritizing bug reports by severity`
  5. `Write a prompt for writing release notes from git commit history`
- **Observe across all 5:**
  - Does quality degrade on prompts 4-5?
  - Does the prompt-N warning continue appearing (prompts 3, 4, 5)?
  - Do deliverables maintain format compliance?
  - Does Phase 2 full stop hold on later prompts?

- **Pass Criteria:**
  - [ ] Prompt-N warning at prompts 3, 4, and 5
  - [ ] Deliverable format consistent across all 5 (section order, changelog, blockquote)
  - [ ] No cross-prompt vocabulary contamination
  - [ ] Phase 2 full stop holds if any prompt enters Phase 2
  - [ ] Quality of restructuring on prompt 5 is comparable to prompt 1
- **Failure Signals:** Warning stops appearing. Deliverable format degrades (missing sections, missing changelog). Vocabulary/constraints from earlier prompts appear. Phase 2 bleed-through on later prompts. Agent becomes "lazy" (shorter, less specific restructures).
- **Calibration data:** Rate the quality of each prompt's restructure on a 1-5 scale. Plot for degradation curve.

| Prompt # | Format Compliance | Restructure Quality (1-5) | Contamination? | Notes |
|---|---|---|---|---|
| 1 | | | | |
| 2 | | | | |
| 3 | | | | |
| 4 | | | | |
| 5 | | | | |
