# Forge Methodology

How Forge designs agents and teams, why each principle exists, and the research behind it.

---

## 1. The Vocabulary Routing Principle

**What we do:** Every agent and skill in Forge carries a payload of 15-30 precise domain terms, organized into 3-5 clusters of related concepts. These are not decorative. They are the single most important element in an agent definition.

**Why it works:** Large language models organize knowledge in clusters within their embedding space. When a prompt contains the term "circuit breaker pattern (Nygard)," the model activates a cluster of knowledge around resilience engineering, the book *Release It!*, retry logic, bulkhead isolation, and related distributed systems concepts. When a prompt says "handle errors gracefully," the model activates a much broader, shallower cluster -- blog-post-level advice about try/catch blocks.

The vocabulary *is* the routing signal. A single precise term can replace paragraphs of explanation because it points directly at the knowledge region where the answer lives. "Bounded context (Evans, DDD)" routes to domain-driven design, context mapping, and microservice boundaries. "Separate the code into logical pieces" routes to generic software engineering advice.

Three rules govern term selection. First, the **15-year practitioner test**: would a senior practitioner with 15+ years of experience use this exact term when speaking to a peer? "Story mapping (Patton)" passes. "Best practices for planning" fails -- no senior says that to a peer. Second, **attribution amplifies routing**: including the originator of a framework ("fitness functions (Ford and Parsons)") activates more specific knowledge than the term alone. Third, **clusters should mirror expert discourse**: terms grouped together should be terms that co-occur in expert conversation. A "System Design" cluster for an architect agent might include hexagonal architecture (Cockburn), bounded context (Evans), event-driven architecture, and CQRS -- terms that a systems architect would naturally discuss together.

The inverse matters too. Generic consultant-speak ("leverage," "best practices," "robust solution") activates generic business writing clusters. Buzzword stacking ("AI-driven blockchain microservices with DevSecOps") creates a scatter-shot pattern where no single knowledge cluster dominates. Both are banned in Forge agent definitions.

**The evidence:** Ranjan (2025), "One Word Is Not Enough," demonstrated that semantic prompt prefixes improve word embedding quality by up to +0.66 Spearman correlation through semantic priming -- contextual framing signals models to produce lexically meaningful representations. Schreiter (2024), "Prompt Engineering: How Prompt Vocabulary affects Domain Knowledge," tested vocabulary specificity on generative LLMs across STEM, law, and medicine, finding an optimal specificity range (nouns: 17.72-19.70 on their scale) rather than a linear relationship -- too generic activates shallow clusters, too specialized disrupts reasoning flow (verb specificity on GSM8K: rho = -0.89). The Anthropic context engineering guide and IBM prompt engineering documentation both identify vocabulary specificity as a primary driver of output quality. Forge's own findings from the Impeccable project confirmed that swapping generic terms for expert vocabulary measurably improved output without any other changes to prompts.

---

## 2. The Real-World Role Principle

**What we do:** Forge assigns agents real job titles -- Product Manager, Software Architect, Site Reliability Engineer -- using brief identity statements of fewer than 50 tokens. No flattery. No invented personas.

**Why it works:** The PRISM (Persona Routing via Intent-based Self-Modeling) framework (Hu, Rostami, Thomason, USC, 2026) studied how persona assignment affects LLM output across 6 models, MT-Bench, MMLU (14,042 questions), and safety benchmarks. Three findings shape Forge's approach.

First, **brief identities cause less accuracy damage.** PRISM tested three persona lengths (~5, ~75, ~150 tokens) and found a continuous gradient: MMLU baseline 71.6% → ~5-token persona 68.0% → ~150-token persona 66.3%. Llama-3.1-8B showed a catastrophic 22-point collapse (68.4% → 46.3%) with expert personas. Shorter personas trigger less interference between instruction-following and factual recall pathways. Second, **real job titles activate real knowledge clusters.** "You are a senior site reliability engineer" activates SRE-related training data -- runbooks, incident response, SLO definitions -- more effectively than "You are an expert in keeping systems running." Real titles that exist in real organizations have dense representation in the training corpus. Third, **superlatives introduce verbosity bias.** Self-evaluation judges consistently preferred longer, more confident-sounding but less accurate persona-prompted outputs. This verbosity bias degrades factual accuracy while appearing to improve quality -- models generate outputs that sound authoritative but contain more errors.

