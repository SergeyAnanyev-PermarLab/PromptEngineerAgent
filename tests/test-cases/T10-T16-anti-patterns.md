# Category: Anti-Pattern Probes

**Spec lines under test:** CLAUDE.md L119-127 (compact table), references/anti-patterns-full.md (full detail)
**Purpose:** Each test is designed to trigger a specific anti-pattern. Pass = the agent resists the trigger.

---

### T-10: AP1 Over-questioning — low complexity prompt

- **Anti-Pattern:** AP1 (Over-questioning)
- **Detection trigger (L121):** Asking 5+ questions on a low-complexity prompt
- **Input:** `Convert this CSV to JSON`
- **Expected:** Low complexity → Phase 4 bypass. Zero clarifying questions. Brief assessment.
- **Pass Criteria:**
  - [ ] No clarifying questions asked
  - [ ] Classified as Low
  - [ ] Bypass to Phase 4
- **Failure Signals:** Agent asks ANY clarifying questions (e.g., "What CSV library?", "What JSON structure?"). These are reasonable questions but the spec says Low complexity should bypass with defaults.

---

### T-11: AP2 Scope Inflation — narrow task

- **Anti-Pattern:** AP2 (Scope Inflation)
- **Detection trigger (L122):** Adding dimensions, constraints, or acceptance criteria the user never implied
- **Input:** `Write a prompt that summarizes long emails into 3 bullet points`
- **Expected:** Restructure stays within email summarization scope. No added features.
- **Pass Criteria:**
  - [ ] No constraints about handling attachments, multiple languages, or email threads (unless user implied)
  - [ ] No acceptance criteria about tone analysis, sentiment, or categorization
  - [ ] Every section in the restructure traces to "summarizes long emails into 3 bullet points"
  - [ ] Changelog "Why Added" column cites specific gaps, not "to improve robustness"
- **Failure Signals:** Restructure includes "handle attachments," "support multiple languages," "detect urgency," or any capability beyond summarization into 3 bullets. Changelog entries with vague justifications.

---

### T-12: AP3 Restructure When Unnecessary — already-good prompt

- **Anti-Pattern:** AP3 (Restructure When Unnecessary)
- **Detection trigger (L123):** Rewriting a prompt that already meets bypass threshold
- **Input:**
```
## Context
You are a code reviewer for a Python web application.

## Task
Review pull requests for security vulnerabilities. For each finding, cite the OWASP category, affected line, and remediation.

## Constraints
Only flag OWASP Top 10 issues. Do not comment on style, performance, or architecture.

## Acceptance Criteria
Each finding includes: OWASP category, file and line number, severity (critical/high/medium/low), and a one-sentence remediation.
A PR with no issues produces a single "No OWASP Top 10 issues detected" comment.
```
- **Expected:** Phase 4 bypass. Agent recognizes this prompt already has Context + Task + Constraints + AC. Brief assessment of what's strong. "Want me to restructure this, or is it ready to use as-is?"
- **Pass Criteria:**
  - [ ] Classified as Medium or High — either way, bypass threshold is met
  - [ ] Phase 4 bypass with brief positive assessment
  - [ ] No unsolicited restructure
  - [ ] Agent names specific strengths, not just "looks good"
- **Failure Signals:** Agent restructures without being asked. Agent enters Phase 2 and asks questions about an already-complete prompt. Agent offers only vague praise ("this is well-structured") without specifics.

---

### T-13: AP4 Parrot Restructuring — test for new analysis

- **Anti-Pattern:** AP4 (Parrot Restructuring)
- **Detection trigger (L124):** Mechanically filling all 5 sections with rephrased user words
- **Input:** `Write a prompt for generating unit tests for React components`
- **Expected:** If restructured (via Phase 2→3 or user request from Phase 4), each included section must contain at least one element NOT in the original. Sections that would only contain rephrased original content are omitted.
- **Pass Criteria:**
  - [ ] Not all 5 sections are present (Vocabulary should be omitted for standard React terms)
  - [ ] Each present section adds something new (e.g., Constraints might add "test behavior not implementation" — absent from original)
  - [ ] Changelog explains what each section adds beyond the original
- **Failure Signals:** All 5 sections present with only rephrased versions of "generating unit tests for React components." Vocabulary section filled with standard terms (React, Jest, component, render). Changelog entries with "reformatted for clarity" rather than specific gaps.

---

### T-14: AP5 Domain Overreach — unfamiliar domain

- **Anti-Pattern:** AP5 (Domain Overreach)
- **Detection trigger (L125):** Making domain-specific decisions that belong to the user
- **Input:** `Write a prompt for diagnosing car engine problems from symptom descriptions`
- **Expected:** Agent structures the prompt but does NOT fill in specific diagnostic procedures, engine components to check, or troubleshooting sequences. Domain decisions are marked with placeholders.
- **Pass Criteria:**
  - [ ] No invented diagnostic steps (e.g., "check catalytic converter first")
  - [ ] Domain-specific decisions use [USER: specify X] placeholders
  - [ ] Structure is provided (Context, Task, Constraints framework) without domain content
  - [ ] Phase 2 questions ask the USER about domain specifics rather than assuming them
- **Failure Signals:** Agent fills in specific engine diagnostic procedures. Agent assumes OBD-II codes, specific engine types, or troubleshooting order. Changelog doesn't flag domain decisions as user-owned.

---

### T-15: AP6 Under-constraining — explicit constraints present

- **Anti-Pattern:** AP6 (Under-constraining)
- **Detection trigger (L126):** Omitting constraints the user's words directly imply
- **Input:** `Write a prompt that generates SQL queries from natural language, but it should never drop tables or modify schema`
- **Expected:** The "no DROP/ALTER" constraint MUST appear in the restructured prompt — it's explicitly stated.
- **Pass Criteria:**
  - [ ] "No DROP" constraint preserved in Constraints section
  - [ ] "No schema modification" constraint preserved in Constraints section
  - [ ] Constraints are not weakened (e.g., "avoid" instead of "never")
  - [ ] If additional implied constraints are uncertain, they use [USER: confirm?]
- **Failure Signals:** The explicit constraints are omitted or softened. The agent adds so many other constraints that the explicit ones get buried. The agent rephrases "never" as "try to avoid."

---

### T-16: AP7 Phase 2 Bleed-Through — duplicate of T-07 focus

- **Anti-Pattern:** AP7 (Phase 2 Bleed-Through)
- **Detection trigger (L127):** Pre-generation gate check
- **Input:** Same as T-04 (high-complexity system design prompt)
- **Expected:** Same as T-07 — Phase 2 questions only, no restructured content
- **Pass Criteria:** Same as T-07 Phase 2 full stop checklist
- **Overlap note:** This deliberately overlaps T-07. The difference is observational focus: T-07 checks the full stop mechanism; T-16 specifically looks for AP7 trigger patterns (## headings after questions, structural previews, "I'll likely include" language).
- **Additional checks:**
  - [ ] No sentence describes what the restructured prompt "will" or "might" contain
  - [ ] No bullet list previewing planned sections
  - [ ] No conditional language about restructure content ("depending on your answers, I might...")
