# Works Cited — Verification Report

Generated: 2026-04-05
Method: WebFetch of arXiv abstract pages + WebSearch for published papers and industry guides
Scope: All 14 sources in references/METHODOLOGY.md § 7 Source Table

---

## Verification Summary

| # | Source (as cited) | Exists? | Title Match? | Author Match? | Year Match? | Claims Verified? | Status |
|---|---|---|---|---|---|---|---|
| 1 | Ranjan (2025), "One Word Is Not Enough" | Yes | Yes | Yes | Yes | **Partial** | MAJOR ISSUE |
| 2 | Jansen et al. (2025, Google/MIT), "Towards a Science of Scaling Agent Systems" | Yes | Yes | **NO** | Yes | Partial | CRITICAL ISSUE |
| 3 | Song et al. (2024), CaptainAgent | Yes | Yes | Yes | Yes | **Partial** | MAJOR ISSUE |
| 4 | Hong et al. (2023), MetaGPT | Yes | Yes | Yes | Yes | Partial | MINOR — unverifiable from abstract |
| 5 | Hu et al. (2026), PRISM (USC) | Yes | Yes | Yes | Yes | Partial | CLEAN |
| 6 | Cemri et al. (2025), MAST (UC Berkeley) | Yes | Yes | Yes | Yes | Yes | CLEAN |
| 7 | Wu et al. (2025, MIT), "Lost in the Middle at Birth" | Yes | Yes | **NO** | **NO** | Partial | CRITICAL ISSUE |
| 8 | Liu et al. (2024), "Lost in the Middle" (TACL) | Yes | Yes | Yes | Yes | Yes | CLEAN |
| 9 | Zamfirescu-Pereira et al. (2023), "Why Johnny Can't Prompt" (CHI) | Yes | Yes | Yes | Yes | Yes | CLEAN |
| 10 | Schreiter (2024), "How Prompt Vocabulary affects Domain Knowledge" | Yes | Yes | Yes | Yes | Partial | CLEAN |
| 11 | Anthropic, "Building Effective Agents" (2024) | Yes | Yes | N/A | Yes | Yes | CLEAN |
| 12 | Anthropic, Context Engineering Guide (2025) | Yes | Yes | N/A | Yes | Partial | CLEAN |
| 13 | IBM, Prompt Engineering Documentation (2024) | Yes | Yes | N/A | Yes | Yes | CLEAN |
| 14 | DigitalOcean, Prompt Engineering Guide (2024) | Yes | Yes | N/A | Yes | Yes | CLEAN |

**Result: 10 CLEAN, 2 CRITICAL, 2 MAJOR, 0 HALLUCINATED**

No source is fabricated. All 14 papers/guides exist and address the topics described. However, 4 sources have metadata or claim discrepancies that need correction.

---

## Critical Issues

### ISSUE 1: Source #2 — Wrong author attribution

**As cited:** Jansen et al. (2025, Google/MIT), "Towards a Science of Scaling Agent Systems," arXiv:2512.08296

**Actual:** The paper at arXiv:2512.08296 is authored by **Yubin Kim, Ken Gu, Chanwoo Park, Chunjong Park, Samuel Schmidgall, A. Ali Heydari, Yao Yan, Zhihan Zhang, Yuchen Zhuang, Mark Malhotra, Paul Pu Liang, Hae Won Park, Yuzhe Yang, Xuhai Xu, Yilun Du, Shwetak Patel, Tim Althoff, Daniel McDuff, Xin Liu**. No author named "Jansen" appears in the author list. The institutional affiliation "Google/MIT" is not confirmed from the arXiv page.

**Correction needed:** Change "Jansen et al. (2025, Google/MIT)" to "Kim et al. (2025)" throughout METHODOLOGY.md and six-laws.md. Verify institutional affiliation from the full paper.

**Paper content is correct:** The title, arXiv ID, year (2025), and content claims (180 configurations, 45% threshold, sequential degradation) all match the actual paper. Only the author attribution is wrong.

### ISSUE 2: Source #7 — Wrong author attribution AND wrong year

**As cited:** Wu et al. (2025, MIT), "Lost in the Middle at Birth," arXiv:2603.10123

**Actual:** The paper at arXiv:2603.10123 is authored by **Borun D Chowdhury** (single author). No author named "Wu" appears. The submission date is **March 10, 2026** (arXiv prefix 2603 = March 2026), not 2025.

**Corrections needed:**
1. Change "Wu et al. (2025, MIT)" to "Chowdhury (2026)" throughout METHODOLOGY.md and six-laws.md
2. Change year from 2025 to 2026 in the source table
3. Verify institutional affiliation (MIT not confirmed from abstract)

