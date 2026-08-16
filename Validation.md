# Scoring

Three surviving ideas across eleven weighted criteria. Cutoff set at 70 before scoring. **Zero of three pass.**

I'm showing the arithmetic and every revision because the revisions are the interesting part — both corrections moved numbers, and they moved them *down*.

---

## Results

| Idea | 7 Aug | After corrections | What moved |
|---|---|---|---|
| **Indic-safe quantization** | 60.8 | **63.0** | Feasibility 7→9 (a named subfield with a quantified precedent). Confidence 5→6. Impact 6→7. Differentiation cut 7→5, because the technique is published and only the Indic instantiation is missing. |
| **Indus fast path** | 60.8 | **60.8** | Unchanged. |
| **Ollama support for the 30B** | 55.8 | **53.2** | Down. llama.cpp merged in May and the first-party GGUF uses a different architecture entirely, so the remaining gap is narrower than I'd scored it. |

Nothing clears 70. The best idea fails by seven points.

---

## The winning idea, scored in full

Eleven criteria, weights summing to 1.00, weighted total × 10.

| Criterion | Weight | Score | Reasoning |
|---|---|---|---|
| User impact | 0.14 | 7 | 90% of weights are exposed to the calibration question |
| Business impact | 0.12 | 5 | Protects the moat. No revenue line I can point at |
| **Demand signal** | 0.12 | **4** | **Nobody has asked for this. Not one thread.** |
| Strategic fit | 0.10 | 7 | They already ship MXFP4 mixed precision |
| Technical feasibility | 0.10 | 9 | Named subfield, published method, quantified precedent in another language family |
| AI fit | 0.08 | 8 | A real ML technique, appropriately applied |
| Engineering simplicity | 0.08 | **5** | **The 22-language eval sweep is the expensive part** |
| Market timing | 0.08 | 6 | Window open. Nothing forcing it |
| Competitive differentiation | 0.08 | **5** | **Cut twice. The method is published; only the Indic run is missing** |
| Not already built | 0.06 | 8 | No published Indic calibration set or per-language numbers |
| Implementation confidence | 0.04 | 6 | A method to follow; the decisive run hasn't happened |

```
(7×.14) + (5×.12) + (4×.12) + (7×.10) + (9×.10) + (8×.08)
       + (5×.08) + (6×.08) + (5×.08) + (8×.06) + (6×.04)

= 0.98 + 0.60 + 0.48 + 0.70 + 0.90 + 0.64 + 0.40 + 0.48 + 0.40 + 0.48 + 0.24
= 6.30
→ 63.0 / 100          cutoff 70          FAIL
```

---

## The two scores I refused to move

**Demand stayed at 4 through both revisions.** This was the tempting one. Finding a named subfield and a quantified precedent for a *mechanism* is not the same as finding anyone asking for the *fix*, and collapsing those two would have been the easiest available route to a passing score. It's the difference between "this problem is real" and "someone wants this solved," and only the second one is demand. [Demand_Log.md](Demand_Log.md) is the full record of looking and not finding.

**Differentiation went the other way: 7 → 6 → 5**, as the prior art got clearer. Each time I verified something, the idea looked *less* novel, because MoEQuant and Compass-v3 had already done the hard conceptual work. I could have argued the Indic instantiation is the novel part — it is — but the technique isn't mine and the score should say so.

Verification should be able to lower your own numbers, not only raise them. If a research pass only ever moves scores up, it isn't research, it's justification.

---

## What I checked, and where

The lesson from my corrections is in the third column. Searching a topic returns the March 2026 narrative indefinitely. Searching the artifact returns the current state. Every row below is an artifact.

| Checked | Question | Artifact | Answer |
|---|---|---|---|
| 8 Aug | Did llama.cpp ever merge Sarvam support? | release `b9093` | **Yes.** 9 May 2026, PR #20275 |
| 8 Aug | What architecture does the shipped GGUF declare? | `sarvam-30b-gguf` | `bailingmoe2` — not `sarvam_moe` |
| 8 Aug | Can you `ollama run` the safetensors path? | ollama #14703 | **No.** Open, conflicted, needs Ollama's own engine |
| 8 Aug | Real routing and vocab numbers? | `sarvam-30b/config.json` | 128 experts, top-6, 1 shared, 19 layers, vocab 262144, untied embeddings, sigmoid router |
| 8 Aug | Do my parameter sums match reality? | HF model metadata | I computed 32.14B; HF reports 32B |
| 8 Aug | Is expert-calibration imbalance a known problem? | arXiv 2505.03804 | **Yes.** Named subfield, ICML 2025 |
| 8 Aug | Has anyone quantified it for low-resource languages? | arXiv 2509.09121 | **Yes.** Thai −10.17%, Vietnamese −11.75% |
| 7 Aug | Any Indic-calibrated recipe anywhere? | HF, GitHub, web | **None found** |
| 16 Aug | Is the ICML claim actually true, or did I overstate it? | icml.cc poster page | **True.** Poster at ICML 2025 |
| 16 Aug | Are the Compass-v3 figures right? | arXiv 2509.09121 | **Correct**, including Portuguese at +1.05% over BF16 |
| 16 Aug | Has the download count moved? | `sarvam-30b-gguf` | 482 → **591** last month; 21 → **22** likes |
| 16 Aug | Has the Series B fully closed? | Sarvam PR, press | **No.** Still a $234M first close against $300M |

---

## Why publish a failing idea

Three reasons, and I'd rather state them than let the score sit there unexplained.

**The cutoff is mine, not a law.** I set 70 before scoring to stop myself rationalising a favourite. It did its job — it failed my favourite. But a 63 with a documented kill condition and a bounded first experiment is more useful than a 78 assembled from generous self-scoring.

**The failure is informative.** The idea fails mainly on demand (4) and differentiation (5). That's a precise diagnosis: the mechanism is real and published, the Indic version is genuinely missing, and *nobody is asking for it*. If you're inside Sarvam, that combination tells you exactly what to check first — instrument the router, count under-covered experts, and find out in an afternoon whether the effect exists at all.

**Zero of three passing is the actual finding.** Sarvam closed most of its March-2026 deployment gap before I got there. A portfolio where everything scores 80+ against a company that just shipped its way out of the problem would mean I hadn't checked.

---

## What would change my mind

If someone ran the router-coverage count and every expert cleared the threshold under English calibration, idea 1 dies and should. If a first-party per-language retention table appeared on the model card, it dies for a different reason — already handled. And if Ollama merged #14703, idea 3 drops to zero.

All three are cheap to check from inside the company, and none of them are checkable from where I'm standing.
