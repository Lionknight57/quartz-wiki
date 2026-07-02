---
title: GPU and Hardware for AI
tags: [gpu, hardware, vram, llm, local-inference]
related: [AI-ML, Python]
sources: [Learn/raw/AI/COURSE Getting Started with Tensorflow 2.0.md, Learn/raw/Tensor Flow.md, Learn/raw/AI/COURSE Getting Started with Tensorflow 2.0_distilled.md, Medium/2026-04/Medium Daily Digest - 2026-04-16.md, Medium/2026-04/Medium Daily Digest - 2026-04-21.md, Medium/2026-05/Medium Daily Digest - 2026-05-27.md]
updated: 2026-05-28
---

# GPU and Hardware for AI

Computing power is the primary constraint for training and running Large Language Models (LLMs). While CPUs can run models, GPUs (Graphics Processing Units) and TPUs (Tensor Processing Units) are required for practical performance due to their ability to handle massive parallel matrix operations.

## VRAM and Model Fitting
The critical hardware metric for local LLM inference is **VRAM (Video RAM)**. A model must fit within the available VRAM to run at acceptable speeds; otherwise, it must "offload" to system RAM, which is significantly slower.

### VRAM Estimation Table
The amount of VRAM needed depends on the model size (parameters) and the **quantisation** level (how many bits are used to represent each weight).

| Model Size | Quantisation | VRAM Needed | Note |
|---|---|---|---|
| 7B | 4-bit (GGUF Q4) | ~4 GB | Entry-level consumer GPUs |
| 7B | 8-bit | ~8 GB | High quality, standard GPUs |
| 13B | 4-bit | ~8 GB | Balanced performance |
| 30B | 4-bit | ~16 GB | Mid-to-high end consumer GPUs |
| 30B | 2-3 bit | ~8–12 GB | Viable on edge hardware (e.g., Raspberry Pi) |
| 70B | 4-bit | ~40 GB | High-end workstation (A100/H100 or multi-GPU) |
| 70B | 2-bit | ~20 GB | Minimum for massive models on consumer gear |

**Key Tool:** The "LLM GPU fit tool" is used by the community to calculate if a specific model version will fit in available VRAM before downloading.

## Hardware Ecosystem (2026)

### Consumer Hardware
- **NVIDIA RTX Series (e.g., 4090):** The gold standard for local LLMs due to CUDA cores and high VRAM.
- **Apple M-Series (Unified Memory):** Highly effective because the GPU can access the entire system RAM as VRAM, allowing larger models (70B+) to run on high-spec Macs.
- **Raspberry Pi:** Recent milestones show that highly quantised 30B models (Qwen) can now achieve real-time inference on sub-$100 hardware.

## Hardware Ecosystem (2026)

### Consumer Hardware
- **NVIDIA RTX Series (e.g., 4090):** The gold standard for local LLMs due to CUDA cores and high VRAM.
- **Apple M-Series (Unified Memory):** Highly effective because the GPU can access the entire system RAM as VRAM, allowing larger models (70B+) to run on high-spec Macs.
- **Raspberry Pi:** Recent milestones show that highly quantised 30B models (Qwen) can now achieve real-time inference on sub-$100 hardware.

### Specialized AI Hardware
- **TPUs (Tensor Processing Units):** Google's custom ASICs designed specifically for TensorFlow/JAX workloads. They provide massive acceleration for tensor operations and are available via Google Cloud infrastructure to empower developers to build NLP and AI projects without managing underlying hardware.
- **Edge AI:** Small-scale models like **Nemotron 3 Nano** are designed specifically for on-device deployment where VRAM is extremely limited.

## TensorFlow Hardware Integration
TensorFlow provides specific abstractions to manage hardware:
- **Distributed Execution:** Ability to run models across a cluster of machines or multiple GPUs/CPUs on a single machine.
- **TensorFlow Lite:** Optimises models for mobile and embedded devices, reducing memory footprint and increasing inference speed.
- **TensorFlow Serving:** High-performance system for deploying models to production servers.

## Production Realities
- **Resource Intensity:** Deep learning is computationally expensive. Training requires massive GPU clusters, while inference requires a specific VRAM "floor" to be functional.
- **Bottlenecks:** Performance is often limited by memory bandwidth (how fast data moves to the GPU) rather than raw compute power.

## Local Inference Runtimes (2026)

| Runtime | Best for | Notes |
|---|---|---|
| **llama.cpp** | Raw performance — fastest tokens/sec | 30–70% faster than Ollama on same model. Now has router mode + web UI. GGUF first-class |
| **LM Studio** | GUI + simplicity | Polished interface, proper GGUF support, model switching, regularly updated llama.cpp backend |
| **vLLM** | Multi-user / production | Proper concurrency, production-ready, industry standard |
| **SGLang** | Multi-user / production | Alternative to vLLM for production concurrency |
| **oMLX / MLX** | Mac / Apple Silicon | Native Apple Silicon, continuous batching, MTP support |
| **Ollama** | ⚠️ Caution (2026) | See below |

### Ollama — 2026 status

Ollama was the go-to local inference tool in 2023–2025 but has accrued significant problems. Key issues:

- **30–70% slower than llama.cpp** on same model (confirmed by llama.cpp creator Georgi Gerganov via X): Ollama's MXFP4 kernels have too much branching, attention sinks implementation is inefficient
- **Proprietary model format** (2024–2025): forked ggml, stored models in hashed filenames in their own registry — models were trapped, couldn't use with llama.cpp/LM Studio. Switched back to llama.cpp in v0.30.0-rc15 (May 2026) due to falling behind on new architectures (MTP, structured output, hybrid attention) and community pressure
- **Ollama Cloud** reliability (as of May 2026): 29.7% failure rate on Qwen3.5, 95% failure rate reported across all models, 60+ second timeouts, broken tool calling, hostile rate limiting ($100/month users throttled after 5 days)
- **Misleading model naming**: listed `DeepSeek-R1-Distill-Qwen-32B` simply as "DeepSeek-R1" — drove confusion about actual model capabilities
- **Trust broken**: was local-first, now VC-backed platform company pushing cloud

**Practical advice:** continue using Ollama for embeddings + simple local tasks where you already have it set up. For new local inference infrastructure, prefer llama.cpp directly or LM Studio for GUI. For production multi-user, use vLLM or SGLang.

> Source: Andrew Zhu — "Why You Should Completely Avoid Ollama in 2026" (2026-05-27, 377 claps, 619 claps total reactions)

---

**See also:**
- [[AI-ML]] — for the mathematical foundations of the patterns GPUs accelerate.
- [[Python]] — the primary language used to interface with GPU hardware via libraries like PyTorch and TensorFlow.