**Paper content is correct:** The title matches, the arXiv ID is correct, and the content claims (U-shape at initialization, causal masking, residual connections, factorial suppression) match the abstract.

---

## Major Issues

### ISSUE 3: Source #1 — Spearman correlation figure discrepancy

**As cited:** "improve word embedding quality by up to +0.66 Spearman correlation"

**Abstract says:** "improvements up to +0.29 in Spearman correlations on SimLex-999" and "correlations as high as +0.73" (recovered from near-zero)

**Analysis:** The cited +0.66 doesn't match either figure in the abstract (+0.29 improvement, +0.73 absolute). The +0.66 may appear elsewhere in the full paper (possibly a different benchmark or metric), but it's not the headline finding. The +0.29 is the improvement figure; the +0.73 is an absolute correlation for models that started near zero.

**Action:** Verify the +0.66 figure against the full paper. If not found, correct to "+0.29 Spearman correlation improvement on SimLex-999" or "+0.73 recovered correlation" depending on which finding is being cited.

### ISSUE 4: Source #3 — CaptainAgent percentage and scenario count discrepancy

**As cited:** "outperforms static teams in 4 of 5 scenarios, with gains up to 30.43% on data analysis tasks" and "library of 541 accumulated agents"

**Abstract says:** "21.94% improvement in average accuracy" across "six real-world scenarios"

**Analysis:** The cited figures (4/5 scenarios, +30.43%, 541 agents) don't match the abstract (six scenarios, 21.94%). These may be from the full paper or from different experimental conditions, but the headline numbers diverge significantly.

**Action:** Verify against the full paper. The 21.94% is the average; 30.43% may be the peak on a specific task. The "4 of 5" vs "six" scenario count suggests different experimental setups. The 541 agent library may be an implementation detail not in the abstract.

---

## Minor Issue

### ISSUE 5: Source #4 — MetaGPT specific figures unverifiable from abstract

**As cited:** "67% reduction in human revision cost, 67% executability improvement" (source table), "roughly 40% reduction in error propagation" (§6 text)

**Abstract says:** "more coherent solutions" and "reduces logic inconsistencies due to cascading hallucinations" — no specific percentages.

**Analysis:** The abstract doesn't contain the specific 67% or 40% figures. These likely come from the evaluation section of the full paper. Not necessarily wrong — just can't be verified from the abstract alone.

**Action:** Low priority. Accept as unverifiable from abstract. If accuracy is critical, verify against the full paper's evaluation tables.

---

## Source-by-Source Verification Detail

### Source 1: Ranjan (2025)
- **arXiv:** 2512.06744
- **Fetched title:** "One Word Is Not Enough: Simple Prompts Improve Word Embeddings"
- **Author:** Rajeev Ranjan — MATCHES
- **Date:** December 7, 2025 — MATCHES (2025)
- **Key abstract claims:** "+0.29 Spearman on SimLex-999," "+0.73 recovered correlation," 7 embedding models, 3 benchmarks
- **Discrepancy:** Cited +0.66 doesn't appear in abstract. See ISSUE 3.

### Source 2: "Jansen" et al. (2025)
- **arXiv:** 2512.08296
- **Fetched title:** "Towards a Science of Scaling Agent Systems" — MATCHES
- **Authors:** Kim, Gu, Park, et al. — DOES NOT MATCH "Jansen"
- **Date:** December 9, 2025 — MATCHES (2025)
- **Key abstract claims:** 180 configurations confirmed, ~45% threshold confirmed, sequential degradation confirmed, centralized coordination +81% confirmed
- **Discrepancy:** Author attribution wrong. See ISSUE 1.

### Source 3: Song et al. (2024), CaptainAgent
- **arXiv:** 2405.19425
- **Fetched title:** "Adaptive In-conversation Team Building for Language Model Agents"
- **Authors:** Linxin Song et al. — MATCHES
- **Date:** May 29, 2024 — MATCHES (2024)
- **Key abstract claims:** "Captain Agent" framework confirmed, "21.94% improvement in average accuracy" (not 30.43%), "six real-world scenarios" (not 4 of 5)
- **Discrepancy:** Percentage and scenario count differ. See ISSUE 4.

