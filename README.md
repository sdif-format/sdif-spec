# SDIF Specification (`sdif-spec`)

This repository contains the official, stable format specification and conformance test suite for the **Semantic Data Interchange Format (SDIF)**.

It serves as the **Single Point of Truth (SSOT)** for all parser and tool implementations.

## Repository Structure

- `docs/` — Official format specifications:
  - `spec.md` — Stable core format specification (stable `@sdif 1.0` and `@sdif.ai 1.0` contract).
  - `canonicalization.md` — Canonicalization and semantic stability rules.
  - `semantic-quality.md` — Semantic density, quality, and profile guidelines.
  - `ai-speed-profile.md` — Context window packing details.
  - `comparison.md` — Structural comparisons to JSON, YAML, XML, and TOON.
- `conformance/` — Portable conformance fixture suite:
  - `manifest.sdif` — Reference manifest listing all valid and invalid cases.
  - `valid/` — Conforming SDIF source documents and their corresponding canonical forms.
  - `invalid/` — Non-conforming source documents designed to verify error detection.

## Conformance Verification

All conforming parser and tool implementations MUST stay aligned with the fixtures in `conformance/`. 
Any changes to SDIF syntax, types, or canonical rules must be reflected in the spec and conformance suite before grammar or library extensions are implemented.

## License

This specification is licensed under the MIT License. See [LICENSE](LICENSE) for details.
