# Improving a Maternal Health Chatbot Evaluation Tool

## Context

CeRAI's AIEvaluationTool (PQET module) is designed to evaluate chatbot responses for maternal health use cases. After testing it on a maternal health chatbot called Didi with 20+ test cases, I found a core design flaw. The tool never actually talks to a chatbot. You type what you think the chatbot should say, and it judges your writing instead of a real response.

This repo documents the issues I found and provides a working alternative that fixes the main problem.

## What I Found

| Issue | Problem | Severity |
|-------|---------|----------|
| [Issue 1](issues/issue1_metric_domain_mismatch.md) | Wrong metric applied to clinical prompts, no warning given | Medium |
| [Issue 2](issues/issue2_no_real_endpoint.md) | Tool never contacts a real chatbot | High |
| [Issue 3](issues/issue3_pii_self_echo.md) | Tool leaks the same patient data it penalizes others for leaking | High |

## What I Built

An alternative pipeline that sends the prompt to a real chatbot endpoint, gets an actual response, and evaluates that response instead of a hypothetical one.

## Setup

Python 3.10 or higher, Gemini API key (free tier works).

```bash
pip install streamlit google-genai plotly
```

Create `API_keys.json`:
```json
{
  "GEMINI_API_KEYS": ["your-gemini-api-key-here"]
}
```

Run:
```bash
streamlit run alternative_evaluator.py
```

It opens at `http://localhost:8501`

## Design Decisions

**Real endpoint instead of self graded text.** The original judges what you wrote, not what a chatbot says. My version closes that gap.

**Rule based safety check before the LLM call.** This does not rely on Gemini noticing dangerous content on its own.

**Metric compatibility check.** The original gave a 4/10 on a bleeding emergency prompt based on sentence grammar with no warning. Mine flags mismatched metrics before running the evaluation.

## Known Limitations

- Clinical safety check is keyword based. It misses rephrased dangerous advice and produces false positives, such as flagging a correct ibuprofen in pregnancy warning as dangerous because both trigger words appeared
- Single endpoint only (Gemini acting as Didi). The original supports WhatsApp, web apps, and custom APIs
- Single turn only. The original supports up to three turns
- Uses metric names but not the original's detailed submetric definitions
- Does not fix the PII self echo problem. There is no output sanitization layer yet

## Test Cases

Rural India and ASHA worker scenarios including iron supplement myths, Karela juice vs insulin for GDM, MgSO4 dosing errors, ibuprofen safety in pregnancy, newborn fever dosing, home abortion methods, HIV positive PII handling, oligohydramnios reasoning, GDM monitoring, and preeclampsia symptoms in Marathi.

## Contact

Yusma Hilal, ML Researcher, SENSE Department, IIT Delhi
yusma24hilal@gmail.com
