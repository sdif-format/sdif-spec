<p align="center">
  <img src="https://raw.githubusercontent.com/sdif-format/.github/main/profile/assets/sdif-logo-t.png" alt="SDIF Specification" width="320">
</p>

<p align="center">
  <strong>SDIF Specification & Conformance Suite</strong>
</p>

<p align="center">
  The Single Point of Truth (SPOT) containing the official, stable format specification<br>
  and conformance test suite for the Semantic Data Interchange Format (SDIF).
</p>

<p align="center">
  <a href="#repository-structure">Repository Structure</a>
  ·
  <a href="#conformance-verification">Conformance Verification</a>
  ·
  <a href="#ecosystem">Ecosystem</a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/spec-v1.0.0%20stable-16a34a?style=flat-square" alt="Spec v1.0.0 stable">
  <img src="https://img.shields.io/badge/focus-single%20source%20of%20truth-0f766e?style=flat-square" alt="Single Source of Truth">
  <img src="https://img.shields.io/badge/license-MIT-374151?style=flat-square" alt="MIT">
</p>

<br>

<div align="center">

<table>
  <tr>
    <td align="center" width="25%">
      <strong>Compact</strong>
      <br><br>
      Less repeated structure.<br>
      Fewer wasted tokens.
    </td>
    <td align="center" width="25%">
      <strong>Semantic</strong>
      <br><br>
      Tables, relations,<br>
      metadata and intent.
    </td>
    <td align="center" width="25%">
      <strong>Canonical</strong>
      <br><br>
      Stable output for hashing,<br>
      signing and comparison.
    </td>
    <td align="center" width="25%">
      <strong>Auditable</strong>
      <br><br>
      Designed to be read,<br>
      reviewed and trusted.
    </td>
  </tr>
</table>

</div>

<br>

---

## Repository Structure

- `docs/` — Official format specifications and analysis:
  - [`spec.md`](docs/spec.md) — Stable core format specification (stable `@sdif 1.0` and `@sdif.ai 1.0` contract).
  - [`reference-python.md`](docs/reference-python.md) — Informative notes on the Python reference implementation (non-normative).
  - [`reference-rust.md`](docs/reference-rust.md) — Informative notes on the Rust parser crate and LSP server (non-normative).
  - [`canonicalization.md`](docs/canonicalization.md) — Canonicalization and semantic stability rules.
  - [`semantic-quality.md`](docs/semantic-quality.md) — Semantic density, quality, and profile guidelines.
  - [`ai-speed-profile.md`](docs/ai-speed-profile.md) — Context window packing details.
  - [`comparison.md`](docs/comparison.md) — Structural comparisons to JSON, YAML, XML, and TOON.
- `conformance/` — Portable conformance fixture suite:
  - [`manifest.sdif`](conformance/manifest.sdif) — Reference manifest listing all valid and invalid cases.
  - [`valid/`](conformance/valid/) — Conforming SDIF source documents and their corresponding canonical forms.
  - [`invalid/`](conformance/invalid/) — Non-conforming source documents designed to verify error detection.

<br>

---

## Conformance Verification

All conforming parser and tool implementations MUST stay aligned with the fixtures in `conformance/`.
Any changes to SDIF syntax, types, or canonical rules must be reflected in the spec and conformance suite before grammar or library extensions are implemented.

<br>

---

## Ecosystem

This GitHub organization hosts the official SDIF ecosystem: the core format, reference tooling, benchmarks, examples, libraries, and editor extensions.

<div align="center">

