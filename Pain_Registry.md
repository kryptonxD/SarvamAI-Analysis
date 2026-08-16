# Pain Registry

Ten complaints about Sarvam, mined from developer writeups, GitHub, model hubs, app stores, forums and analyst notes. Each carries a real source with a date, and a score.

**Important:** this registry was built from evidence dated **March–April 2026**, and several of these pains have since been fixed. I've kept them in with their status marked rather than deleting them, because the pattern of what got fixed and how fast is itself informative — and because deleting them would hide the fact that my own first pass was built on stale criticism. [Corrections.md](Corrections.md) has the full account.

Status re-checked **16 August 2026**.

---

## How I scored these

```
Opportunity Score = (frequency × severity × business impact) ÷ 30
```

Frequency is 1 for isolated, 2 for recurring, 3 for pervasive — where pervasive means independently corroborated across at least three sources. Severity and business impact are 1–10, judged by me. Maximum raw score is 300, divided by 30 to land on a 0–10 scale.

The scoring is a sorting aid, not a measurement. It encodes my judgement about severity and impact, and someone inside the company with real usage data would score several of these differently.

---

## The ten

### P01 — Open-weight models had no mainstream local runtime
**Score 7.2** · pervasive · severity 8 · impact 9 · **mostly fixed**

The complaint, as of March 2026: no merged Ollama or llama.cpp support, no GGUF for the 30B or 105B. Users on the Ollama PR thread asked repeatedly for a merge timeline — *"Any timeline when will this be merged?"* and *"We need sarvam models through Ollama."* CB Insights flagged the same friction: the absence of GGUF formats and the lack of day-one vLLM support [29][32][33][55].

