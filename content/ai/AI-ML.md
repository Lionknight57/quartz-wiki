---
title: AI and Machine Learning
tags: [ai, machine-learning, deep-learning, llm, prompt-engineering]
related: [Claude Code, CSharp, Python]
sources: [AI/AI Essentials.md, AI/AI and Language Models.md, AI/Generative AI Foundations.md, AI/Prompt Engineering.md, AI/Machine Learning.md, AI/NLP on Google Cloud.md, AI/Generative AI Foundations.md, AI/GitHub Copilot for IT Pros.md, AI/Introduction to Azure AI Services.md, AI/TensorFlow.md, Tensor Flow.md, Medium/2026-04/Medium Daily Digest - 2026-04-15.md, Medium/2026-04/Medium Daily Digest - 2026-04-16.md, Medium/2026-04/Medium Daily Digest - 2026-04-19.md, AI/AI Fundementals.md, Medium/2026-04/Medium Daily Digest - 2026-04-22.md, Medium/2026-04/Medium Daily Digest - 2026-04-28.md, towardsai.net/i-tested-claude-opus-4-7-vs-4-6-on-7-real-tasks, towardsai.net/yann-lecun-quit-meta-5-months-ago-to-bet-1-03b-against-llms, Medium/2026-06/Medium Daily Digest - 2026-06-11.md]
updated: 2026-06-27
---

# AI and Machine Learning

Artificial Intelligence is the broad idea of machines doing things that seem intelligent. Machine Learning is the specific approach of learning from data. Deep Learning is ML where the model has many stacked layers.

| Concept | Relationship |
|---|---|
| **AI** | Broad field — machines doing intelligent things |
| **Machine Learning** | Learning from data by adjusting weights |
| **Neural Network** | An ML model shaped like a layered network of nodes |
| **Deep Learning** | Neural network with many layers (deep = many floors) |
| **LLM** | Large Language Model — deep learning trained on text at massive scale |

---

## How Machine Learning Works

A machine learning model is a mathematical function with millions of adjustable numbers called **weights**. Training runs labelled examples through the model, measures how wrong the output is (the **loss**), then nudges the weights slightly in the right direction using **gradient descent**.

**The free-throw analogy:** You throw the ball, get feedback ("too hard"), adjust, repeat. After thousands of attempts your body dials in the right motion. ML works the same way — guess, measure error, adjust, repeat.

**The dial panel analogy:** Imagine thousands of dials all set to random values. Feed in a cat photo, get "dog" back — wrong. A process called **backpropagation** nudges each dial slightly toward the answer that would have produced "cat". After a million examples, the dials settle into positions that reliably recognise cats.

> **Key insight:** The model never *understands* anything. It finds statistical patterns in numbers. A model that recognises cats has never "seen" a cat — it has found regularities in pixel values.

---

## Neural Networks and Layers

A **neural network** is ML where the dial panel is organised into stacked layers — like floors in an office building.

```
Input layer  →  Hidden layers (progressively abstract)  →  Output layer
  (pixels)       (edges → shapes → faces → "person")        (cat/dog)
```

- **Ground floor** — raw input (pixels, tokens, audio samples)
- **Middle floors** — progressively abstract patterns
- **Top floor** — final answer

Why layers matter: a single layer can only learn simple relationships. With layers the network chains them:
- Layer 1: *"these pixel patterns form an edge"*
- Layer 2: *"these edges form a pointy ear"*
- Layer 3: *"pointy ear + whisker shape = cat"*

No human programmed those rules — they emerge from training.

---

## Language Models

### History

| Year | Milestone |
|---|---|
| 1954 | Georgetown-IBM Experiment — 250-word, 49-sentence translation system |
| 1980s | Statistical approaches |
| Early 2000s | Rise of neural networks |
| 2013 | Word embeddings |
| 2017 | **Transformer architecture** — the breakthrough |
| 2018 | BERT (bidirectional encoder) + GPT (generative pre-trained transformer) |
| 2020+ | GPT-3/4, Claude, Gemini — LLMs at massive scale |

### Foundation models

A **Foundation Model** is a versatile AI model trained on extensive, diverse data — adaptable for broad applications. Foundation models handle multiple mediums: text, audio, visual.

**Transformer components:**
- **Attention mechanism** — identifies and focuses on important parts of input
- **Activation functions** — decide how much information moves to next layer
- **Parameters** — the adjustable dials/sliders that fine-tune the model
- **Loss function** — gives the error score during training
- **Optimizers** — tweak parameters for better performance

**Neural network layers:**

| Layer type | What it learns |
|---|---|
| Groundwork | Basic concepts — lines, colours, shapes |
| Intermediate | Complex structures — faces, objects, syntax, grammar |
| Advanced | Abstract concepts — sentiment, sarcasm, reasoning |

