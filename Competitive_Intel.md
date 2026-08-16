# Competitive landscape

Who Sarvam competes with, where it's genuinely ahead, and where a competitor does something it doesn't.

A caveat that applies to the whole file: I could not verify list pricing for any competitor. A like-for-like price comparison is the thing you'd most want here and it's the thing I can't give you without inventing numbers, so there isn't one. Several other cells are marked unverified for the same reason — an empty cell means I didn't find it, not that the capability is absent.

Last updated **16 August 2026**.

---

## The field

**Direct — same job, overlapping buyers**

- **Krutrim** (Ola, Bhavish Aggarwal) — India's first AI unicorn. Krutrim-3 plus its own GPU cloud [44][47][50]
- **CoRover.ai (BharatGPT)** — enterprise and government conversational AI; powers the IRCTC assistant at national scale [46]
- **Gnani.ai** — voice-first, building a 14B voice model [44][48]
- **BharatGen** — government-backed foundation models (Param-1, Param-2) [44]
- **Soket AI** — sovereign LLM effort [44]

**Adjacent**

- **Global frontier labs** — OpenAI, Google, Anthropic, xAI. ChatGPT alone reports 100M+ weekly India users [37][44]
- **Global speech APIs** — Deepgram, ElevenLabs, AssemblyAI [33]
- **Enterprise conversational AI** — Yellow.ai, Haptik, Uniphore [49]

**Substitutes** — the ones that don't show up in competitive decks but take the budget anyway

- **Bhashini**, the government's own translation and speech infrastructure across all 22 languages, effectively free [47]
- **AI4Bharat's open models** — the founders' own prior lineage, still freely usable [46]
- **In-house fine-tunes of Llama, Mistral or Gemma**, plus the BPO and spreadsheet workflows that multilingual ops actually run on today

That last group matters more than it looks. For a mid-size Indian enterprise the realistic alternative to Sarvam usually isn't Krutrim — it's Bhashini plus an open Western model plus people.

---

## Krutrim, the nearest rival

Positioned as private, commercial, frugal sovereign AI: models plus its own GPU cloud [47][50]. Krutrim-3 trained on 2T+ tokens across 22 languages, benchmarking roughly alongside Sarvam-105B with marginally stronger code generation, and explicitly optimised to run without supercomputers [47][50].

Then the thing that reframes the whole competitive picture: in mid-2026 Krutrim **paused foundational-model work** and refocused on AI cloud, cutting headcount from roughly 550 to 150. It reported FY26 revenue around ₹300 crore and its first annual net profit [48].

Read that carefully. India's first AI unicorn looked at building frontier models and chose profitable infrastructure instead. That's an opening for Sarvam and a warning about the economics, simultaneously. My source for it is a single Medium roundup [48], which is thinner than I'd like for a claim this load-bearing — treat it as reported rather than established.

**Others, briefly.** CoRover has the strongest government-scale reference deployment in IRCTC [46] but I found little else on pricing or recent moves. Gnani is voice-specialised and building a 14B model [48]. BharatGen is publicly funded and struggling on adoption in the same way Sarvam's early models did — Param-1 reportedly saw around 12 downloads [24]. Global incumbents own Indian consumer usage, wobble on complex Indic scripts, and route queries through US servers [31][37].

---

## Feature comparison

✅ has it · ⚠️ partial or contested · ❌ doesn't · — not verified

| Capability | Sarvam | Krutrim | CoRover | Gnani | OpenAI / Google |
|---|---|---|---|---|---|
| Deep Indic coverage (22 languages) | ✅ [7][31] | ✅ [47][50] | ✅ [46] | ⚠️ voice-focused [48] | ⚠️ scripts wobble [31] |
| Active from-scratch sovereign LLM programme | ✅ [25][36] | ❌ paused [48] | — | ⚠️ 14B in build [48] | n/a — not India-sovereign |
| **Runs on low-end / CPU hardware** | ⚠️ now possible via GGUF, throughput poor on 105B | ✅ runs without supercomputers [50] | — | — | ✅ API-served, no user hardware |
| **Owns commercial GPU/inference cloud** | ⚠️ building via HCLTech/Odisha [22][51] | ✅ Krutrim Cloud [47] | — | — | ✅ hyperscaler-backed |
| **One-command local runtime** | ⚠️ llama.cpp yes since May [57]; Ollama still blocked [32] | — | n/a closed SaaS | — | n/a API-only |
| Population-scale production deployments | ✅ ~17M farmers, ~45M policyholders [7][16] | — | ✅ IRCTC scale [46] | — | ✅ mass India usage [37] |
| Voice / speech API breadth | ✅ Bulbul, Saaras [7] | — | ✅ [46] | ✅ voice-first [48] | ✅ plus global speech APIs [33] |
| Indic document AI / OCR | ✅ Vision, 35M+ pages [2][31] | — | — | — | ⚠️ weaker on Indic scripts [31] |
| **Consumer app maturity / latency** | ⚠️ Indus latency and quality complaints [40][55] | — | — | — | ✅ ChatGPT and Gemini mature [37] |
| Data residency / air-gapped / DPDP | ✅ SOC 2, ISO 27001, DPDP [7] | — | — | — | ❌ US-routed [31] |
| **Indic-calibrated quantization** | ❌ | ❌ | ❌ | ❌ | ❌ — nobody has this |

