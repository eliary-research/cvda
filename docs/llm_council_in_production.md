# Lucid: LLM Council in Production

## 2,984 → 3,395 sessions of 4-model debate, and what users do when models disagree

**Date**: 2026-05-04
**Author**: Chan Min Park, Eliary Inc.
**Permalink**: https://github.com/eliary-research/cvda/blob/main/docs/llm_council_in_production.md

---

When Andrej Karpathy released [LLM Council](https://aistify.com/briefs/karpathy-llm-council-multi-model-debate-app/) in December 2025 — a personal project where multiple language models debate-and-synthesize on the same prompt — he framed it as "what software 3.0 actually looks like at the user-facing layer." Multi-model orchestration as the new app primitive.

We've been running the consumer-deployed version of that idea for 18 days. Here is what we found.

---

## The setup

[Lucid](https://lucid.currot.com) is a consumer self-discovery product. The user takes a 74-question identity spectrum (Big-Five-adjacent, MBTI-style, plus 4 embedded AI-attitude items). Their answers are sent — in parallel — to four frontier models:

| Lens | Provider | Model |
|---|---|---|
| `claude` | Anthropic | claude-haiku-4-5-20251001 |
| `openai` | OpenAI | gpt-4o-mini |
| `gemini` | Google | gemini-2.5-flash |
| `groq` | Meta (via Groq) | llama-3.3-70b-versatile |

Each model independently generates structured JSON: a 4-letter `type_code` (MBTI-style), an `archetype` name, dimension weights, summary, key insight. The user sees all 4 outputs side-by-side. There is no synthesis. The disagreement is the product.

Production data lives in `spectrum_analysis_runs(session_id, model_id, lens, output_json, scores_json, status, latency_ms)`. We can ask any cross-vendor question by SQL.

---

## The headline finding

In 18 days, Lucid has accumulated **3,395 spectrum sessions**. 1,311 reached the analysis stage. Of those, 234 generated cross-vendor analysis runs (94% completion rate). **40 sessions have all 4 vendors completed and ready for cross-vendor comparison.**

On the simplest possible question — *do the 4 models agree on the user's MBTI type code?* — here is what the data says:

| Distinct types across 4 vendors | Sessions | Percentage |
|---|---|---|
| **1 (all agree)** | **13** | **31.7%** |
| 2 (3-1 split or 2-2 split) | 20 | 48.8% |
| 3 distinct types | 6 | 14.6% |
| 4 (all disagree) | 2 | 4.9% |

**Only 31.7% of users get the same type code from all 4 LLMs.** Two thirds of users see at least one vendor disagreement. One in five sees three distinct types. One in twenty sees four — different LLMs assigning different MBTI types to the same answers.

If you came to multi-model orchestration expecting consensus, the data does not give it to you.

---

## Per-vendor inductive bias is not subtle

Aggregate the 220 completed runs by vendor. Look at how many distinct type codes each vendor returns across all sessions:

| Vendor | Runs | Unique type codes | Unique archetypes |
|---|---|---|---|
| `gpt-4o-mini` | 61 | **14** (most diverse) | 50 |
| `gemini-2.5-flash` | 60 | 11 | 53 |
| `llama-3.3-70b-versatile` | 45 | 11 | 37 |
| `claude-haiku-4-5-20251001` | 54 | **7** (most concentrated) | 42 |

GPT-4o-mini deploys twice the type-space concentration of Claude-haiku-4-5 on identical input. Same questionnaire, same population, structurally different inductive bias. This is a vendor-prior measurement, not a correctness measurement, but the vendor-prior is the thing affecting the user.

---

## And the population is the resistant segment

We measure users' baseline AI attitude with 4 embedded items inside the 74-question spectrum. The composite (`score_ait`, 1–7 Likert) lands at **mean 2.599 (SD 1.241)** for the 1,311 analyzed sessions — left-skewed, AI-skeptical. **66% of users (`score_ait` ≤ 3)** are skeptical; only 4.7% are positive (≥ 5).

Spectrum-first onboarding does not select on AI-friendliness. Lucid reaches the resistant segment, not the AI-easy one. And the resistant segment is exactly the population that matters most for any thesis about AI adoption — the converted-easy users were already going to convert.

---

## What this means for "Software 3.0"

A few takeaways from running Karpathy's idea in production:

1. **Disagreement is the signal, not the noise.** When you ship 4-vendor outputs side-by-side, the user reads disagreement as nuance, not as a defect. This requires giving up "the LLM said" framing and adopting "the LLMs said" framing. Different speech act.

2. **Inductive-bias asymmetry is observable per-vendor.** You don't need a benchmark, you need production logs. Which vendors collapse to which archetype space, on what input distribution, is directly measurable in any multi-model deployment.

3. **AI-skeptical users complete the spectrum at non-trivial rates.** 38.6% of starts reach the analysis stage; the analysis-reaching population is 66% skeptical. Reading this two ways: (a) skeptics finish to disconfirm AI; (b) the spectrum's neutral self-articulation framing is non-threatening to skeptics. We don't know which yet — that's the H2 hypothesis in the working paper.

4. **The agreement question scales with N.** N=40 for all-4-vendor sessions is small. We expect N≥300 over the next 4 weeks via creator-drop traffic; the agreement-rate distribution may look different with 10× the data. The framework will hold; the proportions will refine.

---

## What's next

- **Q-by-question agreement decomposition**: cross-vendor agreement rate per question category (Big-Five vs MBTI vs Prism Original axes). Hypothesis: subjective dimensions converge; behavioral dimensions diverge.
- **Cross-language replication** on Korean-origin user subset (~30% of sessions) — Hashimoto et al.'s 2025 multilingual overconfidence finding gives a direct test.
- **Score_ait × disagreement correlation**: do skeptical users see *more* disagreement in their own outputs (selection-and-engagement effect)?
- **Bayesian hierarchical model** of type-assignment probability per vendor (MCMCpack, after Park).

The 1-page in-progress paper is at [`PAPER.md`](../PAPER.md). The full preprint targets arXiv stat.AP / cs.CL by July 2026.

---

## Read more

- Working paper: https://github.com/eliary-research/cvda
- Try Lucid: https://lucid.currot.com
- Companion paper *The Single-Creator Trap*: https://github.com/Eliary-Inc/single-creator-trap
- Companion paper *Signal Inflation Hypothesis*: https://github.com/Eliary-Inc/signal-inflation-hypothesis

— Chan Min Park, Eliary Inc.
