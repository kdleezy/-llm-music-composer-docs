# API Reference: LLM-Powered Music Composer

## 🎼 Quick Overview

This app lets you use Large Language Models (LLMs) like Claude Opus and OpenAI's GPT-3.5 Turbo (fine-tuned) to compose original music using structured text prompts.

The system translates natural language queries such as:

> "Write a chord progression in C major in ¾ time."

…into **ABC notation**, a text-based music format commonly used in Celtic folk music. The output can be converted to MIDI files and imported into digital audio workstations (DAWs) or music notation software.

---

## 🔁 Basic Workflow

1. **User Input**: A musician types a plain-English prompt describing the music they want (e.g. key, time signature, chord progression).
2. **LLM Generation**: The app sends the prompt to an LLM (GPT-3.5 or Claude) using a carefully structured system prompt.
3. **Format Validation**: The returned ABC music notation is validated and corrected to be compatible with Python's Music21 library.
4. **MIDI Export**: A MIDI file is generated that can be dragged into Logic Pro, Ableton Live, Sibelius, MuseScore, or any DAW.

This API documentation explains how we structure those requests and responses, and how you can use the underlying models yourself.

---

## 🔐 Authentication

### OpenAI API

All requests to OpenAI’s GPT endpoints require an API key passed via the `Authorization` header:

```http
Authorization: Bearer YOUR_OPENAI_API_KEY
Content-Type: application/json
```

### Claude (Anthropic) API

Claude Opus requires the following headers:

```http
Authorization: Bearer YOUR_ANTHROPIC_API_KEY
Content-Type: application/json
anthropic-version: 2023-06-01
```

Store your API keys securely using environment variables or a secrets manager. Never hardcode them in your source code.

---

## 📥 Prompt Structure in API Requests

To generate accurate ABC music notation, the system prompt must be carefully crafted and passed as part of the `messages` array in each API request. These prompts define the expected output format and musical parameters (such as meter, key, and note length), which guide the model toward producing consistent and parsable results.

> Without a strong prompt structure, the model may omit required headers (`T:`, `M:`, `L:`, `K:`), generate invalid formatting, or return plain text instead of notation.

---

### Claude (Zero-shot prompting)

Claude is not fine-tuned for this task, so the system prompt must include highly specific formatting instructions:

```python
system = (
    "You are a music composer familiar with chord scales and music theory.\n"
    "You compose music in ABC Notation. ONLY respond in ABC Notation. "
    "DO NOT INCLUDE OTHER TEXT! JUST THE ABC NOTATION.\n"
    "DON’T FORGET to set note length like this: \n"
    "L:1/4 or L:1/8 or whichever value fits the composition best.\n"
    "Use square brackets for all chords, e.g., [C E G] or [A C E G].\n"
    "Ensure your output includes T:, M:, L:, and K: fields."
)
```

---

### OpenAI GPT-3.5 Turbo (Fine-Tuned)

The fine-tuned version of GPT-3.5 Turbo is trained on examples that follow this structure, so it requires less detailed prompting:

```python
system = (
    "You are a music composer familiar with chord scales and music theory.\n"
    "You compose music in ABC Notation. ONLY respond in ABC Notation. "
    "DON’T FORGET TO SET NOTE LENGTH like this: L:1/4 or L:1/8.\n"
    "Use square brackets for chords. Do not explain your answer."
)
```

---

## 🧑‍💻 Sample Code – OpenAI GPT-3.5 Turbo (Fine-Tuned)

```python
def translate_to_notation(user_input):
    client = openai.Client()

    chat_completion = client.chat.completions.create(
        messages=[
            {
                "role": "system",
                "content": (
                    "You are a music composer familiar with chord scales and music theory.\n"
                    "You compose music in ABC Notation. ONLY respond in ABC Notation. "
                    "DON'T FORGET TO SET NOTE LENGTH like this: L:1/4 or L:1/8 or whichever length value works best.\n"
                    "Pay attention, ensure ALL chords are explicitly written as groups of notes in "
                    "square brackets for compatibility with music parsing libraries."
                )
            },
            {
                "role": "user",
                "content": user_input
            }
        ]
    )
```

---

## 🧑‍💻 Sample Code – Claude Opus (Zero-Shot)

```python
def translate_to_notation(user_input):
    client = anthropic.Anthropic(
        api_key=os.environ.get("ANTHROPIC_API_KEY", "MY_KEY")
    )

    message = client.messages.create(
        model="claude-3-opus-20240229",
        max_tokens=1000,
        temperature=0,
        system=(
            "You are a music composer familiar with chord scales and music theory.\n"
            "You compose music in ABC Notation. ONLY respond in ABC Notation. "
            "DO NOT INCLUDE OTHER TEXT! JUST THE ABC NOTATION.\n"
            "DON’T FORGET set note length like this: \n"
            "L:1/4 or L:1/8 or whichever length value works best for compatibility with music libraries. \n"
            "Pay attention, ensure ALL chords are explicitly written as groups of notes in "
            "square brackets for compatibility with music parsing libraries."
        ),
        messages=[
            {
                "role": "user",
                "content": user_input
            }
        ]
    )
```

---

## 📤 OpenAI API Request Example

```http
POST https://api.openai.com/v1/chat/completions
```

### Request Body:

```json
{
  "model": "ft:gpt-3.5-turbo:your-org:model-id",
  "messages": [
    {
      "role": "system",
      "content": "You are a music composition assistant..."
    },
    {
      "role": "user",
      "content": "Write a chord progression in F major"
    }
  ],
  "temperature": 0.7
}
```

---

## 📤 Claude API Request Example

```http
POST https://api.anthropic.com/v1/messages
```

### Request Body:

```json
{
  "model": "claude-3-opus-20240229",
  "max_tokens": 512,
  "temperature": 0.7,
  "system": "You are a music composition assistant. Write ABC music notation only...",
  "messages": [
    {
      "role": "user",
      "content": "Write a chord progression in F major"
    }
  ]
}
```

---

## ✅ Notation Requirements

All LLM responses must return valid ABC music notation with the following fields:

| Field       | Purpose                                      |
|-------------|----------------------------------------------|
| `T:`        | Title of the piece                           |
| `M:`        | Meter (e.g., 3/4, 4/4)                        |
| `L:`        | Default note length (e.g., L:1/4)             |
| `K:`        | Key signature (e.g., K:F)                    |
| Brackets    | Used for chords (e.g., `[C E G]`)             |
| `^`, `_`    | Represent sharps and flats, respectively      |

### Example:

```abc
T:ii V I Jazz Progression
M:4/4
L:1/4
K:G
[A C E] | [D F# A] | [G B D] ||
```

---

## ⚠️ Error Handling

After an API response is received, the app:

1. Verifies the presence of ABC headers (`T`, `M`, `L`, `K`)
2. Ensures proper bracket formatting for chords
3. Converts sharps (`#`) and flats (`b`) into `^` and `_` for compatibility with the Music21 library
4. Logs a `fail` message and skips conversion if formatting is invalid

---

## 💡 Prompt Tuning Tips

- Use structured output in both user prompts and system messages
- Claude responds best with highly explicit formatting instructions
- GPT models may be fine-tuned with a JSONL dataset containing ABC samples and chord progression patterns
- Provide chord function training (e.g., ii-V-I) for better results

---

## 📬 Contact

For additional information or questions about integration, reach out to the developer or contributor managing the project.
