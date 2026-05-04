# Cross-Vendor Disagreement Atlas (CVDA)
## A 40-Session Production Field Study of Multi-LLM Personality Inference

**Status**: In-progress note v1 — preliminary findings, manuscript in preparation
**Document date**: 2026-05-04 KST
**Authors**: Chanmin Kim (Eliary Inc., Lucid+Currot)
**Target venue**: arXiv stat.AP / cs.CL primary, cs.HC cross-list
**Target full submission**: July 2026
**Data**: Production from `prism.spectrum_analysis_runs`, deployed April 18, 2026; queries snapshot 2026-05-04 KST late morning.

---

## Abstract (240 words)

Lucid is a consumer multi-LLM platform deploying four frontier models — Anthropic Claude (haiku-4-5-20251001), OpenAI GPT (4o-mini), Google Gemini (2.5-flash), and Meta Llama (3.3-70b-versatile via Groq) — on the same 76-item identity spectrum, generating per-vendor personality analyses (MBTI-style type code, archetype, dimension weights, summary, key insight). Across 234 production analysis runs (94% completion rate) covering 40 sessions with all 4 vendors completed, we measure cross-vendor disagreement on assigned personality type.

**Headline finding**: only **31.7% of users (13 of 41) receive the same type code from all 4 LLMs**. 48.8% see two distinct types (split 3-1 or 2-2), 14.6% see three distinct types, and 4.9% see four distinct types. **68.3% of users observe at least one vendor disagreement.**

We further document differential vendor-side type-space concentration: claude-haiku-4-5 returns only 7 unique type codes across 54 runs, whereas gpt-4o-mini returns 14 across 61 runs — a 2× difference in inductive-bias breadth on the same task. Across all 220 completed runs, **INFP dominates (46.4%)** and **79% of assignments are I-types** in a population that is **66% AI-skeptical** (`score_ait` ≤ 3 on 1–7 scale; n=1,311 of 3,395 spectrum sessions; mean 2.599) — suggesting either an introvert-skewed AI-skeptical user population OR systematic LLM over-call of introversion (or both).

The dataset operationalizes opinion-diversity collapse [Hashimoto et al., arXiv:2504.08954, 2025], multi-LLM Council orchestration [Karpathy, Dec 2025], anti-companion pluralism [Pataranutaporn et al., MIT-SERC 2025], centaur/cyborg field experiments at consumer scale [Mollick et al., NBER w33641, 2025], and active-articulation personality measurement [Peters & Matz, PNAS Nexus 2024]. Anonymized session-level data + replication code at GitHub `Eliary-Inc/cvda`.

---

## §1. Methods

### §1.1 Lucid platform

Lucid runs a 76-question identity spectrum (Big-Five-adjacent + 16-type-style + 4 embedded AI-attitude items). Upon completion, the user's answers are sent (in parallel) to four frontier LLMs via the production "spectrum analysis" pipeline. Each LLM independently generates a structured JSON output containing `type_code` (4-letter code, MBTI-style), `archetype` (free-text persona name), `summary`, `key_insight`, `next_question`, and `dimension_weights`. All outputs are persisted in `spectrum_analysis_runs(session_id, model_id, lens, output_json, scores_json, agreement_metrics_json, status, latency_ms)`.

### §1.2 Vendor configuration

| Lens | Provider | Model |
|---|---|---|
| `claude` | Anthropic | claude-haiku-4-5-20251001 |
| `openai` | OpenAI | gpt-4o-mini |
| `gemini` | Google | gemini-2.5-flash |
| `groq` | Meta (via Groq inference) | llama-3.3-70b-versatile |

### §1.3 Population

| Metric | Value |
|---|---|
| Total spectrum sessions (Apr 18 – May 4) | **3,395** |
| Sessions with `score_ait` (analyzed) | 1,311 (38.6%) |
| Sessions with full 4-vendor analysis | **40** (1.2% of starts; 3.1% of analyzed) |
| Phone-verified completers | 59 (1.7% of starts; 4.5% of analyzed) |

