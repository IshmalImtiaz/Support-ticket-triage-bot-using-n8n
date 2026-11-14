# Support Ticket Triage Bot

An n8n workflow that automatically classifies incoming support tickets using an LLM, returning a category, urgency level, and one-sentence summary as structured JSON.

## Problem
Manually reading and categorizing every support request is slow and inconsistent. This workflow automates the first triage step.

## What it does
1. A webhook receives a ticket's message text via POST request.
2. The message is sent to an LLM (Groq, running `openai/gpt-oss-120b`) with a system prompt that:
   - Defines exact category and urgency options
   - Explicitly instructs the model to treat the ticket text as data, not as instructions (basic prompt-injection resistance)
3. The model's JSON reply is parsed into clean fields: `category`, `urgency`, `summary`.
4. The result is returned as the webhook's response.

## Example
**Request:**
```json
POST /webhook/triage
{ "message": "I was charged twice for my subscription this month." }
```
**Response:**
```json
{ "category": "billing", "urgency": "medium", "summary": "Customer was double-charged for their subscription." }
```

## Tech used
n8n, Groq API (OpenAI-compatible chat completions endpoint), webhook-based architecture

## Setup
1. Import `triage-eval.json` into n8n.
2. In the HTTP Request node, replace the Authorization header value with your own Groq API key: `Bearer API_key`.
3. Activate the workflow and send a POST request to the webhook URL n8n provides.

## Notes
Category and urgency are fixed, closed sets defined in the prompt, not free text, this keeps the output consistent and easy for downstream systems to act on.
