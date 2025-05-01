# Composer Functions – Cleaning and Converting ABC Notation

This module includes Python functions to clean, validate, and convert Claude Sonnet's ABC music notation into MIDI files using the Music21 library.

Use these after calling the Claude API to:

- Fix formatting issues (e.g., sharps/flats)
- Validate headers
- Export to MIDI

---

## 🔧 Requirements

Install Music21:

```bash
pip install music21
```

---

## `convert_sharps_flats(progression: str) -> str`

Converts characters like `#`, `b`, or `♭` from Claude output into Music21-compatible symbols (`^` for sharps, `_` for flats).

```python
def convert_sharps_flats(progression):
    note_letters = 'ABCDEFG'
    converted_progression = ""
    i = 0
    while i < len(progression):
        char = progression[i]
        if char in note_letters:
            if i+1 < len(progression) and progression[i+1] in ['#', 'b', '♭']:
                converted_char = '^' if progression[i+1] == '#' else '_'
                converted_progression += converted_char + char
                i += 1  # Skip accidental
            else:
                converted_progression += char
        else:
            converted_progression += char
        i += 1
    return converted_progression
```

---

## `validate_abc_headers(text: str) -> bool`

Checks if the ABC string contains all required headers.

```python
def validate_abc_headers(text):
    required = ["T:", "M:", "L:", "K:"]
    return all(header in text for header in required)
```

---

## `parse_abc_to_stream(abc_notation: str) -> stream | None`

Parses ABC notation into a Music21 stream. Returns `None` if parsing fails.

```python
from music21 import converter

def parse_abc_to_stream(abc_notation):
    try:
        return converter.parseData(abc_notation, format='abc')
    except Exception as e:
        print(f"Parse error: {e}")
        return None
```

---

## `convert_stream_to_midi(s: stream, file_name: str = "output.mid") -> None`

Converts a Music21 stream into a `.mid` file that can be dragged into any DAW or notation app.

```python
def convert_stream_to_midi(s, file_name="output.mid"):
    s.write('midi', fp=file_name)
```

---

## 🎵 Example Flow

After calling Claude Sonnet and storing the result in `raw_output`, use this code to process it:

```python
cleaned = convert_sharps_flats(raw_output)

if validate_abc_headers(cleaned):
    stream = parse_abc_to_stream(cleaned)
    if stream:
        convert_stream_to_midi(stream, "progression.mid")
```

This pipeline assumes you’ve already made the Claude API call and are now ready to clean and convert the music output.

---

## 📎 Related Docs

- [`api-reference.md`](./api-reference.md): for Claude API usage and prompt design.
