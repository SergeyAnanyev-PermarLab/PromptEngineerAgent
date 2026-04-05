# Debug Log

Record root-cause investigations for test deviations. Each entry follows the 5-step debug protocol.

---

## Debug Protocol (reference)

```
Step 1 — Classify the deviation
  → Wrong phase? (triage failure)
  → Right phase, wrong behavior? (instruction failure)
  → Right behavior, wrong output format? (deliverable failure)

Step 2 — Locate the governing instruction
  → Which line(s) in CLAUDE.md should have prevented this?
  → Is the instruction in the attention trough (L54-94)?
  → Is it in a reference file that may not have loaded?

Step 3 — Diagnose the mechanism
  → Competing instructions? (Two rules pull in opposite directions)
  ��� Attention position? (Instruction in middle of file)
  → Self-evaluation dependency? (Instruction requires introspection)
  → Missing trigger? (Instruction exists but nothing activates it)
  → Few-shot override? (Example pattern dominates instruction)

Step 4 — Classify the fix type
  → Reinforcement: add another mention at a high-attention position
  → Reframe: change the mechanism (e.g., post-hoc → generation-time)
  → Extract: move to reference file to reduce trough content
  → Example: add/modify few-shot to demonstrate correct behavior
  → Threshold: adjust a numeric parameter (questions per round, etc.)

Step 5 — Apply fix, re-run the specific test, then run regression suite
```

---

## Investigations

### DBG-001: Agent does not enter SOP — treats all input as code tasks

- **Date:** 2026-04-05
- **Source:** Run run-2026-04-05-01, Test T-01
- **Deviation:** Agent was expected to triage "Fix the typo in line 3" as a low-complexity prompt and bypass to Phase 4. Instead, the agent interpreted it as a literal instruction to fix a typo in the codebase. It read a file, looked for a typo, and reported it couldn't find one. The SOP was never entered.
- **Severity:** Critical — this blocks ALL 34 tests. If the SOP never activates, nothing else in the spec matters.

**Step 1 — Classification:**
Wrong phase — the agent never entered any phase. It operated in Claude Code's default software-engineering-assistant mode. This is not a triage failure (wrong phase selected) — it's a pre-triage failure (triage never invoked).

**Step 2 — Governing instruction:**
- Spec line(s): CLAUDE.md L1-2 (Role Framing) assigns the Requirements Analyst role. L52-56 (Phase 1 Triage) defines the SOP entry point. L68-72 (edge cases) handles non-prompt input.
- Attention position: Role Framing is in the primacy zone (L1-2). Phase 1 is in the trough (L52-56).
- In reference file: No — all inline.

**Step 3 — Mechanism diagnosis:**
**Competing instructions.** Claude Code's system-level instructions tell the model to act as a software engineering assistant. CLAUDE.md's Role Framing (L1-2) says "You are a Requirements Analyst specializing in LLM prompt specification." These are contradictory identities. Claude Code's instructions are injected at the system prompt level BEFORE CLAUDE.md is loaded, giving them structural priority.

The CLAUDE.md has no mechanism to distinguish "this is a prompt the user wants me to optimize" from "this is a task the user wants me to execute." The SOP assumes all user messages are prompts to be triaged, but Claude Code's default assumption is the opposite — all user messages are tasks to be executed.

The edge case at L71 ("Input is not a prompt: respond normally") actually reinforces the problem — it gives the agent an explicit path to bypass the SOP for non-prompt input, but there's no corresponding signal that ACTIVATES the SOP for prompt input.

**Step 4 — Fix type:**
**Reframe.** The fix is not reinforcement (repeating the role more) — it's adding an activation mechanism that the spec currently lacks. Options:

A. **Explicit prefix convention:** User prefixes prompts with a signal like `/optimize` or wraps them in a code block or quotes. The SOP activates only when this signal is present. Pros: unambiguous. Cons: adds friction, user must remember.

B. **Default-to-SOP:** Add an instruction that ALL user messages should be treated as prompts to optimize unless they are clearly meta-instructions (questions about the agent, requests to modify files, etc.). This inverts the current default. Pros: no user friction. Cons: may conflict with Claude Code's system instructions.

C. **Greeting/handshake:** When the conversation starts, the agent states its role and asks "What prompt would you like me to help with?" This primes the interaction model. Pros: sets expectations. Cons: doesn't prevent mid-conversation confusion.

D. **Combination of B + C:** Default to SOP mode AND open with a handshake. The greeting establishes context; the default ensures subsequent messages are also routed correctly.

**Recommended: Option A (explicit prefix) as the immediate fix for testability, with Option D as the production fix.** Option A unblocks testing immediately. Option D is the right long-term answer but needs its own testing.

**Step 5 — Fix applied:**
- Change: Implemented Option D (default-to-SOP + greeting/handshake) plus Environment Override and Input Router with exclusion categories and disambiguation mechanism.
- Spec line(s) modified: CLAUDE.md — added Environment Override (L1-3), Greeting Protocol (L8-9), Input Router (L11-25), updated triage edge case (L89), added Invariant reinforcement (L156), added Known Limitations for over/under-activation.
- New test cases: T-35 through T-41 (tests/test-cases/T35-T41-activation.md)
- Regression suite expanded: 8 → 10 core tests (T-36 and T-39 added at priorities 1 and 2)
- Regression tests run: PENDING — fix just applied, needs testing
- Regression results: PENDING