---

## Where a competitor is genuinely ahead

Four rows above are real gaps rather than missing data:

1. **Frugal / CPU-capable deployment.** Krutrim designed for it from the start; Sarvam arrived at it via GGUF after the fact, and the 105B still isn't practical on CPU.
2. **Owned commercial inference cloud.** Krutrim sells H100 time today. Sarvam is building the capability through HCLTech and Odisha, so this closes over time rather than being a permanent gap.
3. **One-command local install.** Partly closed since May. Ollama, LM Studio and Jan users — the grassroots channel where open-model reputation is actually won — are still blocked on the 30B [32].
4. **Consumer app latency.** OpenAI and Google default to fast with thinking behind a toggle. Indus does the inverse: everything routes to the 105B with reasoning permanently on.

Note what's *not* in this list. Deep Indic coverage, sovereign programme, document OCR, data residency — Sarvam leads or ties on all of these. The competitive problem isn't capability. It's distribution and last-mile ergonomics.

---

## Positioning

Two axes decide this category: **sovereignty and data control** on one side, **deployment ease and ecosystem maturity** on the other.

- OpenAI and Google: high maturity, low sovereignty
- Krutrim: mid sovereignty, better deployment ease from owning its cloud and designing frugal
- **Sarvam: highest sovereignty, historically the hardest to run**

Sarvam has been moving right on that second axis all year — the GGUF builds, the llama.cpp merge, the FP8 builds, the LangChain provider are all the same move. That's the strategic story of 2026 for them, and it's more interesting than the funding round.

---

## The unclaimed space

**Sovereign *and* turnkey.** India-governed models that also run on hardware Indian enterprises already own, air-gapped, without silent quality loss.

Sarvam has closed most of the distance to that position since March. What's left, and what nobody in the field has, is the last row of the matrix: an Indic-calibrated quantization recipe. Everyone's compression pipeline is English-calibrated, including Sarvam's own shipped builds. Whoever publishes the first per-language retention table for a low-bit Indic model sets the standard for the category.

That's the opening, and it's narrow — which is roughly the honest summary of this whole analysis.

---

## Sarvam's vulnerabilities, ranked

1. **Adoption against valuation.** 591 monthly downloads on the flagship open model [59] and 0 on the government portal [36] sit awkwardly against $1.5B. This is now the top risk — it used to be the deployment gap, and Sarvam fixed that.
2. **Consumer front is weak.** Indus competes with ChatGPT and Gemini on latency, the axis where they're strongest [37][40].
3. **NVIDIA and compute dependency.** "Sovereign" models optimised for NVIDIA stacks on IndiaAI-subsidised compute. The GGUF path genuinely reduces this, which is underrated.
4. **Strategic-investor concentration.** HCLTech holds 10.46% and the Odisha infrastructure tie-up deepens it [12][51]. Leverage and dependency at once.
5. **The last mile of "open."** Ollama still blocked on the 30B [32]; no Indic-safe compression anywhere.
6. **Field crowded, but the #2 stumbled.** Krutrim's retreat from foundational models [48] is the single biggest competitive gift Sarvam got this year.

---

## Summary

Sarvam is India's best-funded and most institutionally-backed sovereign-AI company, with real Indic strengths and marquee population-scale deployments. Its from-scratch 30B and 105B answered the "wrapper" critique that dogged Sarvam-M.

The story I expected to write was that the open weights don't run. That was true in March and is mostly not true now — llama.cpp merged in May, first-party GGUF and FP8 builds shipped, templates and framework support landed. Sarvam closed its own biggest gap faster than the criticism of it circulated.

What replaced it is a harder problem. The models run now; not many people are running them. Krutrim, the nearest rival, has retreated from foundation models altogether, and the global incumbents own consumer usage while routing data offshore. The whitespace is sovereign *and* turnkey, and the last unclaimed piece of it is compression that doesn't silently erase the Indic advantage — which is the subject of [Opportunities.md](Opportunities.md).
