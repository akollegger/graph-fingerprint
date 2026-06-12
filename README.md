# Graph Fingerprint

A deterministic visual identity system that encodes names as unique geometric avatars.

Given any name, produces a circular avatar by hashing it into a set of geometric parameters. The same name always produces the same avatar.

**[Live demo →](https://akollegger.github.io/graph-fingerprint/)**

## How it works

Two visual layers combine:

- **Geometric substrate** — hexagonal petal circles, vesica lens highlights, and concentric rings derived from the Flower of Life
- **Graph overlay** — 7 vertices (1 center + 6 outer) connected by a hash-encoded edge set, drawn as luminous lines
- **Clock cluster** — 6 outer elements that encode name length and establish orientation

All parameters are derived from FNV-1a hashes of the input name. The encoding is fully invertible — any avatar can be decoded back to its original name.

## Usage

Open `index.html` directly in a browser — no build step, no dependencies.

The UI controls (node size, ring size, edge width, etc.) are display overrides and do not affect the encoding. Export to SVG at 1×, 2×, 4×, or 8× resolution.

## Encoding capacity

- Up to 20 characters from the charset `A-Za-z0-9-_`
- 124 structural bits, XOR-whitened for visual diversity
- ~52 bits actively used for visual parameters; remainder encodes character data