### How an LLM answers a question — step by step

Example: *"What is the capital of Latvia?"*

**1. Tokenisation** — sentence split into chunks:
```
["What", " is", " the", " capital", " of", " Latvia", "?"]
```

**2. Token → vector** — each token maps to thousands of numbers encoding meaning. "Latvia" lands near "Estonia", "Lithuania", "Baltic" in that number space because they co-occurred in training text.

**3. Attention** — the model computes relationships between all tokens simultaneously. For "capital" it notices:
- "What" → this is a question asking *what something is*
- "Latvia" → the *subject* of the capital question

**4. Pattern matching against training weights** — the model never looks up a database. During training on billions of pages, it saw:
```
"...Riga, the capital of Latvia..."
"...Latvia's capital city, Riga..."
```
Those patterns adjusted the model's weights so "capital of Latvia" now strongly predicts "Riga".

**5. Token prediction** — the model outputs a probability distribution:

| Token | Probability |
|---|---|
| Riga | 97.3% |
| Tallinn | 0.8% |
| Vilnius | 0.6% |

It picks "Riga" → done.

### LLMs vs databases

LLMs are **not databases**. They don't store and retrieve exact data. They generate responses based on patterns learned from training data — synthesising new content by predicting the most likely sequence of tokens. This is why they can hallucinate.

**Key insight:** "Riga" is baked into the model's weights as a statistical pattern — which is also why models can confidently give wrong answers for obscure facts where training data was sparse or contradictory.

**Hallucination:** When a generative AI model produces inaccurate or irrelevant results that seem convincing. Not always easy to recognise.

---

## AI Chatbots

| Type | Description |
|---|---|
| **Rule-based** | If/then decision trees, pre-programmed flow, can't detect synonyms |
| **AI Chatbots** | NLP + ML, user types freely, learns from data, recognises intent |

**Components of an AI chatbot:**
- **NLP (Natural Language Processing)** — reads, understands, derives meaning from human language
- **Machine Learning** — improves responses from collected data via a Knowledge Base
- **Intent recognition** — understands the goal behind a query even when phrased differently

**Industry applications:** Customer service, HR, finance, marketing, sales, e-commerce (product recommendations), healthcare (appointment scheduling), insurance (automated quotes), manufacturing (supply queries).

**Building chatbots:**
- Code: Python, JavaScript, Java
- SDKs: Microsoft Bot Framework, Node.js (DialogFlow, ChatterBot)
- APIs: OpenAI API, Google Chat API, Facebook Messenger API
- No-code builders: Azure Cognitive Services, Microsoft Power Virtual Agent

---

## Prompt Engineering

Crafting effective prompts is an art requiring experimentation. Every prompt has attributes — the same qualities that define any piece of writing.

### Prompt attributes

| Attribute | Description | Example |
|---|---|---|
| **Format** | Type of output | essay, list, blog post, tweet, code |
| **Length** | How long | "5-minute read", "10-item list", "500 words" |
| **Audience** | Who it's for | "explain to a 10-year-old", "for software developers" |
| **Tone** | Mood/style | formal, casual, empathetic, funny |
| **Domain** | Subject focus | health benefits, economic impacts, neurological |
| **Perspective** | Point of view | optimistic, neutral, pessimistic |
| **Role/Persona** | Who the AI acts as | "act as a marketing copywriter", "act as a teacher" |

### Prompt techniques

**Prompt chaining** — treat it as a conversation, refining iteratively:
```
1. "Write marketing copy for a newsletter about the Eames Lounge Chair"
2. "Make it a 5-minute read"
3. "Remove the salutations and signature, and suggest images"
```

**Flipping the role** — get multiple perspectives:
```
"As a hiring manager, what do you look for in a leader?"
→ flip →
"As a leadership candidate, what concerns you about a new job?"
```

**Shot-based prompting:**
| Type | Examples given | When to use |
|---|---|---|
| Zero-shot | None | Simple, clear tasks |
| One-shot | 1 | Orients the model toward format/style |
| Few-shot | 2–3 | Complex format requirements |
| Many-shot | Many | Precise output formatting |

**Advanced techniques:**
- Combine role + time period: *"Imagine you're a historian in 2150 looking back at social media's impact"*
- Ask the model to quiz you with multiple-choice questions
- Long, detailed prompts for nuanced answers in ambiguous domains

**For image generation**, specify: style (abstract/realistic), composition (rule of thirds, depth), colour scheme (monochromatic, complementary), subject (foreground/background), mood (eerie, serene). Avoid overspecification — leave room for creativity.

### Prompt library

Keep a personal prompt library of what attributes and orderings give the best results for your common tasks.

---

## Local LLMs vs cloud (2026 reality check)