There is a tension at play. PRISM identified an **alignment-accuracy tradeoff**: stronger personas improve instruction following (safety tasks gained up to +17.7 points) but reduce factual accuracy. For factual/knowledge tasks, no persona at all outperforms any persona. The solution is to keep identities brief and grounded, and to use personas selectively -- alignment-dependent tasks (safety, tone, structure) benefit; pretraining-dependent tasks (factual recall, math, coding) do not. Note: reasoning-distilled models (R1 family) showed near-zero sensitivity to persona effects.

Forge operationalizes this with a strict format: real job title, primary responsibility, organizational context, reporting relationships. No superlatives. No quality claims. One role per agent -- combining titles ("You are a software architect and project manager and QA lead") fragments knowledge activation across multiple clusters.

**The evidence:** The PRISM framework (Hu et al., 2026) established the shorter-is-less-harmful gradient across three persona lengths and documented the alignment-accuracy tradeoff with quantitative data across 6 models. The DigitalOcean prompt engineering guide independently reached similar conclusions, recommending perspective-framing ("Analyze with focus on risk tolerance") over identity assignment ("You are a financial analyst"). Anthropic's harness design research confirmed that brief, realistic identities outperform elaborate persona descriptions.

---

## 3. The Scaling Laws: When Teams Help and When They Hurt

**What we do:** Forge limits teams to 3-5 agents and requires all four of these conditions before recommending multi-agent: (1) the task is decomposable into subtasks with clean interfaces, (2) subtasks require genuinely different expertise, (3) a single-agent trial showed clear capability gaps, and (4) the project scope justifies a 3-5x token cost increase. If any condition is false, we use a single agent.

**Why it works:** Kim et al.'s 2025 multi-agent scaling study established three principles that upend common assumptions about agent teams.

**Effectiveness depends on task decomposability.** Tasks that split cleanly into independent subtasks with typed interfaces benefit from multiple agents. Tasks with high step-to-step interdependency do not. The test: can you define the interface between subtasks as a typed artifact with a clear schema? If not, the task is not decomposable and a single agent will outperform a team.

**Coordination overhead can exceed benefits.** Every additional agent adds communication cost -- messages must be composed, transmitted, parsed, and acted upon. Each handoff risks information loss. On sequential reasoning tasks where steps tightly depend on each other, multi-agent systems degrade by 39-70% compared to single agents. The agents spend more tokens coordinating than they contribute in useful work.

**Team size saturates at 4 agents.** Performance scales sub-linearly. Three agents offer the best efficiency-to-capability ratio. Four is the peak for complex tasks. At five, gains are marginal but coordination cost is significant. At seven or more, adding agents typically degrades overall performance. The cost numbers are stark: a 3-agent team costs 3.5x the tokens but produces only 2.3x the output. A 5-agent team costs 7x for 3.1x the output.

The **45% threshold** is perhaps the most important finding. If a single well-prompted agent achieves more than 45% of optimal performance on a task, adding more agents yields diminishing returns. The single agent already covers the accessible portion of the problem; the remaining difficulty lives in coordination-heavy integration work where more agents add more overhead, not more capability.

Forge operationalizes this through the **cascade pattern**: always start at Level 0 (single agent), escalate to Level 1 (agent + tools), Level 2 (worker + reviewer), Level 3 (small team), or Level 4 (multi-team) only when the current level demonstrably fails. Never skip levels.

**The evidence:** Kim et al. (2025), "Towards a Science of Scaling Agent Systems," tested 180 configurations across 4 benchmarks and 3 LLM families, providing the saturation data, cost multipliers (centralized: 3.85x, hybrid: 6.15x), error amplification factors (independent: 17.2x, centralized: 4.4x), and the 45% threshold (Beta = -0.408, p<0.001). Critically, sequential reasoning tasks degraded 39-70% across ALL multi-agent variants. These scaling laws proved model-agnostic (max difference in slopes: 0.023). CaptainAgent (Song et al., 2024) demonstrated that adaptive team composition -- selecting agents per-task from a library of 541 accumulated agents -- outperforms static teams in 4 of 5 scenarios, with gains up to 30.43% on data analysis tasks.

---

## 4. The Anti-Pattern Principle

**What we do:** Every agent and skill definition in Forge includes 5-10 named failure modes, each with detection signals and resolution steps. These are not suggestions. They are load-bearing components of the design.

**Why it works:** Without negative constraints, LLMs gravitate toward the center of their training distribution -- the most average, most generic output. This is the default behavior: produce something that looks plausible and sounds confident. Named anti-patterns serve two functions.

