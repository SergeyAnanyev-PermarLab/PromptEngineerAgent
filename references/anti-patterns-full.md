# Anti-Pattern Watchlist — Full Detail

Load this file before entering Phase 2 or Phase 3. The compact detection-trigger table in CLAUDE.md is always loaded; this file provides the full reasoning, resolution steps, and MAST mappings needed during active elicitation and restructuring.

---

## Anti-Pattern 1: Over-questioning

**Detection:** Asking 5+ questions on a low-complexity prompt that could proceed with reasonable defaults. Blocking simple tasks with unnecessary elicitation.

**Why it fails:** Elicitation overhead exceeds the value it adds. The user abandons the flow or provides low-effort answers that are worse than reasonable defaults.

**Resolution:** Apply bypass thresholds: low-complexity prompts need only Task. Proceed with reasonable defaults and state assumptions in the changelog.

MAST note: No direct MAST mapping — MAST documents under-clarification (FM-2.2), not over-clarification. This is a domain-specific failure mode for elicitation agents.

---

## Anti-Pattern 2: Scope Inflation

**Detection:** Adding dimensions, constraints, or acceptance criteria the user never implied. "Improving" the prompt by making it about a bigger task.

**Why it fails:** The optimized prompt no longer represents the user's intent. The user either rejects it entirely or accepts inflated scope they didn't want.

**Resolution:** Add only what the user's words directly imply. If uncertain whether a dimension is implied, ask — don't add. When this conflicts with Anti-Pattern 6 (Under-constraining), see AP6's resolution for the tiebreaker: include with [USER: confirm?].

MAST: FM-1.1 (disobeying task spec)

---

## Anti-Pattern 3: Restructure When Unnecessary

**Detection:** Rewriting a prompt that already meets threshold. The agent should pass through good prompts, not touch everything.

**Why it fails:** Unnecessary restructuring introduces paraphrasing errors and signals that the agent doesn't recognize quality input, eroding user trust.

**Resolution:** If the prompt meets the complexity bypass threshold, pass it through with a brief note on what's already strong. Do not restructure for the sake of restructuring.

MAST: FM-1.1 (disobeying task spec)

---

## Anti-Pattern 4: Parrot Restructuring

**Detection:** Mechanically filling all 5 sections with rephrased versions of the user's words. No actual analysis of what's missing — just reformatting.

**Why it fails:** Reformatting without analysis adds no value. The user receives a longer version of what they already wrote, with the same gaps intact.

**Resolution:** Each section in the restructured prompt must contain at least one element not present in the original prompt. If no new element is warranted for a section, omit it rather than padding with rephrased content.

MAST: FM-3.2 (incomplete verification)

---

## Anti-Pattern 5: Domain Overreach

**Detection:** Making domain-specific decisions that belong to the user. The agent structures the prompt — the user owns the domain content.

**Why it fails:** The agent fills in domain content it doesn't have expertise to choose correctly. The user either misses the substitution or has to undo it.

**Resolution:** Flag the domain decision as a question for the user. Use placeholder text like "[USER: specify X]" rather than filling in domain content.

MAST: FM-1.2 (disobeying role spec)

---

## Anti-Pattern 6: Under-constraining

**Detection:** Omitting constraints or acceptance criteria that the user's words directly imply, out of excessive caution about Scope Inflation or Domain Overreach. Producing a "safe" but underspecified optimized prompt.

**Why it fails:** The optimized prompt is less specific than the original. The downstream LLM fills the gap with defaults the user didn't intend, producing off-target output.

**Resolution:** If the user's prompt implies a constraint, include it. The test: would a reasonable reader of the original prompt expect this constraint? When Scope Inflation and Under-constraining pull in opposite directions, include the constraint with a [USER: confirm?] tag.

MAST: FM-3.2 (incomplete verification)

---

## Anti-Pattern 7: Phase 2 Bleed-Through

**Detection:** Your response contains both clarifying questions and a ## heading for a restructured section, or any restructuring content after a question mark.

**Why it fails:** LLM completion bias drives the model to "help" by providing an initial restructure alongside questions, violating the full-stop contract and removing the user's opportunity to shape the output.

**Resolution:** Stop generating. Delete any content after the questions. End with "Waiting for your answers before I restructure."

MAST: FM-1.1 (disobeying task spec)
