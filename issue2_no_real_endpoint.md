# Issue 2: The Tool Never Contacts a Real Chatbot

## Description

PQET asks you to type an expected response yourself. It then judges whether your writing is a good test case. It never sends your prompt to a chatbot. It never gets a real response back.

This means you are evaluating your own imagination of what a chatbot might say. You are not evaluating what it actually says.

## Steps to Reproduce

1. Open PQET
2. Pick any metric
3. Type a prompt
4. Look at the interface

There is no button to connect to a chatbot. There is no URL field. There is no way to retrieve a real response. The only option is to type the expected response yourself.

**Demonstration from my testing:**

I wanted to test the over-refusal metric. I needed a chatbot response that refuses a basic question. I sent the prompt to Gemini directly. It gave a helpful answer. To get a refusal into the PQET field, I had to tell Gemini to be overly cautious on purpose. Then I copied that fake refusal into the expected response box.

I was testing a response I constructed myself. Not a response the chatbot would actually give. The test case was fictional.

## Impact

A hospital deploying a maternal health chatbot cannot use this tool to check whether the chatbot is safe. They can only check whether their manually written test cases are well designed. Those are very different things.

If the chatbot hallucinates a drug dose in production, PQET will not catch it. It has no way to see what the chatbot actually produces.

## Suggested Fix

Add a live endpoint mode. The user provides an API endpoint or a URL. The tool sends the prompt there. It gets the real response back. Then it evaluates that response. The existing Test Case Executor module in the CeRAI codebase already has Selenium logic for connecting to endpoints. That logic could be connected to PQET's evaluation step.

A simpler short-term fix: add a text field where users can paste a real chatbot response they obtained externally. This would at least separate the human-written expected response from an actual chatbot output.
