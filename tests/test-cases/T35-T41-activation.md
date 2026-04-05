# Category: Activation Signal and Input Router

**Spec lines under test:** CLAUDE.md L1-3 (Environment Override), L8-9 (Greeting Protocol), L11-25 (Input Router), L156 (Invariant reinforcement)
**Introduced by:** Fix for DBG-001 (agent did not enter SOP — treated all input as code tasks)
**Purpose:** Verify the activation mechanism works correctly — the agent enters SOP for prompts, stays out for meta-instructions, and disambiguates when uncertain.

**Risk model:** This fix introduces a new failure axis. The old bug was under-activation (SOP never fires). The fix could cause over-activation (SOP fires on everything, including legitimate meta-instructions). These tests probe both directions.

---

### T-35: Greeting on first user message

- **Input:** Start a fresh conversation in the project directory. Type `hello` or `hi`.
- **Expected:** Agent introduces itself in ~1 sentence and asks the user for a prompt to optimize. Does NOT invoke the SOP on a bare greeting.
- **Pass Criteria:**
  - [ ] Agent introduces itself as a prompt optimization agent (or similar)
  - [ ] Agent asks the user for a prompt to optimize
  - [ ] Introduction is 1-3 sentences (not a wall of text)
  - [ ] No SOP Phase 1 triage triggered on "hello"
  - [ ] No file reading or code analysis
- **Failure Signals:** Agent enters the SOP and tries to classify "hello" as a prompt. Agent reads CLAUDE.md and summarizes it. Agent produces a multi-paragraph explanation of its capabilities. Agent does something other than introduce itself and ask for a prompt.
- **What this tests:** Greeting Protocol (L9-10). Claude Code cannot speak first — the greeting triggers on the first user message. A bare greeting with no prompt content should produce an introduction + ask, not SOP entry.
- **Design note:** Original T-35 expected the agent to speak unprompted on conversation start. Claude Code only responds to user input — the agent cannot initiate. Revised after run-2026-04-05-01 observation.

---

### T-36: Default-to-SOP for unambiguous prompt input

- **Input:** `Write a Python script that parses JSON and extracts email addresses`
- **Expected:** Agent enters the SOP (Phase 1 triage). Does NOT write a Python script.
- **Pass Criteria:**
  - [ ] Agent produces a complexity classification ("This looks like a [low/medium] complexity prompt")
  - [ ] Agent enters Phase 1 triage, not code-writing mode
  - [ ] No Python code generated
  - [ ] No file reading or creation
  - [ ] SOP proceeds normally (bypass to Phase 4, or Phase 2 if classified High)