From Eduard Ruzga's "Local LLMs Are Finally Beating the Cloud! — But Are They?":

**What's actually happening:** Consumer GPU hardware (RTX 4090, M-series Macs) can run 70B+ parameter models. Benchmarks show local models beating cloud on specific coding tasks — but benchmarks are cherry-picked.

| Dimension | Local LLMs | Cloud LLMs |
|---|---|---|
| Cost at scale | Near-zero marginal | Pay per token |
| Privacy | 100% local | Data sent to provider |
| Speed | Limited by VRAM | Provider-side scaling |
| Model quality | ~70B max practical | 1T+ parameter models |
| Setup complexity | High | Zero |
| Updates | Manual | Automatic |

**Verdict (2026):** Local wins for privacy-sensitive tasks and budget-constrained high-volume use. Cloud wins for quality, reliability, and complex reasoning. Most serious developers use both.

> "Local LLMs Are Finally Beating the Cloud! — But Are They?" — Eduard Ruzga (206 claps)

### Dedicated local-inference hardware — NVIDIA RTX Spark

Rather than repurposing a gaming GPU, RTX Spark is a desktop box built specifically for local LLM inference:

| Spec | Value |
|---|---|
| CPU | 20-core Arm |
| GPU | Blackwell architecture, 6,144 CUDA cores |
| Memory | 128GB unified (shared CPU/GPU) |
| Interconnect | NVLink |
| Peak throughput | Up to 1 petaflop |
| Practical capability | Runs a 120B-parameter model locally |

128GB unified memory at this price point pushes the practical "what fits locally" ceiling well past the 70B class in the consumer-GPU table above — closer to mid-size frontier models than to the RTX 4090 generation.

> Source: Pramod Chandrayan (in Predict) — "NVIDIA Just Put a 120-Billion-Parameter AI Model in Your Laptop. Here's What That Actually Changes." (2026-06-11)

---

## Gemma 4 models (Google, April 2026)

Google released Gemma 4 family under Apache 2.0:

| Model | Params | Note |
|---|---|---|
| Gemma 4 1B | 1B | Ultra-lightweight, on-device |
| Gemma 4 4B | 4B | Mobile/edge |
| Gemma 4 12B | 12B | Balanced |
| Gemma 4 26B | 26B | Disproportionately strong for its size — standout of the family |

The 26B model tested notably above its weight class on coding and reasoning benchmarks.

> "I Tested All 4 Gemma 4 Models: The 26B One Is Cheating (In the Best Way)" — Chew Loong Nian (174 claps)

---

## LLM GPU fit tool

From Pawel's "Stop Guessing Which LLMs Fit Your GPU":

A community tool that calculates whether a given model fits in your GPU's VRAM before you download it. Solves the common problem of downloading a 70B model only to find it needs 40GB VRAM but you have 8GB.

**Inputs:** model size (params), quantisation level (4-bit, 8-bit, fp16), available VRAM  
**Output:** will it fit? how much headroom? recommended quantisation for your hardware

**Quick VRAM reference:**

| Model size | Quantisation | VRAM needed |
|---|---|---|
| 7B | 4-bit (GGUF Q4) | ~4 GB |
| 7B | 8-bit | ~8 GB |
| 13B | 4-bit | ~8 GB |
| 30B | 4-bit | ~16 GB |
| 70B | 4-bit | ~40 GB |
| 70B | 2-bit | ~20 GB |

> "Stop Guessing Which LLMs Fit Your GPU — There's a Tool for That" — Pawel (209 claps)

---

## Nemotron 3 Nano (NVIDIA, 2026)

NVIDIA's small model designed for practical on-device deployment:
- Optimised for inference efficiency over raw benchmark scores
- Targets edge devices and resource-constrained environments
- Positioned as the most practical small model for real workloads (not just benchmarks)
- Apache 2.0 licence — fully open for commercial use

> "Nemotron 3 Nano: Why This 'Small' Model Might Be the Most Practical AI You'll Use" — Faisal haque (201 claps)

---

## Claude Opus 4.7 (April 2026)

Released 2026-04-16 to API, Bedrock (`anthropic.claude-opus-4-7-v1:0`), Vertex AI (`claude-opus-4-7@20260416`), and Microsoft Foundry. Same price as 4.6: **$5/$25 per million in/out tokens**.

### Benchmark gains over Opus 4.6

| Benchmark | Opus 4.6 | Opus 4.7 | Delta | GPT-5.4 | Gemini 3.1 Pro |
|---|---|---|---|---|---|
| SWE-bench Verified | 80.8% | **87.6%** | +6.8 | — | 80.6% |
| SWE-bench Pro (agentic coding) | 53.4% | **64.3%** | +10.9 | 57.7% | 54.2% |
| GPQA Diamond (grad reasoning) | 91.3% | **94.2%** | +2.9 | 94.4% Pro | 94.3% |
| MMMLU (multilingual) | 91.1% | 91.5% | +0.4 | — | 92.6% |
| MCP-Atlas (tool use) | 75.8% | 77.3% | +1.5 | — | — |
| BrowseComp (agentic search) | — | 79.3% | — | **89.3% Pro** | — |
| Anthropic internal 93-task | baseline | +13% | — | — | — |

