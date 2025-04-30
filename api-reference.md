# API Reference – Compose Music with Claude Sonnet

This guide shows you how to use Anthropic's Claude Sonnet model to generate ABC music notation from a simple text prompt. You'll send a prompt like "Write a chord progression in C major" and receive structured musical notation. Then, you'll convert that into a MIDI file with Python.

---

## 🔧 Setup

Install the Anthropic SDK:

```bash
pip install anthropic
```

---

## 🔐 Authentication

All requests to the Anthropic API must include the following headers:

- `x-api-key`: your Claude API key
- `anthropic-version`: set to `2023-06-01`
- `content-type`: `application/json`

You can set your API key as an environment variable:

```bash
export ANTHROPIC_API_KEY="your-anthropic-key"
```

---

## Generate ABC Notation with Claude Sonnet

Use this API call to generate ABC music notation from a prompt like `"Write a chord progression in F major in 4/4 time"`.

```python
import anthropic
import os

client = anthropic.Anthropic(api_key=os.environ["ANTHROPIC_API_KEY"])

response = client.messages.create(
    model="claude-3-sonnet-20250219",
    max_tokens=1024,
    temperature=0.5,
    system=(
        "You are a music composer. Write only ABC notation.
"
        "Use headers like T:, M:, L:, K: to structure the piece.
"
        "Use square brackets for chords like [C E G].
"
        "Do not include explanations or any extra text."
    ),
    messages=[
        {"role": "user", "content": "Write a chord progression in F major in 4/4"}
    ]
)

print(response.content[0].text)
```

---

## Parameters

| Field           | Type   | Description                              |
|----------------|--------|------------------------------------------|
| `model`        | string | The Claude model to use (`claude-3-sonnet-20250219`) |
| `max_tokens`   | int    | Max tokens in the output (e.g., 1024)     |
| `temperature`  | float  | Controls randomness (0 = strict, 1 = creative) |
| `system`       | string | Instructions to control the model's tone and output format |
| `messages`     | list   | List of message objects in the chat sequence |

---

## Sample Output

```abc
T:F Major Progression
M:4/4
L:1/4
K:F
[F A C] [G B D] [A C E] | [F A C] ||
```

---

## Output Requirements

Claude Sonnet responses must be parsable by Music21. Ensure the output includes:

| Header | Required | Description |
|--------|----------|-------------|
| `T:`   | ✅        | Title of the piece |
| `M:`   | ✅        | Meter (e.g., 3/4, 4/4) |
| `L:`   | ✅        | Default note length |
| `K:`   | ✅        | Key signature |
| `[ ]`  | ✅        | Chords in square brackets |
| `^` / `_` | ✅     | Sharps (`^`) and flats (`_`) for accidentals |

---

## ⚠️ Error Handling

Claude may occasionally return output that needs cleaning or formatting fixes.

See [`composer-functions.md`](./composer-functions.md) for functions that:
- Convert `#` / `b` into Music21-compatible symbols
- Validate ABC headers
- Convert notation to MIDI
