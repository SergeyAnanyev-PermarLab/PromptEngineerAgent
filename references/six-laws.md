# The Six Laws of Context Engineering — Full Reference

Load this file on demand when a specific law needs detailed application or when working on high-complexity tasks (system design, architecture, agent configuration). For the compact activation table, see the user's global `~/.claude/CLAUDE.md` (not included in this repo — it contains personal configuration).

**Source of truth:** All citations below are 1:1 copies from `references/METHODOLOGY.md` — the human-authored, human-verified canonical source. Do NOT paraphrase, summarize, correct, or "improve" these citation strings. If a discrepancy is found between this file and METHODOLOGY.md, METHODOLOGY.md wins.

---

## Law 1. Vocabulary Routing

Use 15-30 precise domain terms per agent/task, organized in 3-5 expert clusters. Attribution amplifies routing — "bounded context (Evans, DDD)" activates deeper knowledge than "separate the code logically."

There is an optimal specificity range — too generic activates shallow clusters, too specialized disrupts reasoning. The 15-year practitioner test: would a senior with 15+ years use this exact term with a peer?

**Banned:** Generic consultant-speak ("leverage," "best practices," "robust solution"), buzzword stacking.

**Evidence (verbatim from METHODOLOGY.md):**

Ranjan (2025), "One Word Is Not Enough," demonstrated that semantic prompt prefixes improve word embedding quality by up to +0.66 Spearman correlation through semantic priming -- contextual framing signals models to produce lexically meaningful representations. Schreiter (2024), "Prompt Engineering: How Prompt Vocabulary affects Domain Knowledge," tested vocabulary specificity on generative LLMs across STEM, law, and medicine, finding an optimal specificity range (nouns: 17.72-19.70 on their scale) rather than a linear relationship -- too generic activates shallow clusters, too specialized disrupts reasoning flow (verb specificity on GSM8K: rho = -0.89). The Anthropic context engineering guide and IBM prompt engineering documentation both identify vocabulary specificity as a primary driver of output quality.

---

## Law 2. Real-World Roles

Assign real job titles in brief identity statements of fewer than 50 tokens. No flattery. No superlatives. No invented personas. One role per agent.

Persona prompting helps alignment tasks (tone, structure, safety) but damages factual accuracy. For knowledge-heavy tasks, use perspective-framing over identity assignment.

**Evidence (verbatim from METHODOLOGY.md):**

The PRISM (Persona Routing via Intent-based Self-Modeling) framework (Hu, Rostami, Thomason, USC, 2026) studied how persona assignment affects LLM output across 6 models, MT-Bench, MMLU (14,042 questions), and safety benchmarks. Brief identities cause less accuracy damage. PRISM tested three persona lengths (~5, ~75, ~150 tokens) and found a continuous gradient: MMLU baseline 71.6% → ~5-token persona 68.0% → ~150-token persona 66.3%. Llama-3.1-8B showed a catastrophic 22-point collapse (68.4% → 46.3%) with expert personas. PRISM identified an alignment-accuracy tradeoff: stronger personas improve instruction following (safety tasks gained up to +17.7 points) but reduce factual accuracy. For factual/knowledge tasks, no persona at all outperforms any persona.

---

## Law 3. Scaling Laws

**Always evaluate the need for agents.** Start at Level 0 (single agent) and escalate only when the current level demonstrably fails:

- L0: Single agent
- L1: Agent + tools
- L2: Worker + reviewer
- L3: Small team (3-5 max)
- L4: Multi-team (rare)

Four conditions must ALL be true before multi-agent: (1) task decomposes into subtasks with clean interfaces, (2) subtasks need genuinely different expertise, (3) single-agent trial showed gaps, (4) scope justifies 3-5x token cost.

**Evidence (verbatim from METHODOLOGY.md):**

Kim et al. (2025), "Towards a Science of Scaling Agent Systems," tested 180 configurations across 4 benchmarks and 3 LLM families, providing the saturation data, cost multipliers (centralized: 3.85x, hybrid: 6.15x), error amplification factors (independent: 17.2x, centralized: 4.4x), and the 45% threshold (Beta = -0.408, p<0.001). Critically, sequential reasoning tasks degraded 39-70% across ALL multi-agent variants. These scaling laws proved model-agnostic (max difference in slopes: 0.023). CaptainAgent (Song et al., 2024) demonstrated that adaptive team composition -- selecting agents per-task from a library of 541 accumulated agents -- outperforms static teams in 4 of 5 scenarios, with gains up to 30.43% on data analysis tasks.

---

## Law 4. Anti-Patterns

Name failure modes explicitly. Don't just describe the happy path. Reference the MAST taxonomy when relevant:

- **Specification failures (41.77%):** disobey task/role spec, step repetition, lost context, unaware of termination
- **Misalignment failures (36.94%):** conversation reset, fail to clarify, task derailment, info withholding, ignored input, reasoning-action mismatch
- **Verification failures (21.30%):** premature termination, incomplete/incorrect verification

Require reviewers to identify at least one issue or explicitly justify clearance with evidence. Never rubber-stamp.

**Evidence (verbatim from METHODOLOGY.md):**

The MAST framework (Cemri et al., 2025, UC Berkeley) analyzed 200 execution traces across 7 open-source multi-agent frameworks (MetaGPT, ChatDev, HyperAgent, OpenManus, AppWorld, Magentic-One, AG2), producing 1,600+ annotated traces with Cohen's Kappa = 0.88. It provides a taxonomy of 14 failure modes across three categories. Specification and System Design failures (FC1, 41.77%) cover disobeying task spec (FM-1.1), disobeying role spec (FM-1.2), step repetition (FM-1.3), loss of conversation history (FM-1.4), and unawareness of termination conditions (FM-1.5). Inter-Agent Misalignment failures (FC2, 36.94%) cover conversation reset (FM-2.1), failure to ask for clarification (FM-2.2), task derailment (FM-2.3), information withholding (FM-2.4), ignoring other agent's input (FM-2.5), and reasoning-action mismatch (FM-2.6). Task Verification and Termination failures (FC3, 21.30%) cover premature termination (FM-3.1), incomplete verification (FM-3.2), and incorrect verification (FM-3.3).