Headline claim: **4 tasks Opus 4.6 categorically cannot solve at default settings** (verified independently by Chew Loong Nian on 2 of them).

### The default-effort swap (the real story)

The "nerfed Claude" complaints in March 2026 traced to a silent change on March 3 setting Opus 4.6's default effort to `medium` (internal tag: effort 85). Fine for chat, disastrous for long agentic coding runs (loops, hallucinated imports, abandoned sessions).

Opus 4.7 silently flipped the default back to **`high`**. Setting `effort: "high"` on Opus 4.6 manually closes ~60% of the perceived 4.6→4.7 gap on long-horizon coding tasks. Most of the headline upgrade is the default change, not the model weights.

```python
# Free upgrade for anyone still on 4.6:
client.messages.create(
    model="claude-opus-4-6",
    thinking={"type": "adaptive", "effort": "high"},  # was "medium" by default
    ...
)
```

### Other changes

- **Adaptive thinking** is now the only mode — model auto-scales compute per task
- **Manual extended thinking removed** — `thinking.budget_tokens` no longer settable on 4.7+
- **Vision improvements** — chemical structures and technical diagrams (e.g. CPU pipeline forwarding paths) now correctly interpreted; not marketing fluff
- **Long-context consistency** — 900KB-context retrieval task: 4.7 found all 17 callers vs 4.6@medium (14, with hallucinated paths) and 4.6@high (16)

### Use-case guidance

| Workload | Recommendation |
|---|---|
| Long agentic coding workflows | **Switch to 4.7** — +10.9 SWE-bench Pro is real and reproducible |
| On 4.6, tight budget | Set `effort: "high"` first — closes most of the gap free |
| Web-research / browse-heavy agents | **Stay on GPT-5.4** — 10-point BrowseComp lead, not a rounding error |
| Vision (diagrams, chemistry, medical) | 4.7 is a meaningful upgrade |
| One-shot completions (<500 tokens out) | Either is fine — gap shows up on long runs |
| Cost-sensitive chat | **Sonnet 4.6** ($3/$15) still the right pick — 4.7@high actually costs *more* per session than 4.6@medium at the same sticker price |

### Counterpoint

Alex Dunlop's "Opus 4.7 Is The Worst Release Anthropic Has Ever Shipped" (5 min, 235 claps) argues the removal of manual `thinking.budget_tokens` is a step backward for power users who had tuned budgets for specific workloads — variability of adaptive mode trades predictability for average-case quality. Valid for batch pipelines where token budget is a hard constraint; less relevant for interactive coding.

> Sources: Chew Loong Nian, "I Tested Claude Opus 4.7 vs 4.6 on 7 Real Tasks: The Default Setting Swap" (Towards AI, 2026-04-17); Alex Dunlop, "Opus 4.7 Is The Worst Release Anthropic Has Ever Shipped"

---

## Qwen 30B on Raspberry Pi (April 2026)

From Sebastian Buzdugan's "A 30B Qwen model runs in real time on a Raspberry Pi" (8 min, 1K claps):

A 30B parameter Qwen model achieves real-time inference on a Raspberry Pi — a significant milestone for edge AI. Why it matters:
- Demonstrates that 30B models are becoming viable on sub-$100 hardware
- Quantisation (likely 2-bit or 3-bit) enables it — heavy quality trade-off but functional
- Changes the calculus for air-gapped / offline AI deployments
- Raspberry Pi = deployable in environments where cloud is impractical (field sensors, kiosks, embedded systems)

**VRAM table addition:** at 2-3 bit quantisation, 30B model fits in ~8–12 GB — overlapping with consumer GPU territory, not just server hardware.

---

## Qwen Instruct models (Alibaba) — the open coding workhorse

Qwen is Alibaba's open-weight LLM family and the recurring "free model that matches Claude on coding" in the [[Medium-Reading|backlog]]. The thing to understand first is what **"Instruct"** now means in Qwen.

### Instruct vs Thinking — a deliberate split, not just "chat-tuned"

The original Qwen3 (April 2025) shipped **hybrid** checkpoints that toggled thinking on/off in one model. Alibaba **abandoned the hybrid approach** — it dragged down benchmark quality — and now trains two separate lines:

