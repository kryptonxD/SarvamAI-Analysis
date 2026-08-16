# What's actually left to build

Three ideas, in descending order of how much I believe in them. None of them clears my own scoring gate — see [Validation.md](Validation.md) for the arithmetic and why I'm publishing anyway.

---

## First, what got killed

I started with five candidate ideas. Before scoring any of them I checked whether Sarvam had already shipped it. Three had been.

| Candidate | Verdict | What the artifact shows |
|---|---|---|
| Native tool-calling and structured output | **Already shipped** | The API exposes `tool_choice` and strict `response_format: json_schema`. The 30B card lists BrowseComp 35.5 and τ² Bench 45.7 |
| Publish GGUF builds | **Already shipped** | First-party `sarvamai/sarvam-30b-gguf`, Q4_K_M across six shards, 19.6 GB, in a "sovereign models" collection dated 23 March [59] |
| FP8 build for vLLM and SGLang | **Already shipped** | `sarvamai/sarvam-30b-fp8`, with both serve commands published |
| Mainline local-runtime support | **I got this wrong twice** | llama.cpp merged 9 May; the first-party GGUF uses a different architecture entirely. See [Corrections.md](Corrections.md) |
| Indic-calibrated quantization | **Still open** | No published calibration corpus, no per-language retention numbers for any quantized build, first-party or community |

That's the honest headline of this whole exercise: **most of what I was going to propose, Sarvam had already done.** The criticism I built on was real when it was written in March 2026 and had largely stopped being true by May. What survives is narrower and I'd rather show you a narrow true thing than a broad stale one.

Worth saying plainly before the pitch: Sarvam already does mixed-precision quantization engineering. The 30B card documents MXFP4 mixed-precision inference tuned for Apple Silicon, claiming 20–40% higher throughput on an M3. What follows is not "start caring about quantization." It is "the calibration corpus is the part nobody has published."

---

## 1. Indic-calibrated quantization

**Score 63.0/100 against a 70 cutoff — the strongest survivor, and still a fail.**

### The mechanism

Start with the config rather than the marketing. Sarvam-30B: 128 experts per MoE layer, top-6 routing, one shared expert, 19 layers with the first dense, vocabulary 262,144, untied embeddings, sigmoid router. Work out where the parameters actually sit:

```
routed experts    3 × 4096 × 1024 × 128 experts × 18 MoE layers = 28.99 B
embed + lm_head   262,144 × 4,096 × 2  (untied)                 =  2.15 B
attention                                                        =  0.68 B
shared experts + dense FFN                                       =  0.33 B
                                                          total  = 32.14 B
                                            (Hugging Face reports 32B)

routed experts    = 90.2% of all weights
embed + lm_head   =  6.7%
experts per token = 6 of 128 = 4.7%
```

Ninety percent of the model is routed experts, and any single token touches under five percent of them. That's the point of the architecture. It's also the exposure.

Importance-matrix quantization runs a calibration corpus through the model and records which weights matter. In a dense model every weight sees every token. Here, an expert only accumulates statistics if the router sends tokens to it. Feed in English — wikitext is the default in most llama.cpp workflows — and you measure the experts that English routes to.

### The failure mode is thin statistics, not missing ones

This distinction matters, and getting it wrong is how the argument falls apart.

The *empty* case is already guarded. llama.cpp refuses to save importance data for an experts tensor if any single expert has none, because quantizing from all-zero statistics can produce NaNs. Hit that guard and you fall back to plain K-quant, which is roughly uniform and not biased against any particular language.

The unguarded case is the one in between. An expert that receives enough English tokens to clear the completeness check, but too few to estimate importance reliably, gets quantized against noisy statistics that look valid. That is what shifting from an English to a 22-language distribution actually changes, and it's the problem the literature names.

### This is an established subfield

**MoEQuant** (arXiv 2505.03804, ICML 2025) states it in one line: inter-expert imbalance is the uneven distribution of samples across experts, leading to insufficient and biased calibration for less frequently used experts. Their remedy is expert-balanced sampling — constructing a calibration set with balanced expert coverage [60].

