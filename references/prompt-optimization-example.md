# Prompt Optimization — Format Example

This file anchors the output format for the Requirements Analyst agent. One complete input→output cycle.

---

## User Input

> Write a Python script that reads a CSV and finds duplicates

## Deliverable 1 — Optimized Prompt

### Context
You are working with tabular data in CSV format. The file may contain headers and mixed data types.

### Task
Write a Python script that:
- Reads a CSV file from a user-specified path
- Identifies duplicate rows
- Reports which rows are duplicates and what they duplicate

### Constraints
- Use only the Python standard library (csv module) unless the user specifies otherwise
- Handle the case where the file path is invalid or the file is not valid CSV

### Acceptance Criteria
- Script runs from the command line with a file path argument
- Output identifies duplicate rows by row number and shows the row they duplicate
- A file with no duplicates produces a "no duplicates found" message

*Vocabulary section omitted — no domain-specific terms beyond standard Python/CSV. Omitting is preferable to padding (Anti-Pattern 4).*

---

> Write a Python script that reads a CSV and finds duplicates

---

## Deliverable 2 — Changelog

| Change | Original | Why Added | Law/Principle Referenced |
|---|---|---|---|
| Added file path input method | absent | Without specifying how the script receives the CSV path, implementations will vary (hardcoded path vs. CLI arg vs. input()) — underspecification failure | Law 4: underspecification failure |
| Added stdlib constraint with opt-out | absent | Unconstrained, the model may pull in pandas or other dependencies — scoping the default prevents assumption mismatch | Law 6: constraint enumeration |
| Added duplicate reporting format | absent | "Finds duplicates" is ambiguous — report them? delete them? flag them? Explicit output expectation prevents output drift | Law 4: output drift |
| Added invalid-file handling | absent | Real-world CSV scripts fail on bad paths — omitting this produces a script that crashes on first error, which the user likely doesn't want [USER: confirm?] | Anti-Pattern 6: under-constraining check |
| Omitted Vocabulary section | absent | No specialized domain terms required — section would be padding | Anti-Pattern 4: Parrot Restructuring |

---

## Example 2: Medium-Complexity Prompt (Phase 2 → Phase 3)

This example demonstrates the full elicitation flow: Phase 1 triage → Phase 2 clarification → user answers → Phase 3 restructure with changelog tracing to both original words and Phase 2 answers.

### User Input

> Write a prompt for an LLM that reviews GitHub pull requests and comments on code quality issues

### Phase 1 — Triage

"This looks like a medium-complexity prompt — I see Context (GitHub PRs) and Task (review and comment) but no Constraints or Acceptance Criteria. 'Code quality issues' is ambiguous in scope."

Medium complexity requires Context + Task + 1 other to bypass. Only 2 of 5 dimensions present → proceed to Phase 2.

### Phase 2 — Clarify

1. What types of code quality issues should it focus on? (bugs, security vulnerabilities, performance, style — or all of these?)
2. Should it comment only on changed lines in the diff, or can it flag pre-existing issues in files touched by the PR?
3. Is there a target programming language or stack, or should it handle any language?

Waiting for your answers before I restructure.

### User Answers

1. Bugs and security only — style is handled by our linters, performance is out of scope
2. Changed lines only — pre-existing issues create too much noise
3. Primarily Python and TypeScript, but it should handle whatever's in the diff

### Deliverable 1 — Optimized Prompt

#### Context
You are reviewing GitHub pull requests via the PR review API. The codebase is primarily Python and TypeScript. Automated linters already enforce style rules.

#### Task
For each pull request:
- Review the diff (changed lines only)
- Identify bugs and security vulnerabilities
- Post inline review comments on the specific lines where issues are found
- Provide a summary comment with an overall assessment

#### Constraints
- Only comment on changed lines — do not flag pre-existing issues in touched files
- Do not flag style issues (handled by linters) or performance concerns (out of scope)
- If no issues are found, post a single approving summary rather than staying silent [USER: confirm?]

#### Acceptance Criteria
- Each inline comment identifies the issue type (bug or security), explains the risk, and suggests a fix
- Summary comment lists total issues found by type
- Zero false positives on linter-covered style rules

*Vocabulary section omitted — "pull request," "diff," "inline comment" are standard GitHub terms, not domain-specific vocabulary requiring routing (Anti-Pattern 4).*

---

> Write a prompt for an LLM that reviews GitHub pull requests and comments on code quality issues

---

### Deliverable 2 — Changelog

