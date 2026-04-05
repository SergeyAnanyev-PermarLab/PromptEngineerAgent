# Static Review Fixes — 2026-04-05

Source: Two-pass review (initial review by Opus 4.6 reviewer, verification pass by Opus 4.6 behavioral scientist). The initial review produced 6 Axis-A findings, 2 Axis-B findings, and 4 Axis-C findings. The verification pass confirmed 10, corrected 3 (line citation errors, misapplied Law 5 analysis, unsupported magnitude claim), and identified 3 additional issues the reviewer missed.

Spec version before fixes: post-DBG-002 (the version that passed 36 test runs)
Spec version after fixes: post-static-review

---

## Fixes Applied

### FIX-A1: Input Router exclusion 2 vs. Environment Override contradiction

- **File:** CLAUDE.md L16
- **Issue:** L2 says "not code execution, file editing, or general software engineering." L16 says "Execute the file operation, do not enter SOP." Contradictory instructions — the agent is told its function is NOT file editing, then told to execute file edits on request.
- **Root cause:** Environment Override was written to block default behavior; exclusion category 2 was written to handle explicit user requests. Neither acknowledged the other.
- **Fix:** Added scoping clause to L16: "Execute the file operation using Claude Code's standard capabilities, do not enter SOP. (The Environment Override restricts default interpretation, not tool access — file operations requested explicitly are permitted.)"
- **Risk if unaddressed:** Agent either refuses legitimate file edit requests or ignores the Environment Override — inconsistent behavior depending on which instruction wins attention.

### FIX-A2: Phase 4 bypass-to-Phase-2 path unspecified triage behavior

- **File:** CLAUDE.md L118 (now L119)
- **Issue:** When a low/medium-complexity prompt bypasses to Phase 4 and the user requests restructuring, L118 says "proceed to Phase 2" without specifying whether to re-triage or apply high-complexity protocol.
- **Root cause:** The bypass→restructure path was specified as a destination without specifying what classification the prompt carries into Phase 2.
- **Fix:** Changed to: "proceed to Phase 2 (Clarify), treating the request as implicit high-complexity classification regardless of original triage."
- **Risk if unaddressed:** Agent enters Phase 2 without knowing how many questions to ask or whether the full elicitation protocol applies.

### FIX-A3: No context window management mitigation

- **File:** CLAUDE.md L166 (now L167)
- **Issue:** Known Limitation acknowledged context degradation at 5+ prompts but provided no concrete mitigation beyond the prompt-N warning at N≥3.
- **Root cause:** Known Limitation was written as a monitoring note, not an actionable mitigation.
- **Fix:** Added mitigation: "after prompt 5, recommend the user start a fresh conversation. After prompt 8, state that context reliability is degraded and actively recommend a new session."
- **Risk if unaddressed:** Agent degrades silently in long sessions with no user-facing signal.

### FIX-A4: Phase 2 conditionals missing counter-question scenario

- **File:** references/phase2-conditionals.md (new section added)
- **Issue:** File covered partial answers, declined questions, contradictions, and new prompts. Missing: user answers some questions and asks clarifying counter-questions about others.
- **Root cause:** Edge case not anticipated during initial design.
- **Fix:** Added "Counter-Questions" section: answer the user's questions directly (not an SOP invocation), re-ask unanswered Phase 2 questions, counts toward round 2.
- **Risk if unaddressed:** Agent treats counter-questions as partial answers (filling in defaults) or as new prompts (abandoning the flow).

### FIX-A6: No question quality guidance in Phase 2

- **File:** CLAUDE.md L93 (now L93-94)
- **Issue:** Phase 2 says "ask targeted questions" but "targeted" is the only quality descriptor. No guidance distinguishing good clarifying questions from bad ones.
- **Root cause:** Quantity limits (3 per round, 2 rounds) were specified without corresponding quality criteria.
- **Fix:** Added: "prefer questions that offer 2-3 concrete options over open-ended questions (e.g., 'Should this handle X, Y, or both?' over 'What should this handle?'). Open-ended questions are appropriate only when the option space is genuinely unpredictable."
- **Risk if unaddressed:** Agent asks vague open-ended questions that produce low-quality answers, requiring more rounds.

### FIX-B2: Few-shot example ordering biases against Vocabulary inclusion

- **File:** references/prompt-optimization-example.md (full reorder), CLAUDE.md L165 (Known Limitation updated)
- **Issue:** Examples 1 and 2 omitted Vocabulary; Example 3 included it. The 2:1 frequency ratio biased the model toward omission. Note: the initial reviewer claimed Law 5 positional disadvantage, but the verification found this was misapplied — Example 3 was already in recency (strong) position. The real driver is frequency, not position.
- **Root cause:** Examples were ordered by complexity (low → medium → high) rather than by the behavioral signal they send.
- **Fix:** Moved clinical biostatistics example (Vocabulary included) to Example 1 (primacy position). CSV script moved to Example 2, PR review to Example 3. Updated Known Limitation L165 to reflect the mitigation.
- **Risk if unaddressed:** Model defaults to omitting Vocabulary even on domain-heavy prompts where it adds value.

### FIX-C1: Source+gap annotations vulnerable to rationalization