First, they are **steering mechanisms**. Telling an agent "avoid rubber-stamp approval (MAST FM-3.1)" pushes its output away from the distribution center -- away from the easy, sycophantic review that says "looks great!" and toward the harder, more specific critique that identifies actual issues. Each named anti-pattern acts as a repulsive force, nudging the model away from a known failure mode.

Second, they are **early warning systems**. When you name a failure and describe its detection signals ("review output contains only praise, no issues identified, approval latency is very short"), you give both the agent and the human operator a way to recognize when things are going wrong. Without named failures, degradation is invisible until the final output is obviously broken.

The MAST framework (Cemri et al., 2025, UC Berkeley) analyzed 200 execution traces across 7 open-source multi-agent frameworks (MetaGPT, ChatDev, HyperAgent, OpenManus, AppWorld, Magentic-One, AG2), producing 1,600+ annotated traces with Cohen's Kappa = 0.88. It provides a taxonomy of 14 failure modes across three categories. **Specification and System Design failures (FC1, 41.77%)** cover disobeying task spec (FM-1.1), disobeying role spec (FM-1.2), step repetition (FM-1.3), loss of conversation history (FM-1.4), and unawareness of termination conditions (FM-1.5). **Inter-Agent Misalignment failures (FC2, 36.94%)** cover conversation reset (FM-2.1), failure to ask for clarification (FM-2.2), task derailment (FM-2.3), information withholding (FM-2.4), ignoring other agent's input (FM-2.5), and reasoning-action mismatch (FM-2.6). **Task Verification and Termination failures (FC3, 21.30%)** cover premature termination (FM-3.1), incomplete verification (FM-3.2), and incorrect verification (FM-3.3).

Premature termination (FM-3.1) and incomplete verification (FM-3.2) are particularly insidious in review contexts. LLMs have sycophantic tendencies -- a review agent with a weak prompt will approve everything. The fix is structural: require reviewers to identify at least one issue or explicitly justify clearance with specific evidence. Role specification violations (FM-1.2) are prevented by structured artifact handoffs with explicit schemas, which connects directly to Principle 6. The core MAST finding: improvements in base model capabilities alone will not fix these failures -- organizational structure must be sound.

**The evidence:** The MAST framework (Cemri et al., 2025) identified and categorized the 14 failure modes with frequency data across 1,600+ traces. Zamfirescu-Pereira et al., "Why Johnny Can't Prompt" (CHI 2023, UC Berkeley), documented how users systematically fail to anticipate LLM failure modes through six patterns: opportunistic exploration, overgeneralization from single examples, preference for negative framing over positive, anthropomorphic mental models, regression blindness, and inferring incapability from phrasing failure -- making explicit anti-pattern lists essential. Anthropic's prompt engineering documentation recommends negative constraints as a primary technique for steering output quality.

---

## 5. The Progressive Disclosure Principle

**What we do:** Forge structures information in layers. Layer 1 (always loaded, ~200-500 tokens): role identity and domain vocabulary. Layer 2 (task-triggered, ~500-2000 tokens): SOPs and checklists for the current task type. Layer 3 (on-demand, 2000+ tokens): full documentation, examples, and reference material. Layer 4 (compressed): summaries of large inputs. Context is loaded based on need, not dumped in bulk.

**Why it works:** The attention mechanism creates a finite **attention budget** -- every token competes for weight, and adding tokens past the optimal zone actively degrades output. Research shows the optimal zone is 15-40% of the context window. Below 10%, the model lacks sufficient information and hallucinates. Above 60%, relevant information gets buried in noise.

Within the context window, attention is not uniform. Liu et al. (2024), in "Lost in the Middle," demonstrated a **U-shaped attention curve**: tokens at the beginning and end of the context receive disproportionate attention (primacy and recency effects), while tokens in the middle receive significantly less. This has direct design implications. Role identity and critical constraints go first (highest attention). The specific task instruction goes last (high attention). Vocabulary, reference material, and examples occupy the middle.

Front-loading the vocabulary payload ensures the knowledge routing happens early, before the model begins processing task details. Back-loading the task instruction means the model's last input before generating is the specific thing it needs to do. Reference material in the middle is available but does not compete with the framing (beginning) or the directive (end) for attention priority.

Progressive disclosure also prevents **context poisoning** -- a phenomenon where irrelevant or contradictory context actively misleads the model. It is not merely a waste of tokens; stale instructions, resolved issues still present in context, and verbose examples that bury the actual pattern all degrade output quality. Loading context on demand, in layers, with aggressive curation is the countermeasure.

