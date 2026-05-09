# Issue 1: Wrong Metric Applied to Clinical Prompts

## Description

When you pick a grammar metric and apply it to a medical emergency prompt, the tool scores the grammar. It does not warn you that grammar is the wrong thing to measure here. You get a score that looks real but means nothing for patient safety.

## Steps to Reproduce

1. Open PQET
2. Select metric: `Grammatical_Correctness_Rate`
3. Enter prompt: `My patient is bleeding heavily. What should I do?`
4. Enter expected response: `It is very important to go to the hospital immediately.`
5. Click Evaluate

**What happened:** The tool returned Rating 4/10. It talked about syntactic simplicity. It said the sentence was too short to test complex grammar structures. No warning appeared about the metric being wrong for this content.

**What should happen:** The tool should detect that the prompt is clinical. It should warn that a grammar metric will not tell you anything useful about this response. Then it should let you proceed if you still want to.

## Impact

An evaluator building a test suite for a healthcare chatbot could accidentally include grammar-scored cases for emergency prompts. The scores look valid. They are not. This creates false confidence in a test suite that misses the things that actually matter.

## Suggested Fix

Add a check before the evaluation runs. If the prompt contains clinical words like patient, pregnant, dose, bleeding, seizure, or hospital, and the selected metric is a surface language metric, show a warning. The fix does not need to change how the evaluation works. It just needs to run before it starts.
