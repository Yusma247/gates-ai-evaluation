# CeRAI AIEvaluationTool: Critique and Alternative
**Gates Foundation AI Fellowship, India 2026 | Option B**
**Yusma Hilal | IIT Delhi | yusma24hilal@gmail.com**

---

## What I Did

I installed the CeRAI AIEvaluationTool and ran its PQET module. I tested it on a maternal health chatbot called Didi. After running 20+ test cases, I found a core problem. The tool never actually talks to a chatbot. You type what you think the chatbot should say. Then it judges your writing. That is not the same as evaluating a real chatbot.

I chose Option B. I documented what is broken. Then I built a small alternative that fixes the main problem.

---

## Repo Structure

```
repo/
├── README.md
├── alternative_evaluator.py
├── API_keys.json   
├── test_cases.md            
└── issues/
    ├── issue1_metric_domain_mismatch.md
    ├── issue2_no_real_endpoint.md
    └── issue3_pii_self_echo.md
└── requirements.txt
```

---

## Setup

You need Python 3.10 or higher and a Gemini API key. Free tier works.

Install packages:

```bash
pip install streamlit google-genai plotly
```

Create a file called `API_keys.json` in the same folder:

```json
{
  "GEMINI_API_KEYS": ["your-gemini-api-key-here"]
}
```

Run the app:

```bash
streamlit run alternative_evaluator.py
```

It opens at `http://localhost:8501`

---

## What the Original Tool Does

PQET works like this. You pick a metric. You type a prompt. You type what you think a good chatbot response looks like. Then Gemini reads both and says whether your test case is well written.

That is all it does. It grades your exam questions. It never gives the exam to a real chatbot.

This is the problem I focused on.

---

## Issues Found

I found three problems through testing.

| Issue | Problem | Severity |
|-------|---------|----------|
| [Issue 1](issues/issue1_metric_domain_mismatch.md) | Wrong metric applied to clinical prompts with no warning | Medium |
| [Issue 2](issues/issue2_no_real_endpoint.md) | Tool never contacts a real chatbot | High |
| [Issue 3](issues/issue3_pii_self_echo.md) | Tool leaks the same patient data it penalizes others for leaking | High |

---

## What My Alternative Does

My alternative fixes the core problem. It sends your prompt to Didi(Healthcare chatbot) and gets a real response back. Then it checks that response.

Here is the pipeline:

```
You type a prompt
      |
Check 1: Is the metric appropriate for this kind of prompt?
      |
Send prompt to Didi (Gemini acting as a maternal health chatbot)
      |
Get the real response back
      |
Check 2: Does the response contain dangerous medical advice?
      |
Assess the real response against your chosen metric
      |
Show the result
```

---

## Why I Made These Design Choices

**Why Gemini as the endpoint?**
The original tool already uses Gemini. I gave Gemini a different role. Instead of being the judge, it plays Didi. Any developer can run this without extra accounts or setup.

**Why a rule-based safety check?**
The original tool relies on Gemini to notice dangerous medical content. A simple keyword check runs before any LLM call. It does not depend on Gemini catching the problem.

**Why a metric compatibility check?**
I tested what happens when you pick a grammar metric for a bleeding emergency prompt. The tool gave a 4/10 score based on sentence complexity. No warning appeared. My alternative catches this before the evaluation runs.

---

## What the Alternative Cannot Do Well

The clinical safety check only catches word combinations I defined in advance. It misses dangerous advice that uses different phrasing.It produces false positives. When Didi correctly warned a user against ibuprofen in pregnancy, my tool flagged it as dangerous. Both words appeared in the response but the advice was actually correct. The check looks at words, not meaning.


The alternative only works with one endpoint. The original tool supports WhatsApp bots, web apps, and custom APIs. Mine only talks to Gemini as Didi.

The alternative only handles one conversation turn. The original supports up to three.

The metric definitions come from an Excel file in the original tool. My alternative uses the metric name but not the detailed submetric definitions. This makes assessments slightly less precise.

Language switching is unreliable. Didi sometimes replies in Hindi to an English prompt if the medical context feels Indian. For this reason, the current implementation instructs Didi to always respond in English.


The alternative does not fix the PII self-echo problem. When a prompt contains patient names and sensitive health details, Didi repeats them in her response. The tool has no output sanitization layer.

---

## Test Cases I Used

All test cases come from scenarios that ASHA workers face in rural India.

- Iron supplement myths in Hindi
- Karela juice vs insulin for gestational diabetes
- MgSO4 dosing with a deliberately wrong answer to test detection
- Ibuprofen safety in pregnancy during high BP
- Newborn fever and paracetamol dosing refusal
- Home abortion methods
- HIV positive patient registration and PII handling
- Oligohydramnios clinical reasoning
- GDM monitoring in resource-poor settings
- Preeclampsia symptoms in Marathi


## Contact

Yusma Hilal
ML Researcher, SENSE Department, IIT Delhi
yusma24hilal@gmail.com
github.com/Yusma247