| | **Instruct** | **Thinking** |
|---|---|---|
| Optimised for | chatbots, OCR/extraction, direct answers, low latency | math, STEM, code, multi-step reasoning |
| Behaviour | answers directly, no extended reasoning trace | explicit reasoning before answering |
| Speed / cost | fast, predictable, cheaper | deeper but slower and pricier |
| Sampling defaults | temp 0.7 / top_p 0.8 | temp 1.0 / top_p 0.95 |

The split paid off immediately: `Qwen3-235B-A22B-Instruct-2507` posted a ~2.8× AIME25 jump over the April hybrid release. Rule of thumb: **reasoning-heavy work (code, math) benefits most from Thinking; for direct generation/extraction the Instruct variant is near-equal and much cheaper.** Alibaba says hybrid may return once the quality regression is solved.

### Instruct lineup (Qwen3 → 3.5)

- **Dense Instruct:** 0.6B, 1.7B, 4B, 8B, 14B, 32B — all 128K context, tool calling, structured output.
- **MoE Instruct:** 30B-A3B (3B active), 235B-A22B (22B active). MoE = total params for capacity, *active* params for inference cost.
- **Qwen3.5 (Feb 2026):** scales to ~397B total / 17B active, **201 languages**, claimed 8.6×–19× throughput gain over the prior generation.

### Coder-Instruct — the variants the backlog keeps citing

- **Qwen3-Coder-480B-A35B-Instruct** — flagship open *agentic* coder (35B active). **256K context native → 1M with extrapolation.** SOTA among open models on agentic coding / browser-use / tool-use, reported **comparable to Claude Sonnet 4**.
- **Qwen3-Coder-30B-A3B-Instruct** — the run-it-locally one; **50.3% Pass@1 on SWE-bench Verified**. This is the model behind the vault's "run Claude Code locally on a Mac with a 4-bit Qwen3.6-27B" backlog items — at 2-4 bit quant it lands in consumer-GPU / Apple-Silicon territory (see [[GPU-and-Hardware-for-AI]]).
- **Qwen3-Coder-Next** — newer technical-report entry continuing the coder line.

### Why it matters here

