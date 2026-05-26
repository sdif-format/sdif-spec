# SDIF Rust Reference Implementation Notes

This document provides informative notes regarding the Rust reference implementation of SDIF (`sdif-rs` and `sdif-lsp`). These notes are **non-normative**.

## 1. Crate Modules and Layout

The `sdif-rs` crate is structured around a zero-copy parser providing exact span coordinates. The key modules under `sdif/src/` are:

- **`span.rs`**: Location tracking via `Span { start_line, start_col, end_line, end_col }` (1-based, end exclusive).
- **`ast.rs`**: AST nodes (Field, Directive, Table, Relation, Narrative, Rule, ObjectBlock, Document).
- **`error.rs`**: `ParseError { code, message, span, hint }` and `PolicyError`.
- **`policy.rs`**: `Policy` containing document boundaries (defaults to 1 MB limit, mirroring Python).
- **`lexer.rs`**: `Token`, `TokenKind`, and `lex_lines()`.
- **`parser.rs`**: `parse_text()` and `parse_text_with_policy()`.

## 2. Public API

The crate exposes the following core public interface:

- `parse_text(text: &str) -> Result<Document, ParseError>`: Parses a string slice into a span-annotated AST.
- `parse_text_with_policy(text: &str, policy: &Policy) -> Result<Document, ParseError>`: Parses using custom boundaries.
- `Policy`: Bounded resource configuration.
- `ParseError`: Rich syntax diagnostics including hints.
- `Span`: Coordinates for editor diagnostic overlays.
- `Document`: AST root.

## 3. LSP Language Server (`sdif-lsp`)

The sibling LSP binary (`sdif-lsp`) leverages `sdif-rs` for editor support:

- **Transport**: Standard stdin/stdout.
- **Trigger**: Re-parses incrementally on every `textDocument/didChange`.
- **Capabilities**:
  - Live diagnostics mapping `ParseError` and `PolicyError` to LSP Diagnostics.
  - Semantic tokens (full syntax colorization).
  - Hover info and autocomplete suggestions.

## 4. Development and Conformance

Build and verify the crate using standard cargo commands:

```bash
cargo build                  # Compile library
cargo test                   # Run unit tests and conformance suite
cargo clippy -- -D warnings  # Lint validation
cargo fmt --check            # Code style check
```

The conformance tests dynamically locate and parse the shared test fixtures inside the `sdif-spec/conformance/` directory to ensure exact alignment with the normative Python parser.