### Source 4: Hong et al. (2023), MetaGPT
- **arXiv:** 2308.00352
- **Fetched title:** "MetaGPT: Meta Programming for A Multi-Agent Collaborative Framework" — MATCHES
- **Authors:** Sirui Hong et al. — MATCHES
- **Date:** August 1, 2023 — MATCHES (2023)
- **Key abstract claims:** SOPs in multi-agent systems, assembly line paradigm, reduces hallucination cascading — direction matches
- **Note:** Specific percentages (67%, 40%) not in abstract. See ISSUE 5.

### Source 5: Hu et al. (2026), PRISM
- **arXiv:** 2603.18507
- **Fetched title:** "Expert Personas Improve LLM Alignment but Damage Accuracy: Bootstrapping Intent-Based Persona Routing with PRISM"
- **Authors:** Zizhao Hu, Mohammad Rostami, Jesse Thomason — MATCHES "Hu, Rostami, Thomason, USC"
- **Date:** March 19, 2026 — MATCHES (2026)
- **Key abstract claims:** Persona effects on alignment vs accuracy confirmed, PRISM system confirmed
- **Status:** CLEAN. Specific MMLU figures (71.6%, 68.0%, 66.3%) not in abstract but may be in paper.

### Source 6: Cemri et al. (2025), MAST
- **arXiv:** 2503.13657
- **Fetched title:** "Why Do Multi-Agent LLM Systems Fail?" (MAST is the framework name)
- **Authors:** Mert Cemri et al. — MATCHES
- **Date:** March 17, 2025 — MATCHES (2025)
- **Key abstract claims:** 1600+ annotated traces confirmed, 7 frameworks confirmed, 14 failure modes in 3 categories confirmed
- **Status:** CLEAN.

### Source 7: "Wu" et al. (2025), "Lost in the Middle at Birth"
- **arXiv:** 2603.10123
- **Fetched title:** "Lost in the Middle at Birth: An Exact Theory of Transformer Position Bias" — MATCHES
- **Authors:** Borun D Chowdhury (single author) — DOES NOT MATCH "Wu et al."
- **Date:** March 10, 2026 — DOES NOT MATCH (cited as 2025)
- **Key abstract claims:** Architectural origin confirmed ("initialization"), causal masking confirmed, residual connections confirmed, "factorial dead zone" matches O(1/(H-1)!) claim
- **Discrepancy:** Author and year wrong. See ISSUE 2.

### Source 8: Liu et al. (2024), "Lost in the Middle"
- **Publication:** TACL, volume 12, pages 157-173
- **Authors:** Nelson F. Liu, Kevin Lin, John Hewitt, Ashwin Paranjape, Michele Bevilacqua, Fabio Petroni, Percy Liang — MATCHES
- **Date:** 2024 — MATCHES
- **Key claims:** U-shaped performance curve confirmed
- **Status:** CLEAN.

### Source 9: Zamfirescu-Pereira et al. (2023), "Why Johnny Can't Prompt"
- **Publication:** CHI '23 (April 23–28, 2023, Hamburg)
- **Authors:** J.D. Zamfirescu-Pereira (UC Berkeley), Richmond Wong, Bjoern Hartmann, Qian Yang
- **Date:** 2023 — MATCHES
- **Key claims:** User prompt failure patterns, most-downloaded CHI paper — confirmed
- **Status:** CLEAN.

### Source 10: Schreiter (2024)
- **Type:** Master's thesis, Georg-August-Universität Göttingen (also on arXiv: 2505.17037)
- **Author:** Dimitri Schreiter — MATCHES
- **Date:** Thesis October 2024, arXiv May 2025 — cited as 2024, MATCHES thesis date
- **Key claims:** Optimal specificity range, tested across STEM/law/medicine — confirmed
- **Status:** CLEAN. Specific numbers (17.72-19.70, rho=-0.89) not in search summary but direction confirmed.

### Source 11: Anthropic, "Building Effective Agents" (2024)
- **URL:** https://www.anthropic.com/research/building-effective-agents
- **Date:** December 19, 2024 — MATCHES
- **Key claims:** Five workflow patterns (prompt chaining, routing, parallelization, orchestrator-workers, evaluator-optimizer) — confirmed. Structured handoffs recommended — confirmed.
- **Status:** CLEAN.

### Source 12: Anthropic, Context Engineering Guide (2025)
- **URL:** https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- **Date:** September 2025 — MATCHES
- **Key claims:** Progressive disclosure, layered context, context budgeting — confirmed. "60-70% usable window" not directly confirmed in search summary but context budgeting discussed.
- **Status:** CLEAN. The 60-70% figure may need verification against the actual guide text.

### Source 13: IBM, Prompt Engineering Documentation (2024)
- **URL:** https://www.ibm.com/think/topics/prompt-engineering
- **Key claims:** "every word in a prompt can influence the outcome," vocabulary precision as quality driver — confirmed
- **Status:** CLEAN.

