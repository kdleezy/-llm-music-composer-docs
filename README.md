# Compose Music with Text Prompts – LLM + Python + ABC Notation

This repo shows you how to compose chord progressions with [Claude 3.7 Sonnet](https://docs.anthropic.com/en/docs/welcome). You give the model a prompt (like "Write a chord progression in C major in ¾ time") and it returns music in ABC notation. Here's a [quick tutorial on ABC notation](https://notabc.app/abc/basics/) if you want to learn more about folk music notation style. 

Try it in Google Colab or run the scripts locally.

---

## 🔗 Try It in Colab

[Open in Google Colab](https://colab.research.google.com/github/YOUR_USERNAME/llm-music-composer-docs/blob/main/demo.ipynb)  

---

## How It Works

1. You write a prompt like `"Write a chord progression in F major in 4/4"`
2. The prompt is sent to Claude.
3. The model responds with music in ABC notation.
4. A Python script checks and cleans the notation (fixes sharps/flats).
5. The Music21 library converts the ABC notation into a MIDI file.
6. You can drag the MIDI into your DAW (recording software) or notation software.

---

## 📘 Docs

| File | What's Inside |
|------|----------------|
| [`api-reference.md`](./api-reference.md) | How we call Claude to generate music |
| [`composer-functions.md`](./composer-functions.md) | Python code to clean notation and make MIDI files |

---

## Stack

- Python
- Claude Opus (Anthropic)
- Music21
- ABC Notation

---

## 📎 License

Shared under the [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) license.
