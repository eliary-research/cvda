# Cross-Vendor Disagreement Atlas (CVDA)

> **Status**: in-progress note v1 — manuscript in preparation, target arXiv stat.AP / cs.CL July 2026
> **Snapshot**: 2026-05-04 KST
> **Companion app**: Lucid (https://lucid.currot.com)

## TL;DR

Lucid runs **4 frontier LLMs (Claude, GPT, Gemini, Llama) on the same identity questionnaire**, generating per-vendor personality analyses (MBTI-style type code, archetype, dimension weights) per user. **40 sessions** have all 4 vendor analyses completed.

**Headline finding** — only **31.7% of users (13/41) get the same type code from all 4 LLMs**. **68.3% see at least one vendor disagreement.** 19.5% see 3 distinct types. 4.9% have all 4 LLMs disagree.

**Population**: 3,395 spectrum sessions in 18 days post-launch (April 18, 2026). Of 1,311 analyzed, **66% are AI-skeptical** (`score_ait` ≤ 3 on 1–7 scale, mean 2.599). Spectrum-first onboarding does **not** select on AI-friendliness.

**Per-vendor inductive-bias concentration** ranges from 7 unique type codes (`claude-haiku-4-5`) to 14 (`gpt-4o-mini`) on identical input — direct empirical signal of structurally different vendor priors on personality assignment.

## Why this dataset matters

| Research thread | This dataset's contribution |
|---|---|
| Opinion-diversity collapse [Hashimoto et al., arXiv:2504.08954, 2025] | Direct production measurement on personality inference: 31.7% full agreement vs 68.3% disagreement. Per-vendor type-space ratio 2:1. |
| LLM Council [Karpathy, Dec 2025] | Production-deployed consumer version of multi-model debate: 234 runs, 94% success rate, real users. |
| Centaur/Cyborg field experiments [Mollick et al., NBER w33641, 2025] | N=3,395 (4× P&G N=776), behavioral, multi-vendor. |
| Anti-companion pluralism [Pataranutaporn et al., MIT-SERC 2025] | 68% disagreement-by-default — empirical inverse of Addictive Intelligence concerns. |
| Active personality articulation [Peters & Matz, *PNAS Nexus* 2024] | Inverse of zero-shot inference. 66% AI-skeptical population. |
| Consumer surplus from AI [Cowen & Eggers, MR Aug 2025] | Korean+US dataset gap. |

## Read the paper

[`PAPER.md`](./PAPER.md) — 1-page in-progress note v1 (snapshot 2026-05-04).

## Read the engineering write-up

[`docs/llm_council_in_production.md`](./docs/llm_council_in_production.md) — engineering blog post on production patterns of multi-model orchestration. Reference: Karpathy's LLM Council (Dec 2025) personal project; this is the consumer-deployed version.

## Methodology

Per session, the 74-question identity spectrum is sent (in parallel) to 4 LLMs:

| Lens | Provider | Model |
|---|---|---|
| `claude` | Anthropic | claude-haiku-4-5-20251001 |
| `openai` | OpenAI | gpt-4o-mini |
| `gemini` | Google | gemini-2.5-flash |
| `groq` | Meta (via Groq) | llama-3.3-70b-versatile |

Each generates a structured JSON output (`type_code`, `archetype`, `summary`, `key_insight`, `dimension_weights`). All persisted in `spectrum_analysis_runs`. Cross-vendor agreement rate per session computable directly from `output_json`.

## License

- **Code**: MIT (see [LICENSE](./LICENSE))
- **Data**: CC-BY-4.0 (see [LICENSE-DATA](./LICENSE-DATA))

## Citation

See [CITATION.cff](./CITATION.cff). BibTeX:

```bibtex
@misc{kim2026cvda,
  author       = {Kim, Chanmin},
  title        = {Cross-Vendor Disagreement Atlas (CVDA): A 40-Session Production Field Study of Multi-LLM Personality Inference},
  year         = {2026},
  publisher    = {Eliary Inc.},
  url          = {https://github.com/Eliary-Inc/cvda}
}
```

## Acknowledgments

Methodology builds on Prof. Jong Hee Park's MCMCpack (SNU PSIR; 907 citations on the 2011 *Journal of Statistical Software* paper alone; APSA Best Methodology Paper Award 2010, Best Software Award 2013). Cross-vendor disagreement framing influenced by Andrej Karpathy's LLM Council (Dec 2025) personal project.

## Contact

Chanmin Kim — `developer@eliary.com` — [Lucid](https://lucid.currot.com) | [Currot](https://currot.com)
