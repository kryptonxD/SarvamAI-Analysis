# Sarvam AI — an outside read

An unsolicited product analysis of [Sarvam AI](https://www.sarvam.ai), written from public sources only. No affiliation with the company, no interviews, no internal data.

I started with five ideas for things Sarvam could build. Three were already shipped. I got a fourth wrong (twice), and correcting it produced the only one I think is worth reading. The best surviving idea scores **63 out of 100** against a cutoff of 70 that I set before scoring. It fails. I'm publishing the working anyway, because the corrections turned out to be the most useful thing in here.

**Ritik Barnwal** · ritikbarnwal028@gmail.com
Claims last re-verified **16 August 2026**.

---

## Start here

If you read one file, read [Corrections.md](Corrections.md). It's the shortest and it tells you whether I check my own work.

| File | What's in it |
|---|---|
| [Corrections.md](Corrections.md) | Every claim I got wrong, dated, with what replaced it |
| [Research_Notes.md](Research_Notes.md) | Company, product, market and customer background |
| [Pain_Registry.md](Pain_Registry.md) | Ten complaints mined from public sources, scored |
| [Competitive_Intel.md](Competitive_Intel.md) | Feature comparison against Krutrim, CoRover, Gnani and the closed models |
| [Opportunities.md](Opportunities.md) | The three ideas that survived, with the reasoning chain for each |
| [Validation.md](Validation.md) | Three ideas across eleven weighted criteria, all score revisions, arithmetic shown |
| [Demand_Log.md](Demand_Log.md) | Where I looked for people actually asking for this, and what I found |
| [Sources.md](Sources.md) | Numbered sources with URLs, dates, and how far I trust each |

---

## The short version

Sarvam is India's best-funded sovereign-AI company: selected under the IndiaAI Mission to build the country's first from-scratch foundation model, and unicorn-valued at $1.5B on a $234M first close led by HCLTech. Its 30B and 105B are sparse mixture-of-experts models under Apache-2.0, with a tokenizer built for Indian scripts.

Nearly all public criticism of those open weights traces to a cluster of March 2026 sources: no GGUF, no local runtime, no CPU path. I built ideas on that criticism, then checked each claim against the actual artifact instead of the topic. Most of it had been fixed:

- llama.cpp merged native `sarvam_moe` support on 9 May 2026 (PR #20275, release `b9093`)
- Sarvam ships first-party GGUF and FP8 builds
- The API has tool-calling and strict structured output

What's still genuinely open is narrower: **nobody has published an Indic-calibrated quantization recipe.** Ninety percent of Sarvam-30B's weights sit in 128 routed experts, six fire per token, and importance-matrix calibration only measures the experts your corpus routes to. Every public calibration corpus is English.

That mechanism is documented. [MoEQuant](https://arxiv.org/abs/2505.03804) (ICML 2025) names it, and [Compass-v3](https://arxiv.org/abs/2509.09121) quantified it on a different low-resource language family, where naive quantization cost Thai 10.17% and Vietnamese 11.75% against a BF16 baseline. What nobody has done is the Indic instantiation.

I can't run the experiment that would settle it. [Opportunities.md](Opportunities.md) sets out what it would take and what result should kill the idea.

---

## What I don't have

No interviews, no internal metrics, no roadmap access, and no GPU. Everything here is public-source inference, which caps how far you should trust it. Where I couldn't verify something, I've said so in the file rather than filling the gap.
