# Week 7 — Optimizing & Fine-Tuning Agents

**Assignment:** Customer Support Ticket Classifier — From Zero-Shot to Fine-Tuned  
**Tools:** OpenAI API · HuggingFace Transformers · PEFT/LoRA · 4-bit Quantization · Google Colab T4 GPU  
**Completed:** April 2026

---

## What Was Built

A customer support ticket classifier that routes tickets into four categories — billing, technical, account, shipping — built four different ways and benchmarked across accuracy, cost, and latency.

---

## Results

| Approach | Accuracy | Time | Cost/1K | Setup |
|---|---|---|---|---|
| Zero-Shot GPT-4o-mini | 92.9% | 12.5s | $0.02 | None |
| Few-Shot GPT-4o-mini | 92.9% | 7.9s | $0.03 | ~1 hour |
| Few-Shot GPT-4o | **100%** | 11.6s | $0.40 | ~1 hour |
| LoRA SmolLM2-1.7B | 78.6% | 27.7s | ~$0 | ~1 week |

---

## Key Findings

- Zero-shot on GPT-4o-mini achieved 92.9% with zero setup — the correct starting point for any new classifier
- Few-shot improved latency 37% (12.5s → 7.9s) but not accuracy on the same model
- GPT-4o hit 100% — resolving the one ambiguous ticket every other approach missed
- LoRA underperformed at 78.6% due to insufficient training data (~13 examples per category), not model limitations
- The LoRA adapter is 12.6MB vs the 3.4GB full model — a 270x compression ratio

---

## The LoRA Insight

The 270x compression ratio reframes the infrastructure conversation. The base model is shared. Each task-specific adapter is a lightweight deployment artifact. An organization running five classifiers deploys one base model and five adapters totaling under 100MB — not five 3.4GB models.

---

## Decision Framework

| Scenario | Recommendation | Rationale |
|---|---|---|
| Startup, 100 tickets/day | Few-Shot GPT-4o-mini | Fine-tuning has a 3-year break-even at this volume |
| Enterprise, 100K tickets/day | Fine-tuned 7B+ open-source | GPT-4o costs $40K/day at this volume |
| Healthcare, any volume | Self-hosted fine-tuning | Data residency — HIPAA blocks any external API |

---

## Contents
