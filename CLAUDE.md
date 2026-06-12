# Graph Avatar Studio

A deterministic visual identity system that encodes short text (names, identifiers) as unique geometric avatars. Inspired by GitHub's identicons but grounded in graph theory and sacred geometry — specifically a hexagonal Flower-of-Life petal layout with a 7-node graph overlaid.

## What it does

Given any name string, produces a unique circular avatar by hashing the name into a set of geometric parameters. The same name always produces the same avatar. The visual has two layers that work together:

- **Geometric substrate** — hexagonal petal circles, vesica lens highlights, and concentric rings derived from the Flower of Life
- **Graph overlay** — 7 vertices (1 center + 6 outer) connected by a hash-encoded edge set, drawn as luminous lines

The aesthetic is intentionally dark-background, spectral-color, "astrological cloud nebula."

---

## File

`graph-avatar-studio.html` — single self-contained HTML file, no dependencies.

---

## Encoding

All visual parameters are derived deterministically from four FNV-1a 32-bit hashes of the input name:

```
h0 = hash32(name)
h1 = hash32(name + "1")
h2 = hash32(name + "2")
h3 = hash32(name + "3")
```

### Bit map

| Layer | Bits | Source | Variants |
|---|---|---|---|
| Petal fill bitmask | 6 | h0 bits 0–5 | 64 |
| Outer ring bitmask | 6 | h0 bits 6–11 | 64 |
| Center ring | 1 | h0 bit 12 | 2 |
| Edge bitmask | 18 | h1 bits 0–17 | 262,144 |
| Vesica bitmask | 6 | h1 bits 18–23 | 64 |
| Hue base | ~9 | h2 % 360 | 360 |
| Hue accent wobble | ~6 | h3 % 70 | 70 |

**Total meaningful bits used: ~52 of 128 available.**  
h0 bits 13–31, h1 bits 24–31, and most of h2/h3 are currently unused — available for future encoding.

### Geometry

- **7 graph vertices**: v0 = center, v1–v6 = outer ring at 60° intervals
- **18 valid edges**: all C(7,2)=21 pairs minus the 3 diameter pairs (outer-to-outer where |i−j|=3), which are excluded because they pass through center and are visually ambiguous with hub edges
- **Edge classification** (visual weight/color):
  - Hub: center ↔ outer (6 possible, warmer, thicker)
  - Ring: adjacent outer vertices (6 possible, cooler)
  - Chord: non-adjacent outer, non-diameter (6 possible, lightest)
- **6 petal circles**: centered at `pr` distance from center at 60° intervals, radius `pr`; `pr = size * 0.23` so max extent (0.46) fits inside CSS 50% border-radius clip
- **6 vesica positions**: interstitial, 30° offset from vertex ring, at `pr * 0.88`
- **7 rings**: 6 outer (at vertex positions) + 1 center; all same radius `size * 0.057 * ringSize`
- **Center glow**: filled circle at `pr * 0.3`, fill-only (no stroke) so it doesn't compete visually with the center ring

### Color

```js
hueBase   = h2 % 360
hueAccent = (hueBase + 130 + (h3 % 70)) % 360
```

Accent is offset ~130–200° from base (split-complementary). Chord edge hue interpolates halfway between base and accent. Sat/lit are hash-nudged within a narrow range for consistency.

---

## UI controls

All sliders are display overrides — they don't affect the encoding, only the rendering. The encoding (and therefore which avatar a name maps to) is fixed by the hash alone.

| Control | Effect |
|---|---|
| Node size | Scales all 7 vertex dots (center + outer) |
| Ring size | Scales all 7 rings + vesica highlights uniformly |
| Edge width | Multiplies base weight of all edges (hub/ring/chord hierarchy preserved) |
| Hue shift | Rotates entire palette, preserving triadic relationships |
| Saturation | Overrides hash-derived saturation |
| Lightness | Overrides hash-derived lightness |
| Inactive rings toggle | Dim (show inactive rings at 20% opacity) vs Absent (hide inactive rings entirely) |

---

## Known issues / next ideas

- [ ] Fix: center glow stroke removed — now fill-only ✓
- [ ] Export as SVG/PNG button
- [ ] Degree-weighted vertex dot size (high-degree nodes larger)
- [ ] Canonical rotation (highest-degree outer vertex always at top)
- [ ] Curved chord edges to reduce visual crossing clutter
- [ ] Use remaining unused bits in h0/h1 for additional encoding
- [ ] 13-node variant: add 6 vesica positions as graph vertices (78 possible edges)
