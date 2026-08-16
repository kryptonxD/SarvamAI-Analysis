# Research notes — Sarvam AI

Background: what the company is, what it sells, who buys it, and what the market around it looks like. Every factual claim points to a numbered source in [Sources.md](Sources.md).

Researched 7–8 August 2026, re-verified 16 August.

---

## The company

Sarvam AI trades under the legal entity **Axonwise Private Limited**, based in Bengaluru at 732 Chinmaya Mission Hospital Road, Indiranagar [1][7]. It was founded in **August 2023** by **Dr Vivek Raghavan** and **Dr Pratyush Kumar**, both previously at AI4Bharat and IIT Madras; Kumar holds an ETH Zurich PhD alongside IIT Bombay degrees [1][6][50].

In July 2026 the company added **Devendra Chaplot** as an advisor — a member of Mistral AI's founding team who worked on Mistral 7B and Mixtral, later at Thinking Machines Lab and on xAI's pre-training [52][53].

I could not find a reliable current headcount, or an executive roster beyond the founders and Chaplot. Both are simply unknown to me.

### Funding

Roughly **$275M across five rounds** by Tracxn's count [5]. The trajectory:

| When | What | Detail |
|---|---|---|
| Aug 2023 | Seed | $12.6M [5] |
| Dec 2023 | Seed + Series A | ~$41M combined, Lightspeed-led with Peak XV and Khosla [54][1] |
| 2025 | Small top-up | $228K from Avvanti, at a reported ~$196M valuation [12] |
| **15 Jun 2026** | **Series B first close** | **$234M at $1.5B post-money** [11][12][14] |

The Series B deserves precision because it's the fact most likely to be quoted back at you. It is a **first close of $234M against a $300M target**, not a completed $300M round [11]. HCLTech led with **$150M for 10.46%** (₹1,427.25 crore), alongside Bessemer, with Khosla and Peak XV continuing [12][14]. Activate has since been reported joining the ongoing round [5].

Worth noting what kind of money this is: the lead is an IT-services conglomerate, not a venture fund. Sarvam is the first Indian GenAI company whose lead investor is an Indian conglomerate [16]. That buys enterprise distribution and buys a dependency at the same time.

### The sovereign programme

In **April 2025** the government selected Sarvam under the IndiaAI Mission to build India's first from-scratch sovereign LLM, chosen from 67 shortlisted applicants [17][18]. The selection came with subsidised compute — reportedly 4,096 NVIDIA H100 SXM GPUs via Yotta, worth around ₹99 crore [22]. The wider IndiaAI Mission runs a ₹10,370 crore budget against a compute pool of roughly 34,000 H100/H200 GPUs [20][47].

This is the foundation of the company's position and its most obvious concentration risk. Government anointment is a moat until it's a dependency.

---

## What they sell

**Products** [7]

Samvaad (conversational and voice agents, aimed at BFSI and government), Studio (content transformation and dubbing), Akshar (document digitisation), Arya (enterprise "AI for Work", deployable on private cloud, on-prem, hybrid or air-gapped), Indus (the consumer assistant app), Edge (compact on-device models), Sarvam Code (coding agents), bespoke model training, and A1 Workbench for legal work [23][35].

**Models and APIs** [7][31]

Speech and language APIs: **Bulbul** for text-to-speech across 11 Indic languages, **Saaras** for speech-to-text across 12, **Mayura** for translation across 23, plus **Vision** for document OCR — which Sarvam claims beats Gemini and ChatGPT on key OCR benchmarks [31].

The foundation models:

| Model | Shape | Released |
|---|---|---|
| Sarvam-1 / 2B | 2B params, 4T tokens | Oct 2024 |
| Sarvam-M | 24B, fine-tuned on Mistral Small | May 2025 |
| **Sarvam-30B** | MoE, 128 experts, top-6, ~2.4B active of 32B, 32K context, ~16T tokens, from scratch | Feb–Mar 2026 |
| **Sarvam-105B** | MoE, 10.3B active, MLA attention, 128K context, 12T tokens, from scratch | Feb–Mar 2026 |

Both flagship models are **Apache-2.0**, published on Hugging Face and the government's AI Kosh portal [25][36][38].

The Sarvam-M lineage matters for understanding the company's strategy. Building a 24B model on Mistral Small drew a sustained "wrapper model" critique — a foreign model in a desi kurta, as one writeup put it [25][27]. The from-scratch 30B and 105B were the answer to that charge, and reading them as an engineering response to a reputational attack explains a lot about the timeline.

**Pricing** [10]

Pay-per-use in INR, credits that don't expire, ₹1,000 free to start. Sarvam-105B runs ₹4 / ₹2.5 / ₹16 per million input / cached / output tokens; the 30B is ₹2.5 / ₹1.5 / ₹10. Vision is ₹0.50 per page, speech-to-text ₹30 per hour. Plans tier at pay-as-you-go, ₹10,000 Pro, and ₹50,000 Business, with custom enterprise deals above that.

One oddity worth flagging if you're writing anything pricing-dependent: **the pricing page contradicts itself.** The text list describes the 105B and 30B as "Free per token" while the table charges per token, and the bonus-credit figures differ between the text block and the interactive cards [10]. That's a page bug rather than a claim about real prices, but I wouldn't quote the page without checking which half you're quoting.

**Deployment and compliance** [7]

SOC 2 Type II, ISO 27001, DPDP compliance, role-based access and audit trails, with deployment across Sarvam Cloud, VPC, on-prem and air-gapped. Python SDK, REST API, browser playground. NVIDIA NIM, Riva, TensorRT-LLM and Triton underneath [29].

**Recent moves**