| Change | Original | Why Added | Law/Principle Referenced |
|---|---|---|---|
| Scoped to bugs and security only | "code quality issues" (ambiguous) | Phase 2 answer 1: user excluded style and performance — without this, the LLM flags linter-covered issues, creating noise | Law 4: underspecification failure |
| Restricted to changed lines only | absent | Phase 2 answer 2: pre-existing issues create noise — omitting this lets the LLM comment on untouched code the PR author can't act on | Anti-Pattern 6: under-constraining |
| Added language context (Python, TypeScript) | absent | Phase 2 answer 3: scopes the LLM's analysis patterns — without it, the model applies generic heuristics | Law 1: vocabulary routing |
| Added "no issues found" behavior | absent | Unspecified: should the LLM stay silent or approve? Silent approval is ambiguous to PR authors. Included with [USER: confirm?] since this is inferred, not stated | Anti-Pattern 6 / Law 4: output drift |
| Added inline comment format spec | absent | "Comments on code quality" doesn't specify what a comment contains — type, risk, and fix suggestion prevent vague annotations | Law 6: structured artifacts |
| Omitted Vocabulary section | absent | GitHub PR terminology is standard — section would be padding | Anti-Pattern 4: Parrot Restructuring |

---

## Example 3: Prompt with Vocabulary Section Included

This example demonstrates when the Vocabulary section adds value — the user's domain has specialized terms that require routing.

### User Input

> Build me a prompt that gets an LLM to review clinical trial protocols for common statistical design flaws

### Phase 1 — Triage

"This looks like a high-complexity prompt — I see Task (review protocols for flaws) but Context, Constraints, and Acceptance Criteria are absent. The domain (clinical biostatistics) has specialized vocabulary that affects output quality."

High complexity → proceed to Phase 2.

### Phase 2 — Clarify

1. What types of statistical design flaws are you most concerned about? (e.g., underpowered sample sizes, inappropriate endpoint selection, missing multiplicity adjustments)
2. What format are the protocols in — structured documents with specific sections, or free-form descriptions?
3. Should the review flag the flaw and explain it, or also suggest a correction?

Waiting for your answers before I restructure.

### User Answers

1. Mainly sample size justification gaps, multiplicity issues in multi-arm trials, and inappropriate primary endpoint choices
2. Structured — they follow ICH E6/E9 format with a Statistical Analysis Plan section
3. Flag and explain, but don't suggest corrections — our biostatisticians handle that

### Deliverable 1 — Optimized Prompt

#### Context
You are reviewing clinical trial protocols formatted per ICH E6/E9 guidelines. Each protocol includes a Statistical Analysis Plan (SAP) section. Your review is a first-pass screen — corrections are handled separately by biostatisticians.

#### Vocabulary
Use these terms with their precise clinical biostatistics meanings: sample size justification (not "how many patients"), multiplicity adjustment (Bonferroni, Holm, gatekeeping procedures), primary endpoint, multi-arm trial, ICH E9 estimand framework, interim analysis, alpha spending, Type I error rate control, Statistical Analysis Plan (SAP).

#### Task
For each protocol:
- Review the SAP section for statistical design flaws
- Focus on: (1) sample size justification gaps, (2) missing or inappropriate multiplicity adjustments in multi-arm designs, (3) inappropriate primary endpoint selection
- Flag each flaw with a clear explanation of why it is a flaw and what risk it introduces
- Do not suggest corrections

#### Constraints
- Only review the SAP and directly related sections (endpoints, sample size) — do not review operational or regulatory sections
- Do not suggest fixes or alternative designs — flag and explain only
- If a design choice is unconventional but defensible, note it as "atypical — verify intent" rather than flagging as a flaw [USER: confirm?]

#### Acceptance Criteria
- Each flagged flaw identifies: the specific section, the flaw type (sample size / multiplicity / endpoint), and a 1-2 sentence explanation of the risk
- A protocol with no detected flaws produces a "no statistical design flaws detected in SAP" summary
- Zero flags on standard, well-justified designs (e.g., a properly powered two-arm RCT with a single primary endpoint)

---

> Build me a prompt that gets an LLM to review clinical trial protocols for common statistical design flaws

---

### Deliverable 2 — Changelog

| Change | Original | Why Added | Law/Principle Referenced |
|---|---|---|---|
| Added domain vocabulary section | absent | Clinical biostatistics terms have precise meanings that differ from lay usage — "sample size justification" routes different knowledge than "how many patients." Without vocabulary routing, the LLM applies general statistical heuristics | Law 1: vocabulary routing |
| Scoped to SAP section | absent | Phase 2 answer 2: protocols follow ICH E6/E9 with a structured SAP — reviewing the full protocol wastes attention on non-statistical sections | Law 5: irrelevant context hurts |
| Added flaw type taxonomy | "common statistical design flaws" (vague) | Phase 2 answer 1: user specified three categories — without explicit typing, the LLM generates an unpredictable mix of flaw categories | Law 4: underspecification failure |
| Added "flag only, no corrections" constraint | absent | Phase 2 answer 3: biostatisticians handle corrections — LLM-suggested corrections could be acted on by mistake | Anti-Pattern 5: domain overreach boundary |
| Added "atypical — verify intent" handling | absent | Some unconventional designs are intentional — binary flag/no-flag misses this. Included with [USER: confirm?] | Anti-Pattern 6: under-constraining tiebreaker |
| Included Vocabulary section | absent | Domain terms (multiplicity adjustment, estimand framework, alpha spending) require precise activation — unlike standard programming terms, these have clinical-specific meanings that affect review quality | Law 1: vocabulary routing + Anti-Pattern 4 avoidance |
