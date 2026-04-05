# Category: Edge Cases

**Spec lines under test:** CLAUDE.md L68-72 (triage edge cases), L76 (phase2-conditionals reference), references/phase2-conditionals.md (full conditionals)
**Purpose:** Test boundary conditions and unusual user behaviors.

---

### T-29: Meta-prompt — question about prompting

- **Input:** `How would I write a prompt that gets an LLM to generate good unit tests?`
- **Expected:** Agent responds as a question, NOT by invoking the full SOP. Per L71: "Input is not a prompt (question, feedback, code): respond normally without invoking this SOP."
- **Pass Criteria:**
  - [ ] Agent answers the question conversationally
  - [ ] No Phase 1 triage classification
  - [ ] No "This looks like a [complexity] prompt" statement
  - [ ] Helpful answer about prompt-writing techniques
- **Failure Signals:** Agent treats this as a prompt to optimize and enters the SOP. Agent classifies "How would I write..." as a task.
- **Note:** This is a known gap (identified in verification, not yet addressed in spec). If the agent handles it correctly, the existing edge case at L71 is sufficient. If it fails, consider adding a specific edge case: "If input is a question about prompt design, answer the question without invoking the full SOP."

---

### T-30: Multiple prompts in one message

- **Input:** Submit in a single message:
```
I need help with three prompts:
1. A prompt for summarizing meeting notes
2. A prompt for generating API documentation from code
3. A prompt for writing user onboarding emails
```
- **Expected:** Agent acknowledges all three, processes sequentially. Per L70: "acknowledge all, then process each sequentially through the full SOP. Signal transitions between prompts explicitly."
- **Pass Criteria:**
  - [ ] Agent acknowledges receiving 3 prompts
  - [ ] Processes prompt 1 first (full SOP cycle through Phase 4)
  - [ ] Clear transition signal before processing prompt 2
  - [ ] Clear transition signal before processing prompt 3
  - [ ] Each prompt gets independent triage (no shared classification)
- **Failure Signals:** Agent combines all three into one restructure. Agent processes only the first and ignores the others. Agent asks to confirm before processing (appropriate only if >3 prompts per L70 — here exactly 3, so should proceed without confirmation).

---

### T-31: Partial answers to Phase 2 questions

- **Prerequisite:** Run a high-complexity prompt through Phase 1 to reach Phase 2. Note the questions asked.
- **Input:** Answer 2 of 3 questions. Leave one explicitly blank or skip it.
- **Expected behavior per references/phase2-conditionals.md:**
  - If the skipped question is critical and this is round 1: agent re-asks it (counts toward round 2)
  - If non-critical: agent proceeds with reasonable defaults for the unanswered question, documented in changelog
- **Pass Criteria:**
  - [ ] Agent acknowledges which questions were answered and which were skipped
  - [ ] Agent either re-asks the critical skipped question OR proceeds with defaults
  - [ ] If proceeding with defaults: changelog documents the assumption
  - [ ] Agent does NOT re-ask questions that were already answered
- **Failure Signals:** Agent re-asks ALL questions (ignoring provided answers). Agent proceeds without acknowledging the skipped question. Changelog doesn't document the assumption for the skipped answer.

---

### T-32: Contradictory Phase 2 answers

- **Prerequisite:** Run a high-complexity prompt through Phase 1 to reach Phase 2.
- **Input:** Answer questions with contradictory information. Example: if asked about target audience, say "both technical engineers AND non-technical executives" and if asked about detail level, say "very detailed with code examples."
- **Expected:** Agent surfaces the contradiction per Decision Authority escalation (CLAUDE.md L49) and phase2-conditionals.md § Contradictions. Does NOT resolve it.
- **Pass Criteria:**
  - [ ] Agent identifies the specific contradiction
  - [ ] Agent presents the tension to the user (e.g., "You mentioned both technical and non-technical audiences, but also detailed code examples — these pull in different directions")
  - [ ] Agent does NOT pick a side or resolve the contradiction
  - [ ] Agent asks the user to clarify the contradiction before proceeding
- **Failure Signals:** Agent silently picks one side. Agent tries to accommodate both without flagging the tension. Agent ignores the contradiction entirely.

---

### T-33: "Just do it" — user declines elicitation

- **Prerequisite:** Run a high-complexity prompt through Phase 1 to reach Phase 2.
- **Input:** Instead of answering questions, respond with: `Just do it` or `skip the questions and restructure`
- **Expected:** Agent proceeds to Phase 3 with reasonable defaults. Per phase2-conditionals.md § Partial or Declined Answers: "proceed to Phase 3 using reasonable defaults and document each assumption in the changelog."
- **Pass Criteria:**
  - [ ] Agent proceeds to Phase 3 without further questions
  - [ ] Restructured prompt uses reasonable defaults for unanswered dimensions
  - [ ] Changelog documents EACH assumption with a specific row
  - [ ] Changelog "Why Added" entries explain the default choice and the risk of the assumption
  - [ ] Phase 4 dialogue follows normally
- **Failure Signals:** Agent asks questions again despite "just do it." Agent proceeds but doesn't document assumptions. Changelog has a single "user declined questions" row instead of per-assumption rows.

---

### T-34: New prompt instead of Phase 2 answers

- **Prerequisite:** Run a high-complexity prompt through Phase 1 to reach Phase 2.
- **Input:** Instead of answering questions, submit a completely new prompt: `Write a prompt for generating cooking recipes from ingredient lists`
- **Expected:** Agent treats as fresh Phase 1 input and abandons the pending flow. Per phase2-conditionals.md § New Prompt Submission: "treat as fresh Phase 1 input and abandon the pending flow. If ambiguous, ask before proceeding."
- **Pass Criteria:**
  - [ ] Agent recognizes this as a new prompt, not an answer to Phase 2 questions
  - [ ] Agent abandons the previous prompt's Phase 2 flow
  - [ ] Agent begins fresh Phase 1 triage on "cooking recipes from ingredient lists"
  - [ ] No vocabulary or context from the previous prompt contaminates the new one
- **Failure Signals:** Agent treats the new prompt as an answer to Phase 2 questions. Agent tries to merge the two prompts. Agent continues the old flow and ignores the new prompt.
- **Ambiguity test variant:** If time permits, also test with an ambiguous input that could be an answer OR a new prompt. Expected: agent asks "Is this a new prompt, or are you answering my earlier questions?"
