# ARTSEY.IO for Karabiner-Elements

A [Karabiner-Elements](https://karabiner-elements.pqrs.org/) implementation of the [ARTSEY.IO](https://artsey.io/) one-handed chording keyboard layout (v0.8.1).

Type the full alphabet, numbers, symbols, and modifiers using only **8 keys**: `A R T S E Y I O`.

```
┌───┬───┬───┬───┐
│ A │ R │ T │ S │  ← top row
├───┼───┼───┼───┤
│ E │ Y │ I │ O │  ← bottom row
└───┴───┴───┴───┘
```

## Files

| File | Description |
|---|---|
| `artsey.json` | Base layer — all 26 letters, common punctuation, and global utilities/modifiers |
| `artsey_layers.json` | Layer system — Numbers (hold S), Symbols (hold E), Parentheticals (hold A) |

## Installation

```bash
# Copy both files into Karabiner's complex modifications directory
cp artsey.json artsey_layers.json \
   ~/.config/karabiner/assets/complex_modifications/
```

1. Open **Karabiner-Elements → Settings → Complex Modifications → Add predefined rule**
2. Enable **"ARTSEY layers"** first
3. Enable **"ARTSEY.IO v0.8.1"** second

> **Order matters.** The layers rule must appear above the base rule in your profile so layer content is evaluated before base chords.

### Simultaneous threshold

The default 50ms threshold is tight for 3- and 4-key chords. Raise it to 80–100ms:

**Settings → Parameters → `basic.simultaneous_threshold_milliseconds` → `100`**

### Device scoping (optional)

To restrict these rules to your 8-key board only, add a `device_if` condition using your board's vendor and product IDs (visible in Karabiner EventViewer):

```json
"conditions": [{
  "type": "device_if",
  "identifiers": [{ "vendor_id": 1234, "product_id": 5678 }]
}]
```

---

## Base Layer — `artsey.json`

### Letters

Single keys pass through as themselves. Chords produce the remaining 18 letters:

| Chord | Letter | | Chord | Letter |
|:------|:------:|-|:------|:------:|
| A + R | F | | E + O | B |
| R + T | G | | E + Y | C |
| T + S | J | | E + I | H |
| R + S | V | | Y + I | U |
| A + S | W | | Y + O | K |
| A + R + T | D | | I + O | N |
| A + T + S | Q | | E + Y + I | L |
| R + T + S | X | | Y + I + O | M |
| A + R + T + S | Z | | E + I + O | P |

### Punctuation

| Chord | Output |
|:------|:------:|
| A + Y | `.` |
| A + I | `,` |
| A + O | `/` |
| T + I | `!` |
| A + Y + I | `'` |

### Utilities (global — work on all layers)

| Chord | Output |
|:------|:-------|
| E + Y + I + O | Space |
| A + E | Enter |
| R + E | Backspace |
| R + I | Forward Delete |
| A + R + O | Escape |
| A + R + T + O | Tab |

### Modifiers (global — hold chord to hold modifier)

| Chord | Modifier |
|:------|:---------|
| R + T + S + E | Shift |
| S + E | Control |
| S + Y | Command |
| S + I | Option |
| R + Y | Shift Lock (Caps Lock toggle) |
| A + Y + I + O | Caps Lock |

---

## Layers — `artsey_layers.json`

Layers activate by **holding** a key for 200ms. Tapping the key normally still emits the base letter. Releasing the held key returns to the base layer.

### Numbers (hold S)

```
┌───┬───┬───┬───┐
│ 1 │ 2 │ 3 │   │
├───┼───┼───┼───┤
│ 4 │ 5 │ 6 │   │
└───┴───┴───┴───┘

Chords:  A+R → 7   R+T → 8   E+Y → 9   Y+I → 0
```

### Symbols (hold E)

```
┌───┬───┬───┬───┐
│ ! │ \ │ ; │ ` │
├───┼───┼───┼───┤
│   │ ? │ - │ = │
└───┴───┴───┴───┘
```

### Parentheticals (hold A)

```
┌───┬───┬───┬───┐
│   │ ( │ ) │ { │
├───┼───┼───┼───┤
│   │ [ │ ] │ } │
└───┴───┴───┴───┘
```

---

## Not yet implemented

The following ARTSEY layers require further work (mouse keys, media keys) and are not included:

- **Navigation** (R + E + I hold) — arrow keys, Home/End, Page Up/Down
- **Mouse** (A + T + Y hold) — cursor movement, buttons, scroll wheel
- **Custom** (hold O) — media controls, Insert, Print Screen
- **Bluetooth** (A + S + E + O hold) — not applicable to macOS

---

## How it works

Karabiner's [`simultaneous`](https://karabiner-elements.pqrs.org/docs/json/complex-modifications-manipulator-definition/from/simultaneous/) detection holds key events briefly and checks whether multiple keys arrive within a threshold window. If a chord matches, the combined output fires. If not, the individual key passes through normally.

Key design decisions:

- **Rule ordering**: manipulators are sorted by chord size (4-key → 3-key → 2-key) so the most specific chord always wins
- **Modifier transparency**: every rule includes `"modifiers": {"optional": ["any"]}` so chords work regardless of Caps Lock, Shift, or any other active modifier
- **Layer isolation**: base-layer chords carry `artsey_layer == 0` conditions to prevent them firing when a layer is active; global chords (Space, Enter, modifiers, etc.) are unconditional
- **Hold vs tap**: layer toggle keys use `to_if_alone` / `to_if_held_down` with a 200ms threshold to distinguish taps from holds

## Reference

Based on [ARTSEY.IO v0.8.1](https://github.com/artseyio/artsey) by [@CalcProgrammer1](https://github.com/CalcProgrammer1).

ARTSEY is licensed under [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/).


---

## Tested hardware

This configuration has been tested with a [8-key mechanical macropad from AliExpress](https://www.aliexpress.com/item/1005007096723667.html) — a cheap, compact USB HID device with keys arranged in a single vertical column mapped to `A R T S E Y I O` from top to bottom.

Any 8-key input device that sends standard USB HID keycodes will work. Remap the physical keys to send `A R T S E Y I O` using the macropad's onboard firmware or VIA/QMK configuration, then let Karabiner handle the chording logic.