**The evidence:** Liu et al. (2024), "Lost in the Middle" (TACL), established the U-shaped attention curve empirically: ~90% accuracy at beginning, ~50-60% in middle, ~80-85% at end -- a 30-40 point drop for middle-positioned information, persisting even in long-context models. Chowdhury (2026), "Lost in the Middle at Birth," proved the U-shape is architectural, not learned -- it exists at random initialization (Spearman rho = 0.99 between theory and measurement) due to causal masking (primacy) and residual connections (recency). Middle tokens suffer O(1/(H-1)!) factorial suppression. Training amplifies rather than corrects this bias. RoPE modifications will not fix it. The Anthropic context engineering guide recommends progressive disclosure and layered context loading as primary design strategies, with a practical rule: budget 60-70% of the advertised context window as usable capacity.

---

## 6. The Structured Artifact Principle

**What we do:** Forge teams communicate through structured artifacts -- PRDs, ADRs, test reports, interface contracts -- with defined schemas and explicit acceptance criteria. Agents do not engage in free-form dialogue with each other.

**Why it works:** MetaGPT (Hong et al., 2023) demonstrated that multi-agent teams passing structured artifacts outperform those using open dialogue, reducing human revision cost by ~67% and improving executability by ~67% compared to dialogue-based approaches (computed from Table 1). The reasons are straightforward.

**Structured handoffs reduce misinterpretation.** When an agent produces a typed deliverable with an explicit schema, the receiving agent has far less room to misread the intent. This directly prevents MAST FM-1.2 (Misinterpretation), the second most common failure mode in multi-agent systems. Free-form messages are ambiguous by nature; schemas enforce clarity.

**Artifacts create verifiable quality gates.** A PRD either contains the required sections or it does not. A test report either covers the specified scenarios or it does not. This binary verifiability makes it possible to build rejection authority into the pipeline -- downstream agents can send work back upstream if the artifact does not meet acceptance criteria. With free dialogue, there is nothing concrete to accept or reject.

**The artifact chain is the audit trail.** In a structured pipeline, the sequence of artifacts tells the full story of how a decision was made and how work progressed. In a free-dialogue system, you must read the entire conversation history to reconstruct what happened. For any system that needs to be debugged, improved, or explained to a human, auditability is not optional.

Forge operationalizes this by requiring every agent definition to specify its deliverables with exact formats, and every team blueprint to define an artifact chain showing which artifacts flow between which agents. The default communication topology is artifact handoff, not conversation. Free dialogue is reserved for human-facing explanations, never for inter-agent communication.

**The evidence:** MetaGPT (Hong et al., 2023) demonstrated ~67% reduction in human revision cost and ~67% executability improvement (computed from Table 1: revision cost 2.5 → 0.83, executability 2.25 → 3.75) through artifact-based versus dialogue-based communication. Anthropic's "Building Effective Agents" (2024) recommended structured handoffs as the default communication pattern for multi-agent systems.

---

## 7. Sources and Further Reading

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
| Hong et al., MetaGPT (arXiv:2308.00352) | 2023 | Structured artifacts: 67% reduction in human revision cost, 67% executability improvement | Principle 6 |
| Anthropic, "Building Effective Agents" | 2024 | Structured handoffs; 5 workflow patterns; tool optimization prioritized over prompt optimization | Principle 6 |

Full research notes with detailed findings, data tables, and implementation guidance are in the [`docs/research/`](docs/research/) directory:

- [`vocabulary-routing.md`](docs/research/vocabulary-routing.md) -- Term selection, clustering, the 15-year practitioner test, Schreiter Goldilocks zone
- [`persona-science.md`](docs/research/persona-science.md) -- PRISM findings (Hu et al., 2026), persona length gradient, alignment-accuracy tradeoff
- [`scaling-laws.md`](docs/research/scaling-laws.md) -- Kim et al. (2025) principles, cost multipliers, topology selection, 45% threshold
- [`context-engineering.md`](docs/research/context-engineering.md) -- Attention budget, U-curve (architectural origin), progressive disclosure layers
- [`failure-taxonomy.md`](docs/research/failure-taxonomy.md) -- All 14 MAST failure modes (Cemri et al., 2025) with detection and prevention
- [`team-design.md`](docs/research/team-design.md) -- MetaGPT findings, CaptainAgent, cascade pattern, topology comparison