Zamfirescu-Pereira et al., "Why Johnny Can't Prompt" (CHI 2023, UC Berkeley), documented how users systematically fail to anticipate LLM failure modes through six patterns: opportunistic exploration, overgeneralization from single examples, preference for negative framing over positive, anthropomorphic mental models, regression blindness, and inferring incapability from phrasing failure -- making explicit anti-pattern lists essential.

---

## Law 5. Progressive Disclosure

Layer information — don't dump everything at once:

- **L1 (always loaded, ~200-500 tokens):** Role identity + domain vocabulary
- **L2 (task-triggered, ~500-2000 tokens):** SOPs and checklists
- **L3 (on-demand, 2000+ tokens):** Full docs, examples, reference material
- **L4 (compressed):** Summaries of large inputs

The U-shaped attention curve is architectural, not learned. Place critical info at beginning and end of context. Budget usable context conservatively — irrelevant context actively hurts, it's not just wasted space.

**Evidence (verbatim from METHODOLOGY.md):**

Liu et al. (2024), "Lost in the Middle" (TACL), established the U-shaped attention curve empirically: ~90% accuracy at beginning, ~50-60% in middle, ~80-85% at end -- a 30-40 point drop for middle-positioned information, persisting even in long-context models. Chowdhury (2026), "Lost in the Middle at Birth," proved the U-shape is architectural, not learned -- it exists at random initialization (Spearman rho = 0.99 between theory and measurement) due to causal masking (primacy) and residual connections (recency). Middle tokens suffer O(1/(H-1)!) factorial suppression. Training amplifies rather than corrects this bias. RoPE modifications will not fix it. The Anthropic context engineering guide recommends progressive disclosure and layered context loading as primary design strategies, with a practical rule: budget 60-70% of the advertised context window as usable capacity.

---

## Law 6. Structured Artifacts

Communicate through typed deliverables (PRDs, ADRs, test reports, interface contracts) with defined schemas and acceptance criteria. Not free-form prose. Every deliverable specifies its format; every handoff is verifiable.

**Evidence (verbatim from METHODOLOGY.md):**

MetaGPT (Hong et al., 2023) demonstrated that multi-agent teams passing structured artifacts outperform those using open dialogue, reducing human revision cost by ~67% and improving executability by ~67% (computed from Table 1). Structured handoffs reduce misinterpretation. This directly prevents MAST FM-1.2 (Misinterpretation), the second most common failure mode in multi-agent systems. Artifacts create verifiable quality gates. The artifact chain is the audit trail.

---

## Source Table (verbatim 1:1 copy from METHODOLOGY.md § 7)

| Source | Year | Key Contribution | Used In |
|---|---|---|---|
| Ranjan, "One Word Is Not Enough" (arXiv:2512.06744) | 2025 | Semantic prompt prefixes improve embedding quality up to +0.66 Spearman (near-zero baseline models; +0.29 for models with existing baselines) | Principle 1 |
| Schreiter, "How Prompt Vocabulary affects Domain Knowledge" (Gottingen) | 2024 | Optimal vocabulary specificity range; verb specificity disrupts reasoning | Principle 1 |
| Anthropic, Context Engineering Guide | 2025 | Progressive disclosure, attention budget, layered context, 60-70% usable window | Principles 1, 5 |
| IBM, Prompt Engineering Documentation | 2024 | Vocabulary precision as quality driver (practitioner guidance) | Principle 1 |
| Hu et al., PRISM (USC, arXiv:2603.18507) | 2026 | Persona length gradient, alignment-accuracy tradeoff, verbosity bias | Principle 2 |
| DigitalOcean, Prompt Engineering Guide | 2024 | Perspective-framing over identity assignment | Principle 2 |
| Kim et al., "Towards a Science of Scaling Agent Systems" (arXiv:2512.08296) | 2025 | 45% threshold (p<0.001), cost multipliers, error amplification, sequential degradation 39-70% | Principle 3 |
| Song et al., CaptainAgent (arXiv:2405.19425) | 2024 | Adaptive team composition outperforms static in 4/5 scenarios, up to +30.43% | Principle 3 |
| Cemri et al., MAST (UC Berkeley, arXiv:2503.13657) | 2025 | 14 failure modes across specification (41.77%), misalignment (36.94%), verification (21.30%) | Principle 4 |
| Zamfirescu-Pereira et al., "Why Johnny Can't Prompt" (CHI, UC Berkeley) | 2023 | Six patterns of user prompt failure; regression blindness; negative framing preference | Principle 4 |
| Liu et al., "Lost in the Middle" (TACL) | 2024 | U-shaped attention: 90% start, 50-60% middle, 80-85% end | Principle 5 |
| Chowdhury, "Lost in the Middle at Birth" (arXiv:2603.10123) | 2026 | U-shape is architectural (causal masking + residual), not learned; factorial middle suppression | Principle 5 |
| Hong et al., MetaGPT (arXiv:2308.00352) | 2023 | Structured artifacts: ~67% reduction in human revision cost, ~67% executability improvement (computed from Table 1) | Principle 6 |
| Anthropic, "Building Effective Agents" | 2024 | Structured handoffs; 5 workflow patterns; tool optimization prioritized over prompt optimization | Principle 6 |
