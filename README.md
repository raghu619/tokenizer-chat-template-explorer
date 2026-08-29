# Tokenizer & Chat Template Explorer

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/raghu619/tokenizer-chat-template-explorer/blob/main/AI_Text_Understanding_Explorer.ipynb)

A side-by-side look at how four modern instruct LLMs turn the *same* text into the numbers they actually read.

This is a learning project from my journey into AI Engineering. It makes one idea concrete: **an LLM never sees your words — it sees a sequence of integers, and every model maps text to integers differently.**

## What it does

Takes one identical sentence and one identical chat conversation, then runs them through the tokenizers of four models:

| Model | Made by | Notes |
|-------|---------|-------|
| Llama 3.1 8B Instruct | Meta | gated model |
| Phi-4-mini Instruct | Microsoft | open |
| DeepSeek-V3.1 | DeepSeek AI | 600B+ model, tiny tokenizer |
| Qwen2.5-Coder 7B Instruct | Alibaba Cloud | code-specialised |

For each model the notebook shows:

1. **Encoding** — text becomes token IDs, and how many tokens result
2. **The actual tokens** — the sub-word pieces a word gets split into
3. **Vocabulary size** — how many distinct tokens the model knows
4. **Chat templating** — how `apply_chat_template` wraps a conversation in each model's own special tokens
5. **A summary table** comparing all of the above
6. **Bonus** — how a code-specialised tokenizer handles source code

## What I found

The headline surprised me. I expected a bigger vocabulary to mean fewer tokens. It barely mattered on plain English:

| Model | Vocab size | Tokens (raw sentence) | Tokens (chat formatted) |
|-------|-----------:|----------------------:|------------------------:|
| Llama-3.1 | 128,000 | 20 | 52 |
| Phi-4-mini | 200,019 | 19 | 21 |
| DeepSeek-V3.1 | 128,000 | 18 | 20 |
| Qwen-Coder | 151,643 | 19 | 29 |

The same 84-character sentence is 18–20 tokens everywhere, even though Phi's vocabulary is 56% larger than Llama's. The real divergence shows up when you wrap text as a *conversation*: formatting the same chat costs Llama 32 extra tokens but Phi only 2 — a 16x difference. Llama's chat template auto-injects a "Cutting Knowledge Date" and "Today Date" line into every single call; the other three don't.

Takeaways:

- Token count is a property of the model, not the text. It drives context limits and API cost.
- Tokenization is sub-word: common words are one token, rare/long words get split.
- The conversation *wrapper* is where models really diverge, and most people never look at it.
- Never hand-build chat strings. Let `apply_chat_template` produce the format each model was trained on.
- You only download tokenizers (a few MB), never model weights, so even a 600B model is usable on free Colab CPU.

## Run it

Open the notebook in Google Colab (free tier, CPU is fine):

1. Add your Hugging Face token as a Colab secret named `HF_TOKEN` (the key icon in the left sidebar). Llama 3.1 is gated — you need an approved access request on Hugging Face first.
2. Run the cells top to bottom.

## Tech

`transformers` · `AutoTokenizer` · `apply_chat_template` · pandas · Google Colab

## What's next

Moving from the high-level tokenizer API to the low-level Transformers API — loading an actual model and watching these token IDs flow through embeddings and attention to predict the next token. This project is the input side of that pipeline.

---

*Part of my public AI Engineering learning journey. Built to understand, not to impress.*