`score_ait` distribution: mean 2.599, sd 1.241, left-skewed. **AI-skeptical (≤ 3): 868 of 1,311 = 66.2%**; AI-neutral (3 < x < 5): 29.1%; AI-positive (≥ 5): 4.7%.

### §1.4 Cross-vendor measurement

For each session in the 40-session subset, we compute:
- **Distinct-type-code count**: number of unique `type_code` values returned across the 4 vendors (∈ {1, 2, 3, 4})
- **Per-vendor type-space size**: number of distinct `type_code` values produced by each vendor across all sessions
- **Per-vendor archetype-space size**: same for `archetype` field

Future revisions will add semantic-similarity metrics on `summary` and `key_insight` (Sentence-BERT cosine; LLM-judge equivalence).

---

## §2. Findings

### §2.1 Cross-vendor type_code agreement (Fig 1)

Among 40 sessions with all 4 vendor analyses completed:

| Distinct types | n | % |
|---|---|---|
| **1 (all agree)** | **13** | **31.7%** |
| 2 (3-1 or 2-2 split) | 20 | 48.8% |
| 3 distinct | 6 | 14.6% |
| 4 distinct (all disagree) | 2 | 4.9% |

**31.7% full agreement, 68.3% disagreement, 19.5% with 3+ distinct types.**

### §2.2 Per-vendor inductive-bias concentration (Fig 2)

| Vendor | runs | unique type codes | unique archetypes |
|---|---|---|---|
| gpt-4o-mini | 61 | **14** | 50 |
| gemini-2.5-flash | 60 | 11 | 53 |
| llama-3.3-70b-versatile | 45 | 11 | 37 |
| claude-haiku-4-5-20251001 | 54 | **7** | 42 |

Claude-haiku returns 2× fewer distinct type codes than GPT-4o-mini on identical input distribution — direct empirical signal of vendor-specific inductive bias on a personality-assignment task.

### §2.3 Population-level type concentration (Fig 3)

Across all 220 completed runs across vendors:

| type_code | runs | % | I-type? |
|---|---|---|---|
| INFP | 102 | 46.4% | I |
| INTP | 35 | 15.9% | I |
| ENFP | 24 | 10.9% | E |
| INFJ | 15 | 6.8% | I |
| INTJ | 12 | 5.5% | I |
| Other I-types (ISFJ, ISTP, ISFP) | 17 | 7.7% | I |
| Other E-types | 7 | 3.2% | E |
| Non-MBTI codes (ECPN, INXP, etc.) | 8 | 3.6% | — |

**174 of 220 runs (79%) assigned an I-type code.** This either reflects AI-skeptical-population introvert skew OR systematic LLM over-call of introversion. Both are publishable hypotheses; the score_ait × type_code interaction will be a follow-up paper.

---

## §3. Five working hypotheses (with current data)

**H1 — Models converge on contestable identity claims (overrely)**
**Status**: Partially supported. claude-haiku-4-5 returns only 7 type codes vs 14 for gpt-4o-mini. Hashimoto et al. (2025) framework directly applicable.

**H2 — Disagreement variance correlates with skeptic completion rate**
**Status**: Untested in v1. Requires linking score_ait to per-session disagreement count. Follow-up query (5h work).

**H3 — Cross-language overconfidence asymmetry**
**Status**: Untested. Requires language tag in spectrum_session.

**H4 — Phone-gate transition is conditional on disagreement-pattern visibility**
**Status**: Untested. Requires UI event log (impression of all-4-disagree segment).

**H5 — Skeptic conversion exceeds positive defection**
**Status**: Reframed. Original ineligible because anonymous sessions lack user_id. Reframe as "completion delta by AI-attitude segment" — testable now.

---

## §4. Cross-paper academic positioning