**Where it stands now.** Largely closed, and I got this wrong twice before getting it right. llama.cpp merged native `sarvam_moe` support on 9 May 2026 (PR #20275, release `b9093`) [57][58]. Sarvam ships first-party GGUF and FP8 builds [59]. The residual is Ollama specifically: PR #14703 is still open with merge conflicts and needs Ollama's own Go engine, so the llama.cpp merge doesn't carry over [32].

This scored highest of the ten, and it's the one that turned out to be most out of date. That's the argument for checking artifacts over topics.

---

### P04 — Weak open-model adoption and a lingering "wrapper" reputation
**Score 4.8** · pervasive · severity 6 · impact 8 · **open**

Sarvam-M drew somewhere between 23 and 720 downloads in its first days depending on which count you trust, and an investor called the performance "embarrassing" [24][26][27]. The government's own AIKosh portal page for Sarvam-105B shows **0 downloads against 52 views** [36]. A Hacker News commenter judged the model "not currently good" and roughly two years behind the frontier [55].

**Where it stands now.** Still open, and it's the pain I'd worry about most if I worked there. The first-party 30B GGUF records **591 downloads in the last month and 22 likes** [59]. That's real pickup rather than zero, but it is a very small number for a government-backed sovereign flagship at a $1.5B valuation.

The from-scratch 30B and 105B were themselves the strategic answer to the "wrapper" charge, so the engineering response has happened. The adoption response hasn't.

---

### P02 — No CPU path, and the air-gapped licence check fails
**Score 3.7** · recurring · severity 7 · impact 8 · **largely fixed**

Indian enterprise runs substantially on Intel Xeon CPU clusters, but the models were optimised for H100s. Worse, NVIDIA NIM containers validate licences on startup, so they fail in genuinely air-gapped environments — exactly the UIDAI and RBI-style deployments Sarvam markets to [29]. Self-hosting guidance recommended eight high-end GPUs [25].

**Where it stands now.** GGUF plus llama.cpp gives a CPU path with no licence phone-home — omit `-ngl` and it runs on CPU. That sidesteps the NIM problem entirely. The caveat is throughput: the 105B on CPU is memory-bandwidth-bound and impractical, so the 30B with its 2.4B active parameters is the realistic frugal target.

---

### P08 — Output quality gaps
**Score 2.8** · recurring · severity 6 · impact 7 · **partly fixed**

A Hacker News user reported confident hallucination and no tool calling [55]. An App Store reviewer noted the app sometimes returns *"xml tags in response, where the response should be … text"*, said it isn't trained on Indian literature, and that it failed basic Excel questions [40].

**Where it stands now.** The API side is fixed — it has native tool calling and strict JSON-schema structured output. The XML leakage in the consumer app is a separate issue, since the app doesn't appear to apply the controls the API already exposes. General model quality is a matter of opinion I'm not equipped to adjudicate.

---

### P09 — Broken chat templates, no day-one framework support
**Score 2.8** · recurring · severity 6 · impact 7 · **fixed**

CB Insights flagged "broken chat templates and other issues" among early-adoption friction, and the release lacked day-one vLLM support [29][33].

**Where it stands now.** Chat-template fields are on the official GGUF cards, the FP8 build ships tool and reasoning parsers, and there's an official LangChain provider. First-touch developer experience determines whether an ecosystem lifts off, so this mattering less now is a genuine improvement.

---

### P10 — Support bandwidth doesn't scale to self-serve
**Score 2.8** · recurring · severity 6 · impact 7 · **open**

A Fortune-500 security-SaaS ML team reportedly could not get the model running in production despite having every other resource, and needed a personal introduction to Sarvam to make progress [29].

**Where it stands now.** Unresolved as far as I can tell, and structurally hard. Forward-deployed engineering is Sarvam's stated model for enterprise, but it's headcount-bound and can't cover the long tail. The runtime fixes above should reduce how often it's needed.

---

### P05 — Indus app latency
**Score 2.4** · recurring · severity 6 · impact 6 · **open**

An App Store reviewer titled their review *"App very slow while answering"* and said the latency makes real-time usage difficult [40]. Rollout was compute-constrained with waitlists [37].

**Where it stands now.** Still cited in reviews through mid-2026. The likely root cause is architectural rather than capacity: everything routes to the 105B, and reasoning mode can't be turned off, so even trivial queries pay full reasoning latency.

I want to flag the limit of this evidence. App-store reviews are a sentiment signal, not a measurement. I have no first-party latency number, and I can't tell you the magnitude — only that the complaint recurs and the mechanism is verifiable.

---

### P03 — No public Indic-calibrated quantization
**Score 1.4** · isolated · severity 6 · impact 7 · **open**

Standard uniform quantization is calibrated on English and degrades the embedding and vocabulary layers where Sarvam's Indic token efficiency lives. An Indic-calibrated recipe "does not exist publicly" [29].

**Where it stands now.** Open — the only item in the deployment cluster that hasn't been closed. It scores lowest of the ten on my own frequency measure because exactly one source raised it and nobody is complaining about it. It also became the strongest surviving opportunity, which is a tension worth naming rather than hiding: the scoring model rewards things people complain about, and silent quality loss is by definition the thing nobody complains about.

That argument is developed properly in [Opportunities.md](Opportunities.md), and its weak point — nobody asked for this — is scored honestly at 4/10 in [Validation.md](Validation.md).

---

### P06 — Indus audio input capped around 30 seconds
**Score 0.8** · isolated · severity 5 · impact 5 · **unverified**

One reviewer: the app "cannot listen to audio longer than 30 seconds" [40]. A hard limit on a voice-first product, if accurate. Single source, and I haven't confirmed it against the current build.

---

### P07 — Over-restrictive guardrails on political topics
**Score 0.8** · isolated · severity 5 · impact 5 · **open**

A reviewer describes the app as "purely diplomatic" — you "cannot criticize or ask any questions related to the Prime Minister, the government" [40].

Single source, and genuinely double-edged given the government relationship. I'm including it because I said I'd log what I found, not because I think it's an opportunity. It's a credibility question for a product sold on sovereignty, and it isn't mine to resolve.

---

## Summary

| ID | Pain | Freq | Sev | Impact | Score | Status |
|---|---|---|---|---|---|---|
| P01 | No mainstream local runtime | pervasive | 8 | 9 | **7.2** | Mostly fixed — Ollama residual |
| P04 | Weak adoption, "wrapper" reputation | pervasive | 6 | 8 | **4.8** | Open |
| P02 | No CPU path, air-gapped licence fails | recurring | 7 | 8 | **3.7** | Largely fixed |
| P08 | Output quality gaps | recurring | 6 | 7 | **2.8** | Partly fixed |
| P09 | Broken chat templates | recurring | 6 | 7 | **2.8** | Fixed |
| P10 | Support doesn't scale to self-serve | recurring | 6 | 7 | **2.8** | Open |
| P05 | Indus latency | recurring | 6 | 6 | **2.4** | Open |
| P03 | No Indic-calibrated quantization | isolated | 6 | 7 | **1.4** | **Open** |
| P06 | Indus audio cap | isolated | 5 | 5 | **0.8** | Unverified |
| P07 | Over-restrictive guardrails | isolated | 5 | 5 | **0.8** | Open |

---

## What the pattern says

The dominant cluster when I started — P01, P02, P03, P09, P10, all versions of "the open weights are hard to actually run" — has mostly been closed by Sarvam since March 2026. That's the finding, and it's not the one I expected to write.

What's left is narrower and splits three ways. Indic-safe compression (P03) is the one genuine engineering gap nobody has filled. Consumer app latency (P05, P08) is a separate cluster and untouched. Adoption (P04) isn't a buildable feature at all — it's the outcome the other two move, which is why I treat it as a success metric rather than an opportunity.

The uncomfortable part: the highest-scoring pain was the most out of date, and the lowest-scoring one became the recommendation. If you only read the score column you'd have got this backwards, which is roughly what my first pass did.
