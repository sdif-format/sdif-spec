# Specification Changelog

## [1.1.0] - 2026-05-24

### Changed
- **Optional Schema Validation**: Explicitly documented that schema validation is optional and syntactic validity check is the default baseline.
- **AI-Profile Alias Collisions**: Clarified rules for dropping alias targets that collide with literal keys in `.sdif.ai` profiles to prevent spurious expansions.
- **Preserved List Literal Types**: Refined canonicalization rules to preserve list type structures (bare lists `[a,b,c]` and quoted lists `["a","b"]`) in round-trips.

## [1.0.0] - 2026-05-22

### Stable v1.0 specification
- Freezes stable syntax contracts for `@sdif 1.0` and `@sdif.ai 1.0`.
- Documents stable canonical-syntax-v1 formatting rules, encoding constraints (valid UTF-8), and resource limit mitigations.
- Defines lossless/lossy projection behavior for derived `.sdif.ai` documents.
