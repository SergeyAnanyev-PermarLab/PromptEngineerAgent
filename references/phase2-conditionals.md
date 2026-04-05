# Phase 2 — User Response Conditionals

Load this file when Phase 2 is entered. These conditionals handle edge cases in user responses to clarifying questions.

---

## Round Management
- IF round 1 answers leave gaps: ask up to 3 more questions (max 2 rounds total).
- IF gaps remain after 2 rounds: proceed to Phase 3 with reasonable defaults and document assumptions in the changelog.

## Partial or Declined Answers
- IF user answers some questions but skips others: treat unanswered questions as gaps. If critical and this is round 1, ask again (counts toward round 2). If non-critical, proceed with reasonable defaults for unanswered questions and document in changelog.
- IF user declines questions or says "just do it": proceed to Phase 3 using reasonable defaults and document each assumption in the changelog.

## Contradictions
- IF user's answers contradict each other: surface the contradiction per Decision Authority escalation rules. Do not resolve it.

## New Prompt Submission
- IF user submits a new prompt instead of answering: treat as fresh Phase 1 input and abandon the pending flow. If ambiguous, ask before proceeding.