Then the number that matters. **Compass-v3** (arXiv 2509.09121), a 245B multilingual MoE deployed at Shopee, reports what naive quantization did to its low-resource languages against a BF16 baseline: **Thai fell 10.17%, Vietnamese 11.75%**. Their expert-aware scheme, which oversamples calibration data toward under-represented experts, held every language within **2.5%** — and Portuguese came out **1.05% above** BF16 [61].

So the effect is real, quantified, and fixable — in a different low-resource language family. What nobody has published is the Indic instantiation.

**Where the analogy is weak, stated up front.** Compass-v3 uses fewer, larger experts with FP8 activation quantization. Sarvam-30B uses 128 small experts with top-6 routing at 4-bit weight quantization. Different regime. The mechanism transfers; the magnitudes don't. If anything, 128 experts spread the same calibration tokens thinner than 16 would, which points one way — but that's my inference, not a measured result, and it's exactly what the test below is for.

### What it costs

Two interventions, priced separately. Recalibration costs compute once and nothing at inference. Holding sensitive tensors at higher precision costs file size forever.

My first draft priced the second against an assumed uniform 4-bit baseline. That was wrong, and the shipped file says so:

```
sarvamai/sarvam-30b-gguf:  32B params · Q4_K_M · 19.6 GB

  19.6 GB × 8 / 32.14B   =  4.88 bits/weight effective
  uniform Q4_K @ 4.5bpw  =  18.1 GB
                            ---------
  already spent on higher precision:  ~1.5 GB
```

K-quant is **already** mixing precision upward. Q4_K_M conventionally promotes `output.weight` to Q6_K, and by default doesn't apply the importance matrix to it at all. If that holds here, half the protection I was proposing is already shipped and only the token embedding remains:

```
                    both tensors (2.15B)      token_embd only (1.07B)
  BF16                +3.09 GB  +15.8%          +1.54 GB   +7.9%
  Q8_0                +1.07 GB   +5.5%          +0.54 GB   +2.7%
  Q6_K                +0.55 GB   +2.8%          +0.28 GB   +1.4%
```

The right-hand column is the likely real cost: about **1.4%** on file size, not the 17% my first draft implied. Cheap enough that the interesting question stops being whether to protect the embeddings and becomes whether recalibration alone moves the numbers.

*Unverified:* I haven't run `gguf-dump` on the shipped shards to read actual per-tensor types. The 4.88 bpw figure proves upward mixing exists but not where it's spent. Ten-minute job for anyone inside.

### What I'd actually do

**Recalibrate.** Build the importance matrix from a corpus spanning all 22 languages plus code-switched text, using expert-balanced sampling so every expert clears a coverage threshold rather than merely a non-zero check.

**Then protect.** Router and shared expert first — tiny, and the router decides everything downstream. Then whichever of `token_embd` and `output.weight` the dump shows still at 4-bit, at Q6_K. Routed experts stay low, because that's where the 29B lives.

**Publish.** Per-language deltas against BF16 on Flores-200 and IndicGLUE, in the model card. Per language, not averaged. If Hindi holds and Odia collapses, a mean looks fine while the model is broken for the users least served by everything else.

### The experiment, with the kill condition set in advance

The mechanism is documented and the effect is quantified elsewhere. What isn't measured is the magnitude on this model, in these languages, at 4-bit. That's a bounded run.

**Cheapest first step.** Instrument the router and count, per layer, how many of the 128 experts fall below a usable activation threshold under an English calibration corpus. No quantization needed. That number alone either supports the thesis or ends it.

**Then build three,** at the same bit budget: (a) default English imatrix; (b) 22-language expert-balanced imatrix; (c) as (b) plus router, shared expert, and whichever embedding tensor the dump shows unprotected, at Q6_K.

**Measure** per-language delta against BF16 across all 22 languages on Flores-200 and IndicGLUE.

**Continue if** (b) beats (a) consistently *and* the gain lands in low-resource languages rather than only the two best-resourced ones. Compass-v3's low-resource languages moved 7–12 points, so there's a rough scale to compare against.

**Kill if** expert coverage under English calibration is already adequate, or (a) and (b) land inside noise. Then the effect doesn't exist at the claimed severity here and this idea should be dropped.

### Three reasons this fails, in the order they worry me