- **Failure Signals:** Agent writes a Python script. Agent reads project files looking for JSON. Agent asks "do you want me to write this script or optimize this as a prompt?" (disambiguation should NOT fire here — this is unambiguously a prompt to optimize in this agent's context).
- **What this tests:** Input Router default-to-SOP behavior. This is the exact scenario that DBG-001 would have failed on.
- **Note:** If this is the first message in the conversation, expect a one-sentence introduction prefix before the triage classification (per Greeting Protocol). This is correct behavior — the greeting and SOP entry happen in the same response.

---

### T-37: Exclusion category 1 — question about the agent

- **Input:** `What anti-patterns do you check for?`
- **Expected:** Agent answers the question directly by describing its 7 anti-patterns. Does NOT enter the SOP to optimize this as a prompt.
- **Pass Criteria:**
  - [ ] Agent describes its anti-patterns (AP1 through AP7)
  - [ ] No Phase 1 triage classification
  - [ ] No "This looks like a [complexity] prompt" statement
  - [ ] No Phase 2 questions
  - [ ] Response is a direct answer, not a restructured prompt
- **Failure Signals:** Agent enters the SOP and tries to optimize "What anti-patterns do you check for?" as a prompt. Agent produces a triage classification.
- **What this tests:** Input Router exclusion category 1 (questions about this agent). Over-activation detection.

---

### T-38: Exclusion category 2 — request to modify project files

- **Input:** `Add a new test case to T01-T06-classification.md for testing prompts in other languages`
- **Expected:** Agent modifies the test case file. Does NOT enter the SOP to optimize this as a prompt.
- **Pass Criteria:**
  - [ ] Agent reads the test case file
  - [ ] Agent adds or proposes a new test case
  - [ ] No Phase 1 triage classification
  - [ ] No "This looks like a [complexity] prompt" statement
  - [ ] Agent operates in normal Claude Code file-editing mode
- **Failure Signals:** Agent enters the SOP and tries to optimize "Add a new test case..." as a prompt. Agent produces a triage classification.
- **What this tests:** Input Router exclusion category 2 (file modification requests). Over-activation detection.

---

### T-39: Disambiguation fires on genuinely ambiguous input

- **Input:** `Fix the typo in line 3`
- **Expected:** Agent recognizes this is ambiguous — it could be a literal instruction (fix something) or a prompt to optimize. Agent asks for clarification. Does NOT guess either way.
- **Pass Criteria:**
  - [ ] Agent asks a disambiguation question (e.g., "Should I optimize this as a prompt, or are you asking me to fix a typo in a file?")
  - [ ] Agent does NOT enter the SOP without asking
  - [ ] Agent does NOT start reading files without asking
  - [ ] The question is concise (1-2 sentences)
- **Failure Signals:** Agent enters the SOP without asking (over-activation). Agent reads files looking for a typo without asking (under-activation, repeat of DBG-001). Agent picks a side without asking.
- **What this tests:** Input Router disambiguation mechanism ("When ambiguous, ask"). This is the exact input that triggered DBG-001.
- **Calibration note:** This tests the BOUNDARY of the router. If the agent reliably disambiguates here, the router is working. If it guesses, the exclusion categories may need tuning.

---

### T-40: Re-run T-01 after fix — low complexity bypass

- **Input:** After disambiguating in T-39 (or in a fresh conversation), submit: `Fix the typo in line 3` and when asked, confirm "optimize this as a prompt."
- **Expected:** Agent enters the SOP. Classifies as Low complexity. Bypasses to Phase 4.
- **Pass Criteria:**
  - [ ] Agent enters Phase 1 triage
  - [ ] Classified as Low complexity
  - [ ] Bypass to Phase 4
  - [ ] Brief assessment with "Want me to restructure this, or is it ready to use as-is?"
  - [ ] No file reading, no code execution
- **Failure Signals:** Agent writes code. Agent reads files. Agent enters Phase 2 (over-classification).
- **What this tests:** This is T-01 re-run after the DBG-001 fix. If this passes, the activation fix resolved the original Critical deviation.

---

### T-41: Mid-conversation mode switching

- **Procedure:**
  1. Submit a prompt: `Write a prompt that summarizes research papers into bullet points`
  2. Complete the SOP cycle (Phase 1 through Phase 4)
  3. Submit a question: `How many anti-patterns do you check?`
  4. After the answer, submit another prompt: `Write a prompt for translating technical documentation`
- **Expected:**
  - Step 1-2: Agent enters SOP, processes the prompt normally
  - Step 3: Agent answers the question directly (exclusion category 1), does NOT enter SOP
  - Step 4: Agent enters SOP again for the new prompt (this is prompt 2 in the session — no prompt-N warning yet per L131)
- **Pass Criteria:**
  - [ ] Step 1-2: Normal SOP execution (triage → Phase 4 or Phase 2→3→4)
  - [ ] Step 3: Direct answer about anti-patterns, NO triage classification
  - [ ] Step 4: Fresh Phase 1 triage for the new prompt
  - [ ] No confusion between prompt mode and question mode
  - [ ] No cross-contamination between prompt 1 and prompt 2
- **Failure Signals:** Agent tries to optimize the question as a prompt (over-activation). Agent fails to re-enter SOP for the second prompt (under-activation after mode switch). Vocabulary from prompt 1 leaks into prompt 2.
- **What this tests:** The router's ability to switch between SOP mode and exclusion-category mode within a single conversation. This is the most complex activation scenario.

---

## Risk Accounting: What This Fix Might Introduce

| Risk | Description | Test That Catches It | Severity If Uncaught |
|---|---|---|---|
| **Over-activation on meta-instructions** | Agent routes legitimate questions or file-edit requests to the SOP | T-37 (question), T-38 (file edit) | Major — agent becomes unusable for anything except prompt optimization |
| **Under-activation persists** | Claude Code's system instructions still override the Environment Override | T-36 (unambiguous prompt), T-40 (T-01 re-run) | Critical — fix didn't work |
| **Disambiguation fatigue** | Agent asks "should I optimize this?" on every input, including obvious prompts | T-36 (should NOT disambiguate on clear prompts) | Major — UX degradation |
| **Greeting blocks quick starts** | Experienced users who know the agent must sit through a greeting every time | T-35 (observe greeting length) | Minor — friction, not breakage |
| **Mode switch failure** | Agent gets stuck in SOP mode after processing a prompt, can't answer questions | T-41 (mid-conversation switch) | Major — conversation becomes rigid |
| **Phase 2 answer misrouted** | User's answers to Phase 2 questions get interpreted as new prompts or meta-instructions | Existing T-09, T-31, T-34 (Phase 2 edge cases) | Major — SOP breaks mid-flow |
| **Greeting treated as SOP output** | The greeting itself confuses Phase 1 if the user's first message triggers triage | T-35 → T-36 sequence (greeting then prompt) | Minor — one-time confusion |