Feb 2026 saw the 30B and 105B unveiled at the India AI Impact Summit, with weights following in March [25][34][36]. July brought the HCLTech–Sarvam–Odisha data centre MoU at a planned ₹14,257 crore outlay [51], and at the end of the month **Epoch 2026**, Sarvam's first developer conference: Chaplot as advisor, a San Francisco office, Sarvam Vision Edge commercially available, and a stated ambition to train a trillion-plus-parameter frontier model [52][53].

---

## The market

**Category.** Sovereign AI for India — indigenous full-stack AI built, hosted and governed within national borders, for reasons of data residency, language relevance and strategic autonomy. Sarvam also competes in the narrower voice and speech developer-platform category, where CB Insights places it as a Challenger [2][33].

**Size.** I don't have a defensible TAM for Indic AI and I'm not going to construct one. What I can say with sources: Indian AI startups raised **$1.48B in Q1 2026**, about 38% of all Indian startup funding that quarter [13], and the IndiaAI Mission is a ₹10,370 crore programme [20].

**What's moving**

1. The post-2025 national sovereign-AI push produced a whole cohort at once — Krutrim, Sarvam, BharatGen, CoRover, Gnani, Soket [44]
2. Mixture-of-experts architectures marketed on cost-efficiency against dense frontier models, as enterprise budget scrutiny tightens [13]
3. Verticalisation — sovereign players moving from raw LLMs into applied industry products [23][48]
4. IT services tying up with startups at the infrastructure layer, of which HCLTech–Sarvam is the flagship example [51]
5. Global incumbents owning Indian consumer usage. ChatGPT reports 100M+ weekly India users [37] — a hard wall for any local consumer play

**Regulation.** DPDP compliance and data residency are core selling points rather than afterthoughts, and air-gapped deployment specifically targets UIDAI and RBI-style environments [7][29].

---

## Who buys it

**Profile.** Large regulated Indian enterprises — banks, insurers, NBFCs — and government bodies needing multilingual data-resident AI. Secondarily, Indian developers building Indic applications.

**Named deployments** [7][16]

Tata Capital runs multilingual voice across the consumer-loan lifecycle, with a Chief Digital Officer testimonial claiming a 3x increase in customer engagement. SBI Life automates policy calls in 10+ languages. A skills-development programme ran 50,000+ farmer feedback calls. Logos include Aadhaar, Axis Bank, CRED, IDFC, Infosys, LIC, Mahindra Finance, NABARD, Urban Company and IndiaMART. Public-sector voice work has reached roughly 17M farmers for the Ministry of Agriculture and around 45M insurance policyholders.

That's real scale, and it's the strongest thing in this analysis. Whatever the download counts say, the enterprise and government business is genuinely deployed.

**What buyers are hiring it for**

Functionally: serve and collect from citizens in 22 languages by voice, at population scale; digitise handwritten and Indic documents; keep data in-country. Emotionally: control and trust, via sovereignty and air-gapping. Socially: visible alignment with Atmanirbhar Bharat and the IndiaAI programme [7][16][18].

**Willingness to pay.** Enterprise willingness is evidenced by the deployments above. At the developer and consumer end it's much softer — strong app ratings (4.6★ on Play) coexist with latency complaints and "why not just use ChatGPT" sentiment [39][40][55], and the government portal shows zero downloads of the flagship open model [36]. Adoption at the top, not yet stickiness at the bottom.

---

## The founders' public position

From their own posts and coverage: sovereign AI "developed, deployed, and governed entirely in India"; enabling a developer ecosystem with cheap high-quality Indic models; and — endorsed at Epoch — Chaplot's framing that *the model is not the goal, the ability to build models is the goal* [6][7][52][53].

What they push back on: the "wrapper model, not truly sovereign" charge, answered by training the 30B and 105B from scratch; and the assumption that frontier work needs enormous teams and compute, which Chaplot publicly disputes [27][52].

Their register is measured and mission-framed, and they respond to critics with technical reports rather than PR [24][27].

I didn't fetch a full founder interview verbatim beyond press-release lines, so I'd treat the above as themes rather than quotes.

---

## What I don't know

Listed because these are the gaps that would most change the analysis:

- **Headcount and executive roster** beyond the founders and Chaplot
- **Revenue or ARR** — not disclosed, not estimated here
- **A defensible TAM** for Indic AI
- **End-user review data** — no populated G2, Capterra, TrustRadius or Trustpilot profile exists for Sarvam, which is unusual and limits how much I can say about practitioner sentiment
- **Exact terms of the Series B second close** — only aggregator-dated
- **Whether Sarvam ships a supported, benchmarked CPU reference deployment**, as distinct from raw GGUF capability

---

## Five things worth carrying away

1. **Unicorn on strategic money, not venture money.** HCLTech's $150M lead and the follow-on ₹14,257 crore Odisha data centre make this a startup–conglomerate infrastructure alliance more than a conventional round [11][12][51].
2. **Government-anointed and sovereignty-branded.** First pick to build India's from-scratch sovereign LLM, with subsidised compute. Durable moat, real dependency [18][22].
3. **The deployment gap closed faster than its reputation.** The March 2026 criticism — no GGUF, no local runtime, no CPU path — was largely fixed by May, and most published commentary hasn't caught up [57][58][59]. See [Corrections.md](Corrections.md).
4. **Enterprise traction outruns developer traction.** Population-scale government and BFSI deployments alongside 591 monthly downloads on the flagship open model and zero on the government portal [7][36][59].
5. **The nearest rival left the field.** Krutrim paused foundational-model work and cut headcount from ~550 to ~150 to focus on profitable cloud [48]. Sarvam's competitive position improved in 2026 without Sarvam doing anything.
