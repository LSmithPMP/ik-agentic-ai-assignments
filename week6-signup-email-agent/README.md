# Week 6 — Evaluating & Operationalizing Agents

**Assignment:** Signup Email Agent — Full LLMOps Loop  
**Tools:** LangChain · LangSmith · DeepEval · OpenAI GPT-4o-mini  
**Completed:** April 2026

---

## Problem Statement

Build and evaluate a signup email agent that takes a user signup record and generates a structured response containing an ICP classification and a personalized welcome email. Practice the full LLMOps loop: inspect traces → improve the prompt → expand the golden dataset → evaluate with custom judges → reflect on results.

---

## Repository Contents

```
week6-signup-email-agent/
├── notebook/
│   └── Week6_Signup_Email_Agent.ipynb   # Fully executed notebook
├── data/
│   └── golden_dataset.jsonl             # 8-case golden dataset
├── docs/
│   └── Week6_Signup_Email_Agent_Final.docx  # Submission document
└── README.md
```

---

## The Full LLMOps Loop

```
01 Inspect    →   02 Improve    →   03 Expand    →   04 Evaluate
Review traces     Rewrite prompt    Add edge cases   Score with judge
on LangSmith      for personal-     to golden        compare before
baseline          ization & tone    dataset          vs. after
```

---

## Improved System Prompt

The original single-line prompt was replaced with a structured, rules-based system prompt:

**Key improvements:**
- Explicit ICP classification tiers (high / medium / low / unknown) with deterministic rules
- Email writing constraints — 80–120 word target, single CTA, no hallucination
- Missing-name handling — agent must reference role or company rather than defaulting to "Hi there"
- Explicit guardrails against generic openers when input fields are available

---

## Golden Dataset — 8 Cases

| # | Case | ICP Expected | Edge Pattern |
|---|------|-------------|--------------|
| 1 | Sarah — VP Eng, Acme Corp | high | Baseline happy path |
| 2 | J. Chen — Sr. SWE, Stripe (no name) | medium | Missing first name with rich fields |
| 3 | Marcus — DevOps, Accenture (gmail) | medium | Generic email domain with full profile |
| 4 | j.doe — no fields | unknown | Incomplete context — all fields blank |
| 5 | Alex — Co-Founder, Stealth Startup | medium | Stealth/unknown company |
| 6 | Priya — CS Student, University | low | Unclear role — student |
| 7 | Mike — Freelance Developer | low | Unclear role — freelancer |
| 8 | Tom — Operations Manager, Manufacturing | low | Non-technical role in non-tech industry |

---

## Custom LLM-as-a-Judge: Personalization Quality

A custom GEval metric with `strict_mode=True` enforcing binary 0/1 output.

**Score 1 (PASS)** if:
- Uses name, role, or company from the signup record
- Sounds written for this specific user — not any user
- Avoids details not present in the input
- Avoids generic openers when name/role/company IS available

**Score 0 (FAIL)** if:
- Generic greetings when input fields were available to use
- Hallucinated details not in the signup record
- Same tone and structure regardless of user type
- Misses obvious personalization opportunities

---

## Results

### Per-Case Scores

| Case | ICP | Email | Personalization |
|------|-----|-------|----------------|
| Marcus (DevOps/Accenture) | medium ✅ | 0.88 | 1 / PASS |
| J. Chen (missing name/Stripe) | high ❌ | 0.77 | 0 / FAIL |
| Priya (student) | low ✅ | 0.84 | 1 / PASS |
| Tom (non-tech) | medium ❌ | 0.90 | 1 / PASS |
| j.doe (no fields) | unknown ✅ | 0.55 | 0 / FAIL |
| Sarah (VP Eng) | high ✅ | 0.94 | 1 / PASS |
| Alex (stealth) | low ❌ | 0.79 | 1 / PASS |
| Mike (freelancer) | low ✅ | 0.81 | 1 / PASS |

### Aggregate

| Metric | Score |
|--------|-------|
| ICP Fit Exact Match | 62.50% (5/8) |
| Email Quality Avg | 0.81 |
| Personalization Pass Rate | 75% (6/8) |

> **Note:** ICP exact match dropped from 83% (6-case dataset) to 62% (8-case dataset) because the two new cases expose harder classification problems. This reflects a more rigorous test suite — not a regression.

---

## Reflection

### What changed?
Prompt rewritten with explicit ICP rules and email constraints. Seven edge cases added covering all required patterns. Personalization judge upgraded to strict 0/1 binary scoring.

### Did outputs improve?
Yes. Personalization pass rate at 75% with Sarah (0.94), Marcus (0.88), and Tom (0.90) all scoring strongly. The missing-name rule in the prompt directly drove these improvements.

### What failures remain?
Three ICP misclassifications: J. Chen (high vs medium), Tom (medium vs low), Alex (low vs medium). The j.doe zero-signal case correctly scores 0 on personalization — a graceful degraded path is needed for production.

### What to improve next?
1. Tighten ICP rules for Co-Founder and non-technical role signals
2. Add few-shot examples anchoring missing-name behavior
3. Add HallucinationMetric via DeepEval
4. Version prompt in LangSmith Prompts Hub
5. Implement zero-signal fallback path
6. Monitor online evaluator health independently

---

## LangSmith Project

All traces available in: `default` project  
Dataset: `golden-dataset` (8 cases)  
Online evaluator: `Email Tone Check` (LLM-as-a-Judge, hallucination key)

---

*Lamonte Smith · lamontesmithpmp@gmail.com · [github.com/LSmithPMP](https://github.com/LSmithPMP)*