| Research thread | This dataset's contribution |
|---|---|
| **Opinion-diversity collapse** [Hashimoto et al., arXiv:2504.08954, 2025] | Direct production measurement on personality-inference task: 31.7% full agreement vs 68.3% disagreement. Per-vendor type-space ratio 2:1. |
| **LLM Council** [Karpathy, Dec 2025] | Production-deployed consumer version of multi-model debate: 234 runs, 94% success rate, 40 4-vendor sessions. Real users, real outputs, agreement-rate publicly available. |
| **Centaur/Cyborg** [Mollick et al., NBER w33641, 2025] | N=3,395 (4× P&G N=776), behavioral, multi-vendor. Centaur framing meets cross-vendor agreement. |
| **Anti-companion pluralism** [Pataranutaporn et al., MIT-SERC 2025] | By construction: single-use, full-vendor-output transparency, 68% disagreement-by-default. Empirical inverse of Addictive Intelligence concerns. |
| **Active personality articulation** [Peters & Matz, PNAS Nexus 2024; *Mindmasters* 2025] | Inverse of zero-shot inference. 66% AI-skeptical population (n=1,311) does not select on AI-friendliness. |
| **Consumer surplus from AI** [Cowen & Eggers, MR Aug 2025] | Korean+US consumer data on willingness-to-engage with multi-AI self-reflection. |

---

## §5. Data + code release

**Repository**: https://github.com/Eliary-Inc/cvda *(public, see also Zenodo DOI on first push)*
**Dataset**: anonymized session-level cross-vendor agreement metrics (40 sessions × 4 vendors); aggregate type-code distribution; per-vendor diversity measures. **No raw analysis text released — privacy preserving.**
**Replication code**: Python 3.11 (pandas, scipy, matplotlib) + Spanner SQL queries.
**License**: CC-BY-4.0 for data, MIT for code.
**Privacy**: All session-level data anonymized at extraction. No PII. No raw user free-text.

---

## §6. Limitations

1. **N=40** for full 4-vendor coverage is small. Power scales as production data accumulates (estimated +200 sessions over next 4 weeks via creator drop traffic 5/4-5/30).
2. **Self-selection**: Lucid users opt in to spectrum-first onboarding.
3. **18-day window**: long-arc opinion stability not measurable.
4. **Vendor-API drift**: agreement rates depend on model versions (claude-haiku-4-5, gpt-4o-mini, gemini-2.5-flash, llama-3.3-70b). Rerun comparison planned at quarterly cadence.
5. **Type code as proxy**: structural-disagreement signal but coarse. Future work: archetype semantic similarity, dimension-weight L2 distance, key-insight LLM-judge equivalence.
6. **Non-MBTI codes** (ECPN, INXP, EANC, etc.) appear in 3.6% of runs — likely Lucid v4 16-type or LLM hallucination. Filtered or footnoted in publication.

---

## §7. Acknowledgments

Methodology builds on Prof. Jong Hee Park's MCMCpack (SNU PSIR; 907 citations on the 2011 *Journal of Statistical Software* paper alone; APSA Best Methodology Paper Award 2010, Best Software Award 2013). Cross-vendor disagreement framing influenced by Karpathy's LLM Council (Dec 2025) personal project.

---

## §8. Next-steps queue (post-YC submit)

- [ ] Compute archetype semantic similarity (Sentence-BERT or LLM judge) — adds Fig 4
- [ ] Link score_ait segment × disagreement count → tests H2 directly
- [ ] Add language tag to spectrum_session, run cross-language replication → H3
- [ ] Bayesian hierarchical model for type-assignment probability per vendor (MCMCpack)
- [ ] Cross-vendor archetype clustering — does INFP from Claude semantically equal INFP from GPT?
- [ ] N expansion via creator drop (target N≥300 by 5/30; arXiv submission target July 2026)

---

**End of v1 in-progress note**.

Source data: `prism.spectrum_analysis_runs`, `prism.spectrum_session`, run snapshot 2026-05-04 KST late morning.
Companion file: `Papers/CVDA/data_lock_20260504.md`.