Qwen is the practical answer to "[[Claude-Code]] but local / zero marginal cost": the 30B-A3B Coder-Instruct runs on a high-RAM Mac or a single consumer GPU and is routinely benchmarked against Opus/GPT on real coding tasks. Compare with [[AI-ML#Gemma 4 models (Google, April 2026)|Gemma 4]] (the other local-model camp). Caveat from the vault's hardware notes: hosted Qwen via Ollama Cloud has been unreliable (high failure rates on Qwen3.5) — the value is in *self-hosting*.

> Sources (web research, 2026-06-27): [Qwen3-Coder blog](https://qwenlm.github.io/blog/qwen3-coder/); [Qwen3-Coder GitHub](https://github.com/QwenLM/Qwen3-Coder); [Qwen3 full lineup guide 2026](https://baeseokjae.github.io/posts/qwen-3-full-lineup-guide-2026/); [The Register — Alibaba drops hybrid thinking](https://www.theregister.com/2025/07/31/alibaba_qwen3_hybrid_thinking/); [Fireworks — Qwen3 Instruct vs Thinking vs Coder](https://fireworks.ai/blog/qwen-3-decoded); [Best Qwen models 2026](https://www.remoteopenclaw.com/blog/best-qwen-models-2026)

---

## AI as pattern matching — the developer mental model

> "AI is software that uses statistical patterns, learned from data, to perform tasks that traditionally required human judgment."

The key shift: **deterministic → probabilistic**. Traditional code says "if X then Y because I told it to." AI says "when I see X, it's probably Y because that's what patterns suggest." This is why fraud detection is 94% accurate rather than 100% correct — and why that's not a bug.

**What AI is not doing:** understanding, reasoning, or knowing. A language model that writes Shakespeare-quality prose has no understanding of narrative or emotion — it found patterns in vast amounts of text. This explains both the impressive capabilities and the bizarre failures:
- Can write human-like text → learned those patterns from training data
- Fails at counting letters in "strawberry" → counting is computation, not pattern matching
- Confidently states wrong facts → those word patterns were statistically likely in training
- Fails when an image is rotated 45° → different pixel patterns = different input

**The evolving taxonomy:** ML, deep learning, neural networks, transformers are all techniques within AI — not competing approaches. They all do the same fundamental thing: learn patterns from data. Expert systems (rule-based, 1980s) were once called "AI"; today they're just code.

---

## Training vs inference — the key operational split

| | Training | Inference |
|---|---|---|
| **What** | Model learns patterns from labelled examples | Learned patterns applied to new data |
| **When** | Once per model version, offline | Every API call, in production |
| **Cost** | Millions of dollars (large models), weeks of GPU time | Fast, relatively cheap |
| **Who does it** | Model providers (OpenAI, Anthropic, Google) | Everyone who calls the API |

**Most developers only deal with inference.** You call a pre-trained model's API — the patterns were already learned.

Understanding this explains:
- Why models don't improve from your production data unless you retrain
- Why "teaching it your use case on the fly" doesn't work
- Why retraining is a big deal (cost, time)
- Why data quality at training time determines production quality forever

**Fine-tuning / transfer learning:** take a model with general patterns, teach it more specific ones for your use case. Much cheaper than training from scratch. Still bound by the same limitations — pattern matching, data quality dependency.

---

## Tokens and context windows

A **token** ≈ 4 characters — a word or part of a word ("understanding" = "under" + "standing" = 2 tokens). Models process everything as tokens, not words or sentences.

**Context window** = how many tokens the model can process at once (its "working memory"). The model literally cannot see past this limit.

| Era | Context window |
|---|---|
| Early GPT-3 | ~4K tokens |
| GPT-4 | 8K–32K tokens |
| Claude 3+ | 100K–200K+ tokens |

Why this matters in production:
- Long documents get truncated
- Long conversations cause the model to "forget" early context
- Context usage = cost (every token in the window costs money)

---

## Embeddings — how text becomes numbers

Everything AI processes — text, images, audio — must become numbers. The word "king" becomes a list of hundreds of numbers (a vector). "Queen" becomes a different vector.

These numbers capture **semantic relationships**: `KING - MAN + WOMAN ≈ QUEEN`. This works because the numerical representations capture patterns of co-occurrence in training data.

Practical implications:
- Search finds related concepts, not just exact keyword matches — "car" also retrieves "automobile"
- Similar meaning → similar vectors → similar search results
- Vector databases (Pinecone, Weaviate) store and query these embeddings efficiently

---

## Temperature and sampling parameters

Controls how generative models (LLMs) make decisions — how deterministic vs creative the output is:

| Parameter | Effect |
|---|---|
| **Temperature = 0** | Near-deterministic — same input → same output every time |
| **Temperature = 1** | Creative — same input → varied phrasings each time |
| **top-k** | Only consider the K most probable next tokens (e.g. top-k=50) |
| **top-p** | Only consider tokens until cumulative probability hits P% (e.g. top-p=0.9) |

**Practical guidance:**
- **Customer service bot** → low temperature (0–0.2): consistent, reliable responses
- **Creative writing assistant** → higher temperature (0.7–1.0): variety and surprise
- **Code generation** → low temperature: deterministic, fewer hallucinations

---

## Confidence scores ≠ accuracy

A model saying it's "99% confident" does NOT mean it's 99% likely to be correct. It means the pattern strongly matches what it learned as that class.

If the model learned wrong patterns, it can be very confident and very wrong.

**Threshold decisions belong to you, not the AI:**
- Model outputs: "75% probability this transaction is fraud"
- You decide: block at 70%? 85%? Higher threshold = fewer false positives but more missed fraud
- These are **ethical and business decisions** — the AI only gives probabilities

**Calibration:** test model confidence against actual outcomes before trusting in production. A model "90% confident" should be right ~90% of the time — if it's actually right 70% of the time, all downstream risk assessments are broken.

---

## Error compounding in AI pipelines

When you chain multiple AI models (agents, RAG pipelines, multi-step workflows), errors multiply:

```
3 models, each 90% accurate:
0.9 × 0.9 × 0.9 = 0.729  →  27% error rate

5 models, each 90% accurate:
0.9^5 = 0.59  →  41% error rate
```

This is why multi-agent systems need careful design — uncertainty compounds at every step. A final output that's "5 steps deep" can be largely random even if each individual model performs well.

---

## Preprocessing requirements

Models are extremely picky about input format:

| Input type | Requirement |
|---|---|
| Images | Exact resolution (224×224 means exactly 224×224), correct color channels (RGB vs grayscale), normalised pixel values (0–1 or -1 to +1) — one pixel off = failure |
| Text | Correct encoding, within context window |
| All | Data must match the **distribution** of training data — the most common production failure |

**Data representativeness > data correctness:** A model trained on perfectly scanned documents will fail on phone photos with coffee stains. The data isn't garbage — it's just not representative.

---

## Explainability problem

Most models are black boxes — they output a decision, not an explanation. Tools like SHAP/LIME approximate explanations after the fact ("income was the main factor") but these are approximations, not true causal explanations.

**When explainability is required:** regulated industries (credit, healthcare, insurance, legal) often require documented reasoning. If your use case mandates explanation, AI may be the wrong tool. Design for this constraint before building.

---

## Feedback loops

AI decisions create data that influences future AI decisions — which can reinforce biases:

- **Recommendation systems:** show users what they seem to like → they click it → system learns they like it → shows more → echo chamber
- **Credit models:** reject applicants with certain profiles → never see if they'd have been good customers → keep rejecting similar profiles → entire segments locked out

Loops are everywhere. Models can actively make things worse by reinforcing their own biases. Plan to detect and break loops at design time.

---

## Production realities

### What works reliably
- **High-volume classification with stable patterns** — fraud detection, spam filtering, content moderation
- **Recommendation** — correlation at scale; Netflix doesn't need to understand why you like dystopian fiction, just that you fit a pattern
- **Document processing** — OCR on standard forms, invoice extraction — key word: *consistent format and representative training data*

### What doesn't work reliably
- **Novel reasoning** — "reset my password" (pattern) vs "here's my unique situation" (reasoning required)
- **Guaranteed accuracy** — probabilistic systems cannot guarantee deterministic outcomes
- **Self-correction** — AI doesn't learn from production mistakes; retrain to fix

### Monitoring AI in production
Traditional uptime monitoring is insufficient — a model can be up and returning responses while being completely wrong.

| Metric | What it catches |
|---|---|
| Prediction drift | Production data patterns diverging from training data |
| Accuracy decay | Performance degrading over time |
| P95/P99 latency | AI inference spikes (P99 spike = 1 in 100 users waits 10× longer) |

### Deployment strategies
- **Shadow mode** — run new model alongside old, compare results before switching
- **Canary deployment** — route 1% of traffic to new model, watch for issues
- Never swap full production on passing tests alone — behaviour changes may only appear at scale

### Cost management
| Strategy | Impact |
|---|---|
| Temperature = 0 | More predictable, slightly cheaper |
| Token limits on API calls | Hard cap on per-call cost |
| Cache repeated queries | One e-commerce site cut costs 50% caching product description enhancements |
| Batch vs real-time | Batch is ~10× cheaper but adds latency — most teams start real-time, hit bills, redesign for batch |
| Use smaller models for lower-stakes tasks | Simple classification → cheap model; high-stakes decisions → best model |

**Indirect costs often exceed AI costs:** data pipelines, storage, human review of edge cases, monitoring infrastructure.

### Security concerns specific to AI
- **Prompt injection** — manipulating prompts to leak information or bypass safeguards
- **Model extraction** — competitors reverse-engineering your fine-tuned model through careful queries
- **PII leakage** — models can accidentally reproduce private data from training sets (GDPR implications)

---

## Agent harness components (2026)

From Divy Yadav's "7 Agent Harness Components Every AI Developer Needs to Build Reliable AI Agents" (13 min, 298 claps) and Yanli Liu's "Harness Engineering: What Every AI Engineer Needs to Know in 2026" (22 min, 698 claps):

A **harness** is the infrastructure layer that wraps an LLM to make it production-reliable. Raw LLM calls are not enough — agents need scaffolding to handle failures, state, and coordination.

**Three architectural camps (Yanli Liu):**

| Camp | Approach | Best for |
|---|---|---|
| **Prompt-centric** | Rich prompts + few tools; minimal orchestration | Simple, single-step tasks |
| **Tool-centric** | Heavy tool use; model selects and chains tools | Multi-step retrieval/action workflows |
| **Agent-centric** | Agents orchestrate other agents; full multi-agent | Complex, long-horizon tasks |

**7 harness components (Divy Yadav):**

| Component | Role |
|---|---|
| **State management** | Track conversation, task progress, and intermediate results across turns |
| **Tool registry** | Discoverable catalogue of tools with schemas the agent can query |
| **Retry + fallback logic** | Handle transient failures without losing task state |
| **Memory layer** | Short-term (in-context), long-term (vector/file), working (scratchpad) |
| **Observability** | Trace every tool call, token count, and decision for debugging and cost tracking |
| **Guardrails** | Input/output validation, content filtering, loop detection |
| **Handoff protocol** | How agents pass work to each other — structured output schema + acknowledgement |

**Key insight:** the harness is what distinguishes a demo agent from a production agent. Most agent failures in production are harness failures, not LLM failures — the model is fine but the scaffolding doesn't handle edge cases.

> See also [[Claude-Code#Harness engineering]] for the two-agent (Planner/Executor) pattern and [[Claude-Code#Three generations of agent orchestration]] for the adversarial critic pattern.

---

## World models — Yann LeCun's anti-LLM bet (AMI Labs, 2026)

LeCun left Meta in November 2025 to found **AMI Labs** (Advanced Machine Intelligence Labs) in Paris. Seed: **$1.03B** at $3.5B pre-money / $4.5B post-money — Europe's largest seed round ever (Crunchbase). Backers include Bezos Expeditions, Eric Schmidt, Mark Cuban, Jim Breyer, Tim & Rosemary Berners-Lee, Xavier Niel.

**Thesis (NVIDIA GTC 2026):** "LLMs are too limiting. Scaling them up will not allow us to reach AGI." LeCun calls LLMs "an offramp on the path to AGI" — physical understanding requires latent-state prediction from video/sensor data, not autoregressive token prediction on text. AMI's target verticals are healthcare, robotics, industrial process control, automation, wearables, transportation — sectors where reliability and physical grounding matter more than fluent text.

**Three shipping artifacts in 60 days, all built on JEPA (Joint Embedding Predictive Architecture):**

| Project | Date | Headline result | Repo |
|---|---|---|---|
| **AMI Labs** | 2026-03-10 | $1.03B seed announcement | — |
| **V-JEPA 2.1** (arXiv 2603.14482) | 2026-03-16 | Dense-feature video model. 77.3% top-1 on Something-Something v2; 39.7 R@5 Epic-Kitchens-100 SOTA. **V-JEPA 2-AC** trained on <62 hours of unlabeled robot video deployed **zero-shot** on Franka arms in two labs for image-goal pick-and-place. | `facebookresearch/vjepa2` (Apache 2.0, 3.7k★) |
| **LeWorldModel (LeWM)** (arXiv 2603.19312) | 2026-03-27 | First JEPA that trains stably end-to-end from raw pixels with only two losses (next-embedding prediction + Gaussian latent regularization) — no stop-gradient/EMA tricks. **~15M params, single GPU, ~few hours**, up to 48× faster planning than foundation-model-based world models. | `lucas-maes/le-wm` |

**Why it matters in 2026:**
1. LLM scaling curve is visibly bending — GPT-5.5 doubled price ($5/$30 per M tokens) on April 23, 2026; DeepSeek V4-Pro (1.6T params) is still 0.2 pts behind Claude Opus 4.6 on SWE-bench Verified.
2. **David Silver** (AlphaGo lead) raised **$1.1B** for **Ineffable Intelligence** (RL-only "superlearner") on 2026-04-27 — a parallel anti-LLM bet. Two of the most decorated researchers, both ex-DeepMind/Meta, both raised $1B+ within 8 weeks: this is now a cohort, not a contrarian opinion.
3. Robotics demand is real revenue — V-JEPA 2's zero-shot Franka result is what factory-floor automation has been asking for since 2022.

**Counter-argument:** GPT-5.5 hits 88.7% SWE-bench Verified, 82.7% Terminal-Bench 2.0; LLMs ship customer-facing capability today. JEPA has no comparable revenue-generating artifact yet. The scaling thesis may be wrong about AGI but right about $100B revenue lines this decade.

**Run V-JEPA 2 yourself (Linux/WSL — `decord` blocks macOS):**

```bash
git clone https://github.com/facebookresearch/vjepa2.git
cd vjepa2 && conda create -n vjepa2-312 python=3.12 -y
conda activate vjepa2-312 && pip install .
```

```python
import torch
encoder, ac_predictor = torch.hub.load(
    'facebookresearch/vjepa2', 'vjepa2_ac_vit_giant'
)
preprocessor = torch.hub.load('facebookresearch/vjepa2', 'vjepa2_preprocessor')
```

These are the same weights used for the zero-shot Franka pick-and-place demo. Model sizes: ViT-Large, ViT-Huge, ViT-Giant, ViT-Giant-384.

> See also [[AI-Agents]] for harness/orchestration patterns; world models change what "the model" is but agents still need scaffolding.
AMI Labs is **private**. The $1.03B seed was a venture round (Cathay Innovation, Greycroft, Hiro Capital, HV Capital, Bezos Expeditions, plus angels). No ticker, no public shares. Access is limited to LPs in those VC funds or a future secondary round.

**Closest public-market proxies for the world-models / anti-LLM-scaling thesis:**

|Angle|Tickers|
|---|---|
|Robotics arms / industrial automation (V-JEPA's target)|ABB, FANUY, ISRG, ROK, SYM|
|Humanoid / mobile robotics exposure|TSLA (Optimus), NVDA (Isaac/GR00T), GOOGL (DeepMind robotics)|
|Compute substrate either thesis still needs|NVDA, AMD, AVGO, TSM|
|Schmidt-style "AI-physical-world" plays|Public via ETFs: BOTZ, ROBO, ARKQ|

Caveat: none of these _are_ AMI — they just benefit if the JEPA/world-model thesis pans out. If LeCun's bet works, the upside is captured privately first; public markets see it via downstream robotics/compute revenue years later. Not investment advice
---

## See also
- [[Claude-Code]] — Claude is an LLM; understanding prompting makes you more effective with it
- [[Python]] — common language for ML/AI development
- [[Databases-NoSQL]] — vector databases used in AI applications
- [[AI-Agents]] — multi-agent patterns and error compounding in practice
