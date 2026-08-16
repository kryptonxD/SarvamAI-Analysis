# Demand log

Where I looked for people actually asking for these things, and what I found. This file exists because the winning idea scores **4 out of 10 on demand** in [Validation.md](Validation.md), and a number that low should come with its evidence attached.

The short version: for the quantization idea, I found the problem discussed and never found anyone asking for the fix. I've kept looking rather than quietly dropping the criterion.

Searches run 7–8 August 2026, re-checked 16 August.

---

## Idea 1 — Indic-calibrated quantization

### What I found

**One source raises it directly.** The mtrajan teardown [29] states that an Indic-calibrated quantization recipe "does not exist publicly" and explains why uniform INT8 damages the embedding and vocabulary layers where Sarvam's token efficiency lives. That's it. One person, one post, March 2026.

**The mechanism is discussed in academic literature, not by users.** MoEQuant [60] and Compass-v3 [61] both address expert-calibration imbalance rigorously. But that's researchers describing a phenomenon, not practitioners requesting a build. I want to be precise about this distinction because conflating the two was the easiest available route to a passing score, and I didn't take it.

**Community quantization activity exists.** Community GGUF conversions of Sarvam models are published, and general-purpose quantization guides discuss hand-rolling IndicGLUE and Flores-200 retention benchmarks. So people *are* compressing these models. What I could not find is anyone reporting that compression damaged their Indic output.

### What I searched and came up empty

| Where | What I looked for | Result |
|---|---|---|
| Hugging Face | Any Indic-calibrated recipe, imatrix corpus, or per-language retention table for any Indic model | Nothing |
| GitHub | Issues or PRs on llama.cpp about multilingual imatrix calibration for Indic scripts | Nothing Sarvam-specific |
| Hacker News | Complaints about quantized Sarvam model quality | The 105B thread [55] criticises the model generally, never quantization specifically |
| Reddit | `site:reddit.com Sarvam AI`, plus LocalLLaMA-targeted queries | No substantive Sarvam threads at all |
| Model card discussions | Quality complaints on the first-party GGUF repos | None found |

### The honest read

**Nobody is complaining.** Sarvam ships first-party GGUFs, 591 people pulled one last month [59], and not one of them has publicly said their Odia or Assamese output degraded.

There are two explanations and I can't separate them from outside:

1. The effect is smaller on this architecture than on Compass-v3, and there's nothing much here
2. The effect is real but invisible, because nobody measures per-language quality on their own deployment. You'd need a BF16 baseline and a 22-language eval sweep to notice, which is precisely the work nobody does

Silent quality loss is by definition the thing nobody files an issue about. That's an argument for why absence of complaint is weak evidence here. But it is *also* exactly the argument a motivated analyst would reach for to explain away inconvenient data, and I'm aware of that. It's why the score stayed at 4 instead of being talked upward.

This is the weakest joint in the whole analysis. If you're evaluating this work, press here.

---

## Idea 2 — Indus fast path

Demand here is **real and dated**, unlike idea 1.

App Store reviews for Indus, February–April 2026 [40], carry specific complaints:

- A review titled *"App very slow while answering"*, saying latency "makes real-time usage difficult"
- Response time *"significantly slower compared to other AI applications"*
- Output *"returning xml tags in response, where the response should be … text"*
- *"so many errors in almost every conversation"*
- Audio input that *"cannot listen to audio longer than 30 seconds"*

Ratings sit at 4.7★ across 453 App Store ratings and 4.6★ across 7.4K Play ratings [39][40], so this is a minority complaint inside a generally positive reception. But it's the same complaint repeatedly, about the same thing, and it persisted into mid-2026 re-checks.

**What I don't have:** a first-party latency measurement, or any way to tell whether serving capacity improved after the compute-constrained launch [37]. I know the root cause (reasoning can't be disabled, everything routes to the 105B) and I know people complain. I don't know the magnitude.

---

## Idea 3 — Ollama support for the 30B

The most explicit demand of the three, and it's on the record with dates.

Ollama PR #14703 [32], opened March 2026 and still open:

- *"Any timeline when will this be merged?"* (23 April 2026)
- *"We need sarvam models through Ollama."* (29 April 2026)
- A further request for a merge timeline (1 July 2026)
- *"@Swagatade bro resolve conflicts!"* (8 July 2026)

Related: llama.cpp issue #20175 (since closed by the May merge) and Ollama issue #14319 (open).

So: a handful of named people, asking repeatedly across four months, for one specific thing. That's thin in absolute terms, a PR thread rather than a groundswell, but it's unambiguous, and it's more demand signal than the winning idea has.

The awkward part is that the idea with the clearest demand scores lowest overall (53.2), because the gap it closes is now narrow. Demand and impact aren't the same axis.

---

## Where I looked and found nothing at all

Recording these because an absent result is still a result.

- **G2, Capterra, TrustRadius, Trustpilot, Product Hunt.** No Sarvam-specific review page exists. One promising G2 result turned out to be Kore.ai. For a company at this funding stage that's unusual, and it removes the single richest source of practitioner complaints I'd normally use.
- **Reddit.** Nothing substantive, including LocalLLaMA-targeted searches. Developer sentiment surfaced on Hacker News [55] and Teamblind [30] instead.
- **Stack Overflow.** No Sarvam-tagged questions with usable pain.
- **X and LinkedIn.** The well-known critiques are reported secondhand through news [24][26]. I didn't fetch originals, so I haven't quoted them as though I had.

The absence of a review footprint is worth flagging on its own. It means every pain in [Pain_Registry.md](Pain_Registry.md) rests on a narrower base than I'd like: one expert teardown, one GitHub thread, one app-store review set, one HN thread, and a government portal counter. Five sources, each thin. I'd treat the *pattern* across them as meaningful and any individual data point as fragile.

---

## Summary

| Idea | Demand evidence | Strength |
|---|---|---|
| Indic quantization | One expert post; academic literature on the mechanism; zero user requests | **Weak — 4/10** |
| Indus fast path | Repeated dated app-store complaints, persisting into mid-2026 | Moderate |
| Ollama support | Named users asking repeatedly on an open PR across four months | Clearest, but narrow scope |

The idea I believe in most technically has the least demand behind it. That tension is the whole reason it fails my own gate, and I'd rather leave it visible than resolve it in my favour.