**Nobody is complaining.** Sarvam has shipped first-party GGUFs and people are running them. If quantization visibly wrecked Indic output you'd expect a loud thread, and there isn't one. Either the effect is smaller here than in Compass-v3, or it's real and invisible because nobody measures per-language quality on their own deployment. Both are plausible and I don't know which. This is scored honestly as 4/10 on demand in [Validation.md](Validation.md), and it's the single weakest point in the argument.

**It may already be handled and undocumented.** The shipped file already spends ~1.5 GB on higher-precision mixing, and Sarvam already does MXFP4 work for Apple Silicon. Their calibration may well have been multilingual and simply never written down — in which case the gap is disclosure, not engineering. That's a much smaller idea and I can't distinguish the two from outside.

**It defends a moat without selling anything.** This is a recipe and a set of checkpoints. Someone could look at the same evidence, put the engineers on the consumer app instead, and I wouldn't have a strong rebuttal.

### Credit

The observation that no Indic quantization recipe exists isn't mine. It comes from an independent teardown by **mtrajan**, who also did the conversion work and published `mtrajan/sarvam-30b-GGUF` [29].

His own model card then supersedes the central technical claim of that critique: sigmoid routing was never the blocker, because llama.cpp already supports it and GLM4 uses it. The real blocker was a missing class registration, tensor mappings and graph builder — all supplied by PR #20275 in 387 lines [57]. The card's status flow still reads "PR pending," which was accurate when written and stopped being accurate on 9 May.

---

## 2. Indus fast path

**Score 60.8/100. Untouched by my corrections — it was the same before and after.**

**The pain:** P05 and P08. Reviewers report response times "significantly slower compared to other AI applications" and that the latency "makes real-time usage difficult." XML tags occasionally leak into responses [40].

**The root cause is verifiable and slightly absurd.** The app always runs the **105B in reasoning mode with no way to turn reasoning off**, so even trivial queries pay full reasoning latency. Everything routes to the 105B rather than the far cheaper 30B with its 2.4B active parameters. And the chat surface doesn't apply the API's own `response_format` or JSON mode, so template tokens leak — *even though the API already supports* `reasoning_effort` and structured output.

The fast path exists at the API layer. Indus neither exposes nor applies it.

**What to build.** A "Fast / Think" toggle defaulting to Fast, exposing `reasoning_effort`. Auto-routing so simple queries go to the 30B or a no-reasoning 105B while complex ones get reasoning. Structured-output enforcement so XML never surfaces. Token streaming for perceived latency.

**Why it isn't first.** The competitor comparison is the strongest part of this idea — OpenAI and Google default to instant with thinking behind a toggle, and Indus does the exact inverse. But the evidence for the *magnitude* is app-store review sentiment, not measurement. I have no first-party latency number and can't confirm whether serving capacity has caught up since the compute-constrained launch [37]. The root cause is verified; the size of the prize isn't.

---

## 3. Ollama support for the 30B

**Score 53.2/100, down from 55.8 after my corrections. The weakest of the three, and I've scored it down rather than defending it.**

The 30B still fails on stock Ollama with `unknown model architecture: 'sarvam_moe'`. The community PR #14703 is open, conflicted, and marks the architecture as requiring Ollama's own Go engine — so llama.cpp's May merge doesn't propagate [32].

Demand is explicit and dated on that thread: *"Any timeline when will this be merged?"* and *"We need sarvam models through Ollama."*

**What it takes.** Adopt and drive the PR, publish an official `ollama.com/library/sarvam-30b` entry with a correct Modelfile, chat template and stop tokens, and ship a verify-your-runtime script that diffs logits against a reference SGLang run to retire the silent-degradation concern.

**Why it dropped.** When I started this was going to be the flagship recommendation. Then llama.cpp merged in May and I found the first-party GGUF uses `bailingmoe2` entirely — so there are already two working local paths and the remaining gap is narrower than I'd scored it. This is now a DevRel and upstream-engineering task, not a product opportunity. There's no AI in it, and pretending otherwise would be dressing up a merge conflict as machine learning.

It still matters more than 53.2 suggests, for one reason: grassroots local-LLM mindshare is where open-model reputation gets won, and it's exactly what Sarvam is short of. One command is the difference between a model people try and a model people read about.

---

## The ask

If anyone inside Sarvam has already measured per-language retention on the GGUF or MXFP4 builds, I'd like to know I'm wrong. That result would be more useful to me than this analysis being right.
