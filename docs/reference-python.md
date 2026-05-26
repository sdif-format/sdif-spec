# SDIF Python Reference Implementation Notes

This document provides informative notes regarding the Python reference implementation of SDIF (`sdif-py`). These notes are **non-normative**.

**Reference Python implementation version:** 1.1.0

## 1. Command Line Interface

The Python reference CLI is invoked via `sdif` or `python tools/sdif-cli.py`:

```bash
sdif parse examples/plan.sdif
sdif canon examples/plan.sdif
sdif hash examples/plan.sdif
sdif validate examples/plan.sdif --schema examples/schema.sdif
sdif tokens examples/plan.sdif
sdif to-json examples/plan.sdif
sdif from-json document.json
sdif ai examples/plan.sdif --alias kind=k --alias status=st
sdif from-ai examples/plan.sdif.ai
sdif inspect examples/plan.sdif --json
sdif fmt examples/plan.sdif --check
```

## 2. Token Estimation and tiktoken

The `sdif tokens` CLI checks byte size and estimates token usage. It leverages `tiktoken/cl100k_base` if installed, falling back to a 4-bytes-per-token heuristic.

The repository benchmark derives JSON compact, JSON pretty, YAML, XML, CSV Bundle, SDIF, SDIF AI, and optionally TOON from the same canonical JSON fixture source. `SDIF AI` is produced from the generated SDIF document with the `.sdif.ai` projection so the benchmark tracks the AI-context surface separately from canonical SDIF. For benchmark fairness, that projection chooses the smaller of a headerless context-window view and an explicit alias-header view; aliases are only useful when their decoding header pays for itself. The projection also uses compact table rows and `$` string-preserved columns when those choices reduce repeated syntax without changing semantics. It always reports an `Estimate` column using the same deterministic 4-UTF-8-bytes-per-token fallback as `sdif tokens`, and uses `tiktoken` as the primary ordering and ratio metric when that optional dependency is installed. `CSV Bundle` is intentionally named as a bundle because a full semantic SDIF document may contain metadata, nested values, relations, and rules that cannot fit in a single honest flat CSV table.

## 3. Reference AST Model

The parser constructs the following AST node structure:

- `Document(directives, statements)`
- `Directive(name, args)`
- `Field(key, value, quoted=False)`
- `ObjectBlock(key, statements)`
- `Table(name, columns, rows, quoted_columns=frozenset())`
- `Relation(subject, predicate, object, object_quoted=False)`
- `Rule(source, expression=None)`
- `Narrative(key, text)`
