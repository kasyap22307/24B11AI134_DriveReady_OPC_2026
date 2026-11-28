# Photo Studio & Xerox: AI Customer Assistant

A customer-support chatbot for a photo studio and xerox shop, built in Python with a large language model (LLM). It answers questions about prices, timings and services, remembers the conversation, understands Telugu and English, and sends wedding and video enquiries to the owner instead of guessing.

This is the **Day 2** project of an AI agents course. It builds on the Day 1 setup and covers prompt design, conversation memory, structured JSON output and the core Python used to build agents.

## Features

- **Grounded answers.** The bot answers only from a fixed set of facts (prices, hours, policies). If something is not in the facts, it says it will check with the owner.
- **Wedding and video enquiries.** The bot never quotes a price, package or delivery time for weddings, events, albums or video work. It gives the owner's contact and tells the customer that wedding bookings must be informed at least 2 days in advance.
- **Passport photo rules.** Photos are always given in sets of 8. The bot knows the three price cases (new photo, customer's own photo, and a photo already taken at the shop less than 2.5 years ago).
- **Multilingual replies.** The bot replies in the language the customer writes in, for example a Telugu question gets a Telugu answer.
- **Conversation memory.** A history list is resent on every call and trimmed to the most recent messages, so the bot remembers a customer's name and order.
- **Message classifier.** A few-shot prompt labels each message as `order`, `question`, `wedding_enquiry` or `complaint`.
- **Order extraction to JSON.** A customer message is converted into a structured order (`name`, `service`, `qty`, `urgent`) and parsed with `json.loads`.
- **Price calculator.** A typed, documented `get_price()` function computes totals, rounds passport photos up to sets of 8 and returns `-1` for items not on the menu instead of crashing.
- **Error handling.** Rate limits, connection failures and authentication errors return polite messages instead of stack traces.

## Menu used by the bot

| Service | Price (Rs) |
|---|---|
| Passport photos, we capture the photo (set of 8, ready in 15 minutes) | 100 |
| Passport photos, customer has the photo (set of 8) | 80 |
| Passport photos, photo already taken at the shop less than 2.5 years ago (set of 8) | 60 |
| Xerox black & white | 2 per page |
| Xerox colour | 10 per page |
| Printing from pen drive or WhatsApp | 5 per page |
| 4x6 photo paper printing | 30 per page |
| Lamination A4 | 40 |
| Lamination Aadhaar card | 30 |
| Lamination above A4 size | 60 |
| Big size Aadhaar card | 100 |
| PVC card printing | 100 |

Shop timings: 9 AM to 8 PM, Monday to Saturday. Closed on Sundays.

## Concepts covered

| Topic | Where it appears |
|---|---|
| Messages and roles (`system`, `user`, `assistant`) | `chat()` and `ask_model()` |
| System prompt with Role, Facts, Rules and Format | `SYSTEM` |
| Few-shot examples | `classify()` |
| Asking the model for JSON | `extract_order()` |
| Memory by hand | `history`, `MAX_TURNS` |
| Dicts and JSON | `MENU`, `json.dumps`, `json.loads` |
| Functions with type hints and docstrings | `get_price()` |
| Decorators | `@log_call` |
| `try` / `except` | `ask_model()`, `extract_order()` |

## Repository contents

| File | Description |
|---|---|
| `Day2_My_Business_Bot.ipynb` | The notebook, split into labelled cells. Recommended for Google Colab. |
| `day2_my_business_bot.py` | The same code as a single script. |
| `README.md` | This file. |

## Getting started

### Requirements

- Python 3.9 or newer
- A free [Groq](https://console.groq.com) API key
- The `openai` Python package (the Groq API is OpenAI-compatible)

### Run in Google Colab (recommended)

1. Open [Google Colab](https://colab.research.google.com) and upload `Day2_My_Business_Bot.ipynb` (File, then Upload notebook).
2. Open the key icon in the left sidebar (Secrets) and add a secret named `GROQ_API_KEY` with your Groq API key. Turn on notebook access for it.
3. Choose Runtime, then Run all. Or run the cells one by one from top to bottom.
4. The last cell starts a live chat. Type a message to talk to the bot, and type `bye`, `goodbye`, `exit` or `quit` to stop.

### Run locally

```bash
pip install openai
export GROQ_API_KEY="your-key-here"     # Windows PowerShell: $env:GROQ_API_KEY="your-key-here"
python day2_my_business_bot.py
```

If the key is not set, the script asks for it in the terminal.

> **Security:** never write your API key into the code or commit it to GitHub. Use Colab Secrets or an environment variable.

## Using the bot

Type normal customer messages in the live chat, for example:

```
You: How much to laminate my Aadhaar card?
You: I need 10 passport photos, I have the photo with me
You: How much is a wedding shoot?
You: ఆదివారం షాప్ తెరిచి ఉంటుందా?
```

Special commands in the chat:

| Command | What it does |
|---|---|
| `/order <message>` | Extracts the order from the message as JSON |
| `/classify <message>` | Labels the message as order, question, wedding enquiry or complaint |
| `/history` | Shows how many messages are stored in memory |

## Customising for your own business

All business details are in two cells near the top of the notebook:

- **Business details cell:** `STUDIO_NAME`, `OWNER_NAME`, `OWNER_PHONE` and the `MENU` dictionary.
- **System prompt cell:** the `FACTS` and `RULES` text that controls what the bot may say.

If you change a price, update it in both the `MENU` dictionary and the `FACTS` text, and in the `get_price()` function if you want the calculator to match.

## Experiments to try

1. **Remove the rules.** Delete the `RULES` block in the system prompt, run again and ask "How much for a wedding shoot?". The bot will likely invent a price. This shows why rules matter.
2. **Test memory.** Set `MAX_TURNS = 2`, tell the bot your name, chat for three more turns, then ask your name. It will have forgotten, because the name was trimmed out of the messages sent to the model.
3. **Use the classifier.** Run `/classify` on new messages and see how the three examples guide the labels.
4. **Extract an order.** Run `/order hi Ravi here, need 16 passport photos, urgent` and check the JSON result.

## Tech stack

- Python
- OpenAI Python SDK, pointed at the Groq API (`https://api.groq.com/openai/v1`)
- Model: `openai/gpt-oss-20b`
- Google Colab or Jupyter

## Limitations

- The bot cannot verify when a passport photo was taken. For the lowest passport photo price, staff should confirm the date at the counter.
- The bot's answers come from a language model, so wording can vary between runs. Prices come from the facts in the system prompt.
- Wedding and video details are handled by the owner, so the bot only shares contact details for them.
- Free API tiers have rate limits. If a limit is hit, the bot asks the customer to try again shortly.