**Status:** Fixed — awaiting regression test confirmation

---

### DBG-002: Disambiguation fires on unambiguous prompt — over-disambiguation

- **Date:** 2026-04-05
- **Source:** Run run-2026-04-05-02, Test T-36
- **Deviation:** Agent was expected to enter the SOP and produce a triage classification for "Write a Python script that parses JSON and extracts email addresses." Instead, it asked: "Should I optimize this as a prompt, or are you asking me to actually write the Python script?" The disambiguation mechanism fired on an input that is unambiguously a prompt to optimize in this agent's context.
- **Severity:** Major — the agent adds a confirmation step to every coding-style prompt, defeating the purpose of default-to-SOP.

**Step 1 — Classification:**
Right phase (pre-triage, Input Router), wrong behavior (disambiguation instead of SOP entry). The Input Router's "when ambiguous, ask" instruction is too sensitive.

**Step 2 — Governing instruction:**
- Spec line(s): CLAUDE.md L20 ("When ambiguous: Ask before assuming.")
- Attention position: L20 is in the primacy zone (Input Router section, near top of file)
- In reference file: No — inline in CLAUDE.md
- Note: The "when ambiguous, ask" instruction is COMPETING with the default-to-SOP instruction at L13 ("Treat every user message as a prompt to be processed through the SOP"). Both are in the primacy zone. The disambiguation instruction is winning because it's more specific and more cautious.

**Step 3 — Mechanism diagnosis:**
**Instruction conflict within the Input Router.** The router says two things:
1. L13: "Treat every user message as a prompt to be processed through the SOP" (default action)
2. L20: "When ambiguous, ask" (disambiguation action)

The problem: the agent interprets ANY prompt that could also be a coding task as "ambiguous." "Write a Python script..." matches both "prompt to optimize" AND Claude Code's default "task to execute." From the agent's perspective, this IS ambiguous — it can see both interpretations. But from the USER's perspective in this agent's context, it's not ambiguous at all — they're talking to a prompt optimizer, so of course it's a prompt to optimize.

The fix needs to shift the ambiguity threshold: disambiguation should only fire when the input looks like it's directed at THIS AGENT'S capabilities (file editing, spec questions) rather than at the downstream task the prompt describes. "Write a Python script" describes a DOWNSTREAM task — it's what the optimized prompt will ask an LLM to do. "Fix line 5 in CLAUDE.md" describes a task for THIS AGENT.

**Step 4 — Fix type:**
**Reframe.** Change the disambiguation trigger from "could this be a task?" (too broad — any prompt describing a coding task triggers it) to "is this directed at me specifically?" (narrower — only inputs that reference this project, this agent, or this conversation trigger disambiguation).

Concrete fix: Replace the "when ambiguous" instruction with a more specific trigger:
- If the input references files in THIS project, agent capabilities, or the current conversation → may be a meta-instruction, ask to disambiguate
- If the input describes a DOWNSTREAM task (something an LLM would do) → it's a prompt to optimize, enter SOP
- The test: does the input make sense as instructions TO another LLM? If yes → it's a prompt. If it only makes sense as instructions to THIS agent → it's a meta-instruction.

**Step 5 — Fix applied:**
- Change: Replaced "When ambiguous: Ask before assuming" (open-ended, too sensitive) with "Disambiguation rule: Only ask when input partially matches an exclusion category" (closed-form check against defined categories). Updated Invariant end-position reinforcement to match. Fix validated by sanity-checking agent before application — agent approved with modifications (lead with concrete signals, drop abstract "instructions to another LLM" test). Both modifications incorporated.
- Spec line(s) modified: CLAUDE.md L20-22 (Input Router disambiguation), L156 (Invariant reinforcement)
- Regression tests to run: T-35, T-36, T-37, T-38, T-39, T-40
- Regression results: PENDING

**Status:** Fixed — awaiting regression test confirmation

---

<!-- Copy the template below for each new investigation -->

<!--
### DBG-NNN: [short description]

- **Date:** YYYY-MM-DD
- **Source:** Run [run-ID], Test [T-XX]
- **Deviation:** [what happened vs. what was expected]
- **Severity:** Critical | Major | Minor

**Step 1 — Classification:**
[Wrong phase | wrong behavior | wrong format]

**Step 2 — Governing instruction:**
- Spec line(s): CLAUDE.md L[NN]
- Attention position: [primacy zone | middle/trough | end/recency zone]
- In reference file: [Yes — which file | No — inline in CLAUDE.md]

**Step 3 — Mechanism diagnosis:**
[Which mechanism failed and why]

**Step 4 — Fix type:**
[Reinforcement | Reframe | Extract | Example | Threshold]

**Step 5 — Fix applied:**
- Change: [what was changed in the spec]
- Spec line(s) modified: CLAUDE.md L[NN]
- Regression tests run: [list test IDs]
- Regression results: [all pass | which failed]

**Status:** Open | Fixed | Wont-Fix | Duplicate of DBG-NNN
-->