- **File:** CLAUDE.md L109 (now L110)
- **Issue:** Step 6 verification checks that Source and Gap annotations exist but doesn't verify the Source actually appears in the user's input. The model can fabricate plausible-sounding source references during the same forward pass.
- **Root cause:** Self-evaluation limitation — the model generates and evaluates annotations in the same pass, creating a faithfulness gap.
- **Fix:** Added verbatim-match requirement: "For each Source annotation, verify the cited phrase appears verbatim (or near-verbatim for conversational Phase 2 answers) in the original prompt or a Phase 2 answer — if the exact phrase cannot be located, the source is fabricated and the section must be deleted."
- **Risk if unaddressed:** Agent produces sections with fabricated justifications that pass the source+gap check.

### FIX-C2: Edit loop counting degrades in multi-turn conversations

- **File:** CLAUDE.md L121 (now L122)
- **Issue:** "IF 3+ edit rounds" requires the model to track turn count accurately. Models lose count in multi-turn conversations — no reliable internal counter.
- **Root cause:** Implicit state tracking (counting turns without recording the count).
- **Fix:** Added explicit externalization: "state the edit round count in your response (e.g., 'This is edit round 2')". The count becomes part of the conversation text, which the model can read back.
- **Risk if unaddressed:** Agent fails to trigger the escalation question at round 3, leading to unbounded edit loops.

### FIX-C3: Coding-adjacent prompts highest-risk for misrouting

- **File:** CLAUDE.md L13
- **Issue:** Prompts like "Write a script that..." compete with Claude Code's system-level instructions. DBG-001 showed this exact failure. The multi-layered defense reduces but doesn't eliminate the risk.
- **Root cause:** Claude Code's system instructions have architectural priority over CLAUDE.md and pull toward code execution on coding-adjacent input.
- **Fix:** Added internal self-narration anchor to Input Router: "Coding-adjacent prompts ('Write a script that...', 'Build an API that...', 'Create a function that...') are the highest-risk category for misrouting — internally confirm 'processing as a prompt to optimize' before entering the SOP."
- **Risk if unaddressed:** ~10-15% misrouting rate on coding-adjacent prompts (estimated, based on DBG-001 mechanism).

### FIX-C4: Vocabulary section inclusion needs explicit decision test

- **File:** CLAUDE.md L48
- **Issue:** No concrete criterion for when to include Vocabulary. The model relied on pattern-matching from examples (which biased toward omission — see FIX-B2).
- **Root cause:** Omission was specified (AP4 anti-pattern) but inclusion criteria were not.
- **Fix:** Added decision test: "Include Vocabulary when the prompt's domain has terms with specialized meanings that differ from common usage — the test: would a non-specialist misinterpret any key term? If yes, include Vocabulary."
- **Risk if unaddressed:** Agent applies a vague heuristic for Vocabulary inclusion, producing inconsistent behavior across domains.

### FIX-M1: Step 5d newness requirement contradicts no-invention invariant

- **File:** CLAUDE.md L105 (now L106)
- **Issue:** Original L105 said "ensure at least one element is new (not rephrased from original)." L152 says "Never add domain content the user didn't provide or imply." If a user's original prompt has a strong section needing no additions, the model faces a double bind: include it (must add something new → but can't invent) or omit it (loses valid content).
- **Root cause:** The anti-parroting intent (AP4) was expressed as a "newness" requirement without acknowledging that some sections may be structurally valuable without new content.
- **Fix:** Revised to: "ensure the section adds structural or organizational value — either new content from Phase 2, clearer scoping of the user's original intent, or explicit constraints the original implied but didn't state. Merely reformatting the user's exact words into the section template without structural improvement warrants omission (AP4), but the user's valid content should not be dropped solely because nothing new can be added."
- **Risk if unaddressed:** Agent drops valid user content to satisfy the newness requirement, or fabricates additions to justify including sections it shouldn't pad.

### FIX-M2: No fallback for few-shot example unavailability

- **File:** CLAUDE.md L63
- **Issue:** L63 specifies loading prompt-optimization-example.md but has no fallback if the file can't be loaded. Other reference file instructions (L94 for phase2-conditionals.md, L135 for anti-patterns-full.md) both have explicit fallbacks.
- **Root cause:** Inconsistency in fallback coverage across reference file loading instructions.
- **Fix:** Added: "If unavailable, rely on the Deliverables schema above for format guidance without example anchoring."
- **Risk if unaddressed:** Agent has no format guidance if the example file is missing, producing unpredictable output structure.

---

## Not Applied (already resolved by user)

- **A5:** README "testing has not yet begun" — user updated README before this fix pass.
- **M3:** README "34 test cases" count — user updated README before this fix pass.

---

## Regression Testing Required

These fixes modify CLAUDE.md instruction text and reference files. Per regression-suite.md, the full 10-test core regression suite should be run, plus conditional tests for:
- Phase 2 changes (A4, A6): T-07, T-08, T-09, T-31, T-32
- Deliverable changes (B2, C1, C4, M1): T-17, T-18, T-22, T-23, T-24, T-25
- Interaction model changes (A2, A3, C2): T-26, T-27, T-28
- Input Router changes (C3): T-36, T-39, T-40, T-41

Status: PENDING — fixes just applied, needs testing.