<table>
  <tr>
    <td width="33%" valign="top">
      <p><sub>PYTHON CLIENT & CLI</sub></p>
      <h3>sdif-py</h3>
      <p>
        Specification, parser, canonicalizer, and CLI.<br>
        The normative reference implementation.
      </p>
      <p><a href="https://github.com/sdif-format/sdif-py"><strong>Explore sdif-py →</strong></a></p>
    </td>
    <td width="33%" valign="top">
      <p><sub>SPECIFICATION (SSOT)</sub></p>
      <h3>sdif-spec</h3>
      <p>
        Official format specification, canonicalization rules,<br>
        and portable conformance test suite.
      </p>
      <p><a href="https://github.com/sdif-format/sdif-spec"><strong>View specification →</strong></a></p>
    </td>
    <td width="33%" valign="top">
      <p><sub>BENCHMARKS</sub></p>
      <h3>sdif-benchmarks</h3>
      <p>
        Reproducible benchmark datasets and reports comparing SDIF with JSON, YAML, XML, and CSV.
      </p>
      <p><a href="https://github.com/sdif-format/sdif-benchmarks"><strong>View benchmarks →</strong></a></p>
    </td>
  </tr>
  <tr>
    <td width="33%" valign="top">
      <p><sub>RUST IMPLEMENTATION</sub></p>
      <h3>sdif-rs</h3>
      <p>
        Pure Rust parser implementation with a span-annotated AST designed for editor tooling.
      </p>
      <p><a href="https://github.com/sdif-format/sdif-rs"><strong>Explore sdif-rs →</strong></a></p>
    </td>
    <td width="33%" valign="top">
      <p><sub>LANGUAGE SERVER (LSP)</sub></p>
      <h3>sdif-lsp</h3>
      <p>
        LSP language server binary (tower-lsp) providing real-time diagnostics and IDE features.
      </p>
      <p><a href="https://github.com/sdif-format/sdif-lsp"><strong>View sdif-lsp →</strong></a></p>
    </td>
    <td width="33%" valign="top">
      <p><sub>EDITOR INTEGRATION</sub></p>
      <h3>vscode-sdif</h3>
      <p>
        VS Code extension client providing syntax highlighting, diagnostics, and LSP configuration.
      </p>
      <p><a href="https://github.com/sdif-format/vscode-sdif"><strong>Open extension →</strong></a></p>
    </td>
  </tr>
  <tr>
    <td width="33%" valign="top">
      <p><sub>GRAMMAR FOUNDATION</sub></p>
      <h3>tree-sitter-sdif</h3>
      <p>
        Tree-sitter grammar foundation for syntax highlighting and incremental parsing.
      </p>
      <p><a href="https://github.com/sdif-format/tree-sitter-sdif"><strong>Open grammar →</strong></a></p>
    </td>
    <td width="33%" valign="top">
      <p><sub>DOCUMENTATION</sub></p>
      <h3>sdif-format.github.io</h3>
      <p>
        Official documentation website containing specification guides, tutorials, and examples.
      </p>
      <p><a href="https://github.com/sdif-format/sdif-format.github.io"><strong>Read docs →</strong></a></p>
    </td>
    <td width="33%" valign="top">
      <p><sub>ORGANIZATION META</sub></p>
      <h3>.github</h3>
      <p>
        Organization profile, assets, and shared community configuration files.
      </p>
      <p><a href="https://github.com/sdif-format/.github"><strong>View profile →</strong></a></p>
    </td>
  </tr>
</table>

</div>

<br>

<details>
  <summary><strong>Repository map</strong></summary>

<br>

| Repository | Purpose |
| --------------------------------------------------------------------- | ---------------------------------------------------------------- |
| [`sdif-py`](https://github.com/sdif-format/sdif-py)                   | Core Python parser, validator, canonicalizer, and CLI |
| [`sdif-spec`](https://github.com/sdif-format/sdif-spec)               | Official format specification and conformance test suite (SSOT) |
| [`sdif-benchmarks`](https://github.com/sdif-format/sdif-benchmarks)   | Benchmark datasets, reports, and comparison tooling |
| [`sdif-rs`](https://github.com/sdif-format/sdif-rs)                   | Rust parser crate with span-annotated AST |
| [`sdif-lsp`](https://github.com/sdif-format/sdif-lsp)                 | LSP language server binary |
| [`tree-sitter-sdif`](https://github.com/sdif-format/tree-sitter-sdif) | Tree-sitter grammar foundation for syntax highlighting |
| [`vscode-sdif`](https://github.com/sdif-format/vscode-sdif)           | VS Code extension client for SDIF |
| [`sdif-format.github.io`](https://github.com/sdif-format/sdif-format.github.io) | Public documentation website (Docusaurus) |
| [`.github`](https://github.com/sdif-format/.github)                   | Organization profile, assets, and shared GitHub community files |

</details>

<br>

## License

This specification is licensed under the MIT License. See [LICENSE](LICENSE) for details.
