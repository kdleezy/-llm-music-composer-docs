
# Composer Functions Reference

This document outlines the core Python functions used in the LLM Music Composer app to clean ABC music notation and convert it into MIDI files using Music21.

---

## 🧹 `convert_sharps_flats(progression: str) -> str`

Converts LLM-generated accidentals (`#`, `b`) into Music21-compatible ABC symbols (`^`, `_`).

This function walks through the ABC string character-by-character, detects accidentals, and rewrites them inline for compatibility with music parsing libraries.

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
                i += 1  # Skip next character
            else:
                converted_progression += char
        else:
            converted_progression += char
        i += 1
    return converted_progression
```

---

## 🎼 `parse_abc_to_stream(abc_notation: str) -> stream | None`

Parses ABC notation into a Music21 stream object. This stream can be converted into a MIDI file.

Includes basic error handling to catch and log parsing errors.

```python
def parse_abc_to_stream(abc_notation):
    """
    Parses ABC notation into a music21 stream.
    """
    try:
        return converter.parseData(abc_notation, format='abc')
    except Exception as e:
        print(f"Failed to parse ABC notation into stream: {e}")
        return None
```

---

## 🎛️ `convert_stream_to_midi(s: stream, file_name: str = "output.mid") -> None`

Takes a Music21 stream and saves it as a `.mid` file.

```python
def convert_stream_to_midi(s, file_name="output.mid"):
    """
    Converts the music21 stream to a MIDI file.
    """
    s.write('midi', fp=file_name)
```

---

## 🧪 Example Output

Here is an example of a correctly formatted ABC string after LLM conversion and Music21 processing:

```abc
T:F Major Chord Progression
M:4/4
L:1/4
K:F
[F,A,C] [G,B,D] [A,C,E] [F,A,C] [_B,D,F] [C,E,G] [D,F,A] [G,B,D] | [C,E,G] [F,A,C] [_B,D,F] [F,A,C] ||
```

This ABC string can be passed through the functions above to produce a MIDI file that composers can drag into their DAW.

---

## 📌 Notes

- Music21 requires chords to be wrapped in brackets and uses `^` and `_` for accidentals.
- Always validate output after LLM generation before conversion.
- MIDI creation is fast but depends on successful ABC parsing.