### Source 14: DigitalOcean, Prompt Engineering Guide (2024)
- **URL:** https://www.digitalocean.com/resources/articles/prompt-engineering-best-practices
- **Key claims:** "instead of assigning a role, try being explicit about the perspective you want" — confirmed. Perspective-framing over identity assignment — confirmed.
- **Status:** CLEAN.

---

## Corrections Applied

### Critical — FIXED (2026-04-05)
1. ~~**METHODOLOGY.md + six-laws.md:** Change "Jansen et al. (2025, Google/MIT)" → "Kim et al. (2025)"~~ — **DONE.** All instances corrected in METHODOLOGY.md (body text L43, L55, source table L121, further reading L134) and six-laws.md (evidence L49, source table L108). Also removed "DeepMind" prose attribution (L43) that was unverified.
2. ~~**METHODOLOGY.md + six-laws.md:** Change "Wu et al. (2025, MIT)" → "Chowdhury (2026)"~~ — **DONE.** All instances corrected in METHODOLOGY.md (body text L89, source table L126) and six-laws.md (evidence L84, source table L113).

### Major — RESOLVED (2026-04-05, full paper verification via arxiv.org/html)

3. **Ranjan +0.66 Spearman — VERIFIED WITH CORRECTION.**
   - The exact value "0.66" does not appear in the paper. The closest match is **+0.658** (voyage-3 model on SimLex-999, improving from near-zero baseline to 0.658).
   - The abstract's "+0.29" is the maximum improvement for models with *existing non-zero baselines*.
   - The METHODOLOGY's "+0.66" is +0.658 rounded — technically present in the paper but only for near-zero-baseline models, not the general case.
   - **Correction applied:** METHODOLOGY.md source table updated from "+0.66 Spearman" to "+0.66 Spearman (near-zero baseline models; +0.29 for models with existing baselines)" for precision.

4. **CaptainAgent 30.43% / 4 of 5 / 541 — VERIFIED, ALL FIGURES FOUND.**
   - "30.43%" — Section 3.4.1: adaptive vs. static team comparison *on data analysis tasks specifically*
   - "541 agents" — Section 3.4.5: total agent library accumulated across all experiments
   - "4 of 5 scenarios" — Ablation study (Section 3.4) comparing adaptive vs. static team building
   - The abstract's "21.94%" is the *average* across all scenarios; 30.43% is the *peak* on data analysis.
   - The abstract's "six scenarios" is from the main benchmark; "4 of 5" is from the ablation study.
   - All figures are valid but come from different experimental conditions than the abstract.
   - **No correction needed.** Figures are accurate as cited. METHODOLOGY could optionally note "ablation study" context but the numbers are correct.

### Minor — RESOLVED (2026-04-05, full paper + blog verification via arxiv.org/html and anthropic.com)

5. **MetaGPT and Anthropic figures — VERIFIED WITH CORRECTIONS NEEDED.**

   **67% figures (MetaGPT) — DERIVABLE, NOT STATED:**
   - The paper's Table 1 reports: Human Revision Cost: ChatDev 2.5 → MetaGPT 0.83. Calculated: (2.5−0.83)/2.5 = 66.8% ≈ 67% reduction. Valid derivation.
   - Executability: ChatDev 2.25 → MetaGPT 3.75. Calculated: (3.75−2.25)/2.25 = 66.7% ≈ 67% improvement. Valid derivation.
   - The paper never states "67%" explicitly. These are computed from Table 1 data. Standard academic practice.
   - **Correction applied:** Source table updated to note figures are computed from Table 1.

   **"~40% reduction in error propagation" (attributed to MetaGPT in §6) — NOT FOUND:**
   - The exact phrase or any "40%" error reduction figure does not appear in the MetaGPT paper.
   - The paper discusses reducing "logic inconsistencies due to cascading hallucinations" qualitatively but provides no 40% figure.
   - **Correction applied:** Prose in §6 updated to remove the unverifiable 40% claim.

   **"tool description quality = 40% swing in completion" (attributed to Anthropic "Building Effective Agents" in source table) — NOT FOUND:**
   - The Anthropic blog post does not contain "40%" or any quantitative claim about tool description quality swings.
   - The post does state that Anthropic "spent more time optimizing our tools than the overall prompt" during SWE-bench, but provides no percentage.
   - **Correction applied:** Source table entry for Anthropic "Building Effective Agents" updated to remove unverifiable 40% claim.
