# Security Policy

## Overview

This repository contains educational assignment work for the Interview Kickstart Applied Agentic AI program. It includes Jupyter notebooks, evaluation datasets, and documentation. No production systems or sensitive data are stored here.

## Sensitive Data Handling

- **API keys are never hardcoded.** All notebooks use environment variables or Google Colab `userdata.get()` for secrets.
- **No PII is stored.** All dataset records use fictional or anonymized signup data.
- **No credentials, tokens, or private keys** are committed to this repository.

## Reporting a Vulnerability

If you discover a security issue in any code within this repository, please report it responsibly:

**Contact:** lamontesmithpmp@gmail.com  
**Subject line:** `[SECURITY] ik-agentic-ai-assignments — <brief description>`

Please include:
- A description of the vulnerability
- Steps to reproduce
- Potential impact assessment

I will respond within 72 hours and work to address confirmed issues promptly.

## Dependencies

Notebooks in this repository use the following packages. Keep these updated in your own environments:

| Package | Purpose |
|---|---|
| `langchain` | Agent orchestration |
| `langchain_openai` | OpenAI LLM integration |
| `langsmith` | Tracing and evaluation |
| `deepeval` | Offline evaluation metrics |
| `pydantic` | Structured output validation |

## Best Practices for Contributors

- Never commit `.env` files or files containing API keys
- Use `userdata.get()` in Colab or environment variables in local environments
- Review all outputs before committing — LLM responses may occasionally contain unexpected content

---

*Last updated: April 2026*
