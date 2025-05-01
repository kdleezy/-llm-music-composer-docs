# Compose Music with Text Prompts – LLM + Python + ABC Notation

This repo shows you how to compose chord progressions with [Claude 3.7 Sonnet](https://docs.anthropic.com/en/docs/welcome). You give the model a prompt (like "Write a chord progression in C major in ¾ time") and it returns music in ABC notation, which we convert to a MIDI file with a Python script. Then, you can drag and drop the MIDI file into your recording software or re-run the script to generate a new MIDI file. 

Try it in Google Colab or run the scripts locally.

---

## 🔗 Try It in Colab

[Open in Google Colab](https://colab.research.google.com/github/YOUR_USERNAME/llm-music-composer-docs/blob/main/demo.ipynb)  

---

## How It Works

1. You write a prompt like `"Write a chord progression in F major in 4/4"`.
2. The prompt is sent to either Claude or GPT-3.5 (fine-tuned).
3. The model responds with music in ABC notation.
4. A Python script checks and cleans the notation (fixes sharps/flats).
5. The Music21 library converts the ABC into a MIDI file.
6. You can drag the MIDI into your DAW or notation software.

---

## 📘 Docs

| File | What's Inside |
|------|----------------|
| [`api-reference.md`](./api-reference.md) | How we call GPT-3.5 and Claude to generate music |
| [`composer-functions.md`](./composer-functions.md) | Python code to clean notation and make MIDI files |

---

## Stack

- Python
- OpenAI GPT-3.5 Turbo (fine-tuned)
- Claude Opus (Anthropic)
- Music21
- ABC Notation

---

## 📎 License

Shared under the [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) license.
