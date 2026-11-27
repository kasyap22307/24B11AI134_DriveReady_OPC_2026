# Day 1 - How LLMs Work and My First API Call

**Business used:** Satish V Mix Photo Studio & Xerox Shop

## Task Description

Learn how Large Language Models work (tokens, temperature, memory, context window, hallucination) and make a first API call to an LLM. Then build a small customer-support assistant that answers questions using real business facts, and test it with different experiments.

## My Idea

I built an assistant for my own business, a **photography studio and xerox shop**. It answers customer questions about passport photos, xerox prices, lamination, printing and opening hours. For wedding and video enquiries, it never quotes a price and shares the owner's contact instead.

## Implementation

- **Model / API:** `openai/gpt-oss-20b` through Groq, using the OpenAI-compatible Python client.
- **System message:** contains the business ROLE, FACTS (prices, timings, owner contact), RULES (use only the facts, never invent prices, refer wedding/video enquiries to the owner) and FORMAT (short, friendly, same language as the customer).
- **Temperature:** `0.2` for the assistant, so prices and answers stay consistent.
- **`ask()` helper:** sends one request with a system message and a user message, and returns the reply and token usage.
- **API key safety:** the key is read from Colab Secrets (or typed with `getpass` in Jupyter) and is never written in the code.

## Experiments

| # | Experiment | What I did |
|---|------------|-----------|
| 1 | Temperature | Asked for a tagline 3 times at 0.2 and 3 times at 1.2 |
| 2 | Hallucination | Asked the same question with and without FACTS and RULES |
| 3 | No memory | Asked "My name is Ravi..." and then "What is my name?" in separate calls, then in one `messages` list |
| 4 | Tokens | Compared `prompt_tokens` for the same message in English and Telugu |
| 5 | Out-of-facts questions | Asked things not in the facts, in English and Telugu |

## How to Run

1. Get a free API key from [console.groq.com](https://console.groq.com).
2. **Google Colab:** add the key under Secrets (key icon) as `GROQ_API_KEY` and turn on Notebook access.
   **Jupyter:** run the key cell and enter the key when asked.
3. Install the library: `pip install openai`
4. Open `LLM_Setup.ipynb` and run the cells from top to bottom, or run `python task.py` after setting `GROQ_API_KEY` as an environment variable.

## Sample Output

> Replace each block below with your own real output.

**Build (studio assistant):**
```
Q: How much for 50 black and white xerox copies?
A: 50 black & white xerox copies cost Rs 100.

Q: How much do you charge for a wedding shoot?
A:  For wedding and video enquiries, please contact the owner: Satish, 9876543210.
```

**Hallucination test:**
```
WITHOUT FACTS: Our passport photos cost ₹50 each. We offer delivery to Kakinada. Just let us know your address and we’ll bring them right to you!
WITH FACTS:    Passport photos cost Rs 100 for 8 copies. Delivery to Kakinada? I'll check with the owner. Satish, 9876543210.
```

**Token comparison:**
```
English prompt_tokens: 96
Telugu  prompt_tokens: 106
```

## What I Learned

- An LLM predicts the next token, and everything (cost and limits) is measured in tokens.
- Low temperature gives consistent answers, which suits prices and facts.
- The model has no memory. The app has to resend the conversation each time.
- Without facts, the model hallucinates. Giving facts and clear rules in the system prompt keeps answers accurate.
- A stricter, more specific prompt fixed the mistakes I saw in my first run.

## Files

- `LLM_Setup.ipynb` - the notebook with all the code and outputs
- `task.py` - the same code as a Python script
- `README.md` - this file
