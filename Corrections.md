# Corrections

Everything I got wrong, in the order I found out. I'm keeping the original claim visible rather than editing it away, because the point of this file is the delta.

---

## 1. I said local-runtime support was shipped, and that `ollama run` works on mainline

**Recorded 7 August. Corrected 8 August.**

Wrong in both directions, which is the annoying kind of wrong.

llama.cpp *did* gain native `sarvam_moe` support — but not when I implied, and not where I looked. It landed on **9 May 2026**, in release `b9093`, via PR **#20275**, commit `1e5ad35`. Before that merge the converter exited with `Model SarvamMoEForCausalLM is not supported`. So the March-2026 writeups saying "you can't run it" were accurate when published and stopped being accurate in May.

Ollama is a separate implementation, and this is the part I collapsed. Its PR **#14703** is still open, carries merge conflicts, and marks `sarvam_moe` as requiring Ollama's own Go engine. llama.cpp's merge does not propagate to it. Saying "`ollama run` works on mainline" was simply false.

**What caused the error:** I searched the topic instead of the artifact. Searching "can you run Sarvam locally" returns the March narrative, indefinitely. Searching the release tag, the PR number and the config file returns the current state. Every row in the check log in [Validation.md](Validation.md) is an artifact for that reason.

---

## 2. The first-party GGUF doesn't use the `sarvam_moe` path at all

**Found 8 August.**

I assumed the official GGUF was the output of the architecture work in PR #20275. It isn't.

Hugging Face reports the architecture of `sarvamai/sarvam-30b-gguf` as **`bailingmoe2`**, not `sarvam_moe`. Sarvam shipped their GGUF mapped onto an architecture llama.cpp already supported, which is why the model card's build instructions are a plain `git clone` with no patch step. The llama.cpp PR discussion makes the distinction explicit — it implements full RoPE handling specifically to separate `sarvam_moe` from `bailingmoe2`.

So there are two independent paths to running this model locally: the first-party GGUF via `bailingmoe2`, and native conversion of the safetensors via `sarvam_moe` since May. The "you can't run it" narrative missed both, and so did I, in opposite directions.

**Still unverified:** whether `ollama run hf.co/sarvamai/sarvam-30b-gguf:Q4_K_M` actually works end to end. Hugging Face lists the command, but those snippets are auto-generated for any GGUF repo and don't prove compatibility. One command settles it and I don't have the disk space.

---

## 3. I priced the fix against a baseline that doesn't exist

**Found 8 August.**

My first draft costed "protect the embedding layers at higher precision" against an assumed uniform 4-bit build, and produced a scary number — around 17% on file size.

Then I read the metadata on the shipped file. `sarvam-30b-gguf` is 19.6 GB for 32.14B parameters, which is **4.88 bits per weight effective**. A uniform Q4_K build at 4.5 bpw would be about 18.1 GB. Roughly 1.5 GB is already being spent on higher precision somewhere.

K-quant is already mixing precision upward. llama.cpp's Q4_K_M conventionally promotes `output.weight` to Q6_K, and by default doesn't apply the importance matrix to it at all. If that holds here, then half the protection I was proposing is already shipped and only the token embedding remains — which costs about **1.4%**, not 17%.

That inverts the recommendation. The interesting question stops being "should you protect the embeddings" (it's cheap enough to be uncontroversial) and becomes "does recalibration alone move the numbers."

**Still unverified:** I haven't run `gguf-dump` on the shipped shards to read actual per-tensor types. The 4.88 bpw figure proves upward mixing exists but not where it's spent. That dump is a ten-minute job for anyone inside the company.

---

## 4. Download figures moved between drafts

**Updated 16 August.**

The Hugging Face counter on `sarvam-30b-gguf` read 482 downloads and 21 likes when I first wrote this up on 8 August. On 16 August it reads **591 downloads in the last month and 22 likes**.

The direction doesn't change the argument — for a government-backed sovereign flagship this is still a very small number, and pickup remains the problem rather than institutional standing. But the specific figure is a live counter, so treat any version of it as a snapshot with a date attached, including this one.

---

## 5. One thing I expected to be wrong, and wasn't

On the 16 August re-check I went back at the two academic citations expecting to have overstated them, since that's the easiest kind of error to make when a paper supports your argument.

Both held. [MoEQuant](https://arxiv.org/abs/2505.03804) is genuinely an ICML 2025 paper, not just an arXiv preprint — there's a poster page on the ICML site. And the [Compass-v3](https://arxiv.org/abs/2509.09121) figures I quoted are accurate: naive quantization cost Thai 10.17% and Vietnamese 11.75% against BF16, their expert-aware scheme brought both inside 2.5%, and Portuguese came out 1.05% above the BF16 baseline.

Recording this because a corrections file that only ever moves in one direction isn't a corrections file, it's a hedge.

---

## What this file is for

I lead with these because they're the most useful thing in the repo. Four clean kills would have looked tidier and meant less. The corrections are also what pushed the surviving idea's score *down* — differentiation went 7 → 6 → 5 as the prior art got clearer, which is the arithmetic in [Validation.md](Validation.md).

Verification should be able to lower your own numbers, not only raise them.
