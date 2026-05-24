<!-- markdownlint-disable MD010 MD013 MD060 -->
<!--
  This specification intentionally contains literal HTAB characters in SDIF
  table examples and long grammar/contract lines. Those are semantic examples,
  not prose formatting defects.
-->

# SDIF — Semantic Data Interchange Format

**Format version:** 1.0
**Specification document version:** 1.1.0
**Python package version:** 1.1.0
Status: Stable Core Specification
Date: 2026-05-24
License: MIT

## Table of Contents

- [1. Abstract](#1-abstract)
- [2. Status of This Document](#2-status-of-this-document)
- [3. Terminology and Conventions](#3-terminology-and-conventions)
  - [3.1 Requirement Keywords](#31-requirement-keywords)
- [4. Overview](#4-overview)
- [5. Goals and Non-Goals](#5-goals-and-non-goals)
  - [5.1 Design Goals](#51-design-goals)
  - [5.2 Non-Goals](#52-non-goals)
- [6. Data Model](#6-data-model)
- [7. Document Structure](#7-document-structure)
- [8. Lexical Rules](#8-lexical-rules)
- [9. Directives](#9-directives)
- [10. Scalar Fields](#10-scalar-fields)
- [11. Objects](#11-objects)
- [12. Lists](#12-lists)
- [13. Tables](#13-tables)
- [14. Relations](#14-relations)
- [15. Declarative Rules](#15-declarative-rules)
- [16. Narrative Blocks](#16-narrative-blocks)
- [17. Profiles](#17-profiles)
  - [17.1 SDIF Source](#171-sdif-source)
  - [17.2 SDIF Canonical](#172-sdif-canonical)
  - [17.3 SDIF AI View](#173-sdif-ai-view)
- [18. Parsing Requirements](#18-parsing-requirements)
- [19. Validation Requirements](#19-validation-requirements)
- [20. Canonicalization](#20-canonicalization)
- [21. JSON Mapping](#21-json-mapping)
- [22. Diagnostics](#22-diagnostics)
- [23. Security Considerations](#23-security-considerations)
- [24. Versioning and Extensibility](#24-versioning-and-extensibility)
  - [24.1 Format Versioning](#241-format-versioning)
  - [24.2 Media Type and File Extensions](#242-media-type-and-file-extensions)
  - [24.3 Internationalization](#243-internationalization)
- [25. Conformance](#25-conformance)
  - [25.1 Parser Conformance](#251-parser-conformance)
  - [25.2 Serializer Conformance](#252-serializer-conformance)
  - [25.3 Canonicalizer Conformance](#253-canonicalizer-conformance)
  - [25.4 Validator Conformance](#254-validator-conformance)
  - [25.5 AI Projection Conformance](#255-ai-projection-conformance)
- [Appendix A. Grammar](#appendix-a-grammar)
- [Appendix B. Examples](#appendix-b-examples)
- [Appendix C. Python Reference Implementation Notes](#appendix-c-python-reference-implementation-notes)
- [Appendix D. Reserved Extension Surfaces](#appendix-d-reserved-extension-surfaces)
- [Appendix E. Changelog](#appendix-e-changelog)

---

## 1. Abstract

This document defines the technical specification for SDIF, a compact semantic data interchange format designed for AI agents and deterministic machine workflows, with human-auditable source files.

---

## 2. Status of This Document

This document records the stable core specification of SDIF. `@sdif 1.0` identifies the stable core syntax and semantic contract. The package version may advance independently from the document format version.

---

## 3. Terminology and Conventions

### 3.1 Requirement Keywords

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 [RFC2119] [RFC8174] when, and only when, they appear in all capitals.

---

## 4. Overview

SDIF combines the strict structural model of JSON, the readability of YAML and TOML, the tabular compactness of CSV, the graph-like expressiveness of semantic triples, the bounded narrative capability of Markdown-style text blocks, and the determinism required for canonicalization, hashing, signing, and reproducible validation.

The format is structured around the core principle of maximizing semantic density per token without sacrificing validation safety or parsing determinism.

Core v1 behavior includes parsing, the reference AST shape, schema-driven validation, canonical-syntax-v1, safe default policies, local includes behind explicit policy, and `.sdif.ai` reversibility to canonical source.

---

## 5. Goals and Non-Goals

### 5.1 Design Goals

SDIF aims to achieve the following:

1. Maximize semantic density per token for efficient processing by machine workflows and AI agents.
2. Provide deterministic parsing and canonicalization.
3. Enable schema-driven validation and stable hashing.
4. Support diverse data types, including scalars, objects, lists, tables, relations, declarative rules, and narrative blocks.
5. Support human-auditable source files alongside machine-normalized canonical files.

### 5.2 Non-Goals

SDIF does not aim to:

1. Function as a general-purpose programming language or execute arbitrary code.
2. Replace SQL, RDF, OWL, or general-purpose formats like JSON for public APIs where JSON is the established contract.
3. Allow highly flexible syntax at the cost of ambiguity.

---

## 6. Data Model

An SDIF document is an ordered sequence of statements. Every conforming document model is composed of:

1. Directives
2. Scalar fields
3. Objects
4. Lists
5. Tables
6. Relations
7. Declarative rules
8. Narrative blocks
9. Comments

---

## 7. Document Structure

A document MUST consist of a sequence of statements ending with an optional sequence of blank lines and comments. Statement types MUST NOT be mixed in ways that violate lexical indentation rules.

---

## 8. Lexical Rules

All SDIF documents MUST be UTF-8 encoded. Line endings MUST be normalized to LF (`\n`) for canonical output, though parsers MAY tolerate CRLF (`\r\n`).

Indentation MUST use spaces. Tabs MUST NOT be used for indentation in strict mode. The horizontal tab character (`HTAB`, U+0009) is strictly reserved as the column separator inside table rows.

Comments start with the hash character (`#`) outside strings and multiline blocks and continue to the end of the line.

---

## 9. Directives

Directives start with `@` and provide format metadata. The following directives are reserved in SDIF:
- `@sdif`: Declares the SDIF format version (e.g., `@sdif 1.0`). `@sdif 1.0` identifies the stable core syntax and semantic contract.
- `@sdif.ai`: Declares the token-optimized SDIF AI projection.
- `@profile`: Declares the document profile.
- `@vocab`: Declares a vocabulary.
- `@base`: Declares a base URI or semantic namespace.
- `@namespace`: Declares a namespace prefix. The v1 namespace form is `@namespace prefix iri`. Complex namespace behavior is a versioned extension.
- `@include`: Includes another local document or vocabulary. `@include` is disabled by default.

Remote includes and remote schemas are reserved extension surfaces and are rejected by the current reference implementation.

---

## 10. Scalar Fields

A scalar field consists of a key identifier and a value separated by horizontal space. A scalar value outside tables MUST be quoted if it contains spaces, commas intended as text, parentheses, brackets, quotes, comment markers, or non-identifier characters.

Quoted scalar fields outside tables MUST satisfy:

1. **Single-line closure**: The closing double quote `"` MUST appear on the same logical line as the opening double quote `"`.
2. **No trailing content**: No non-whitespace, non-comment characters may follow the closing double quote on the same line.

---

## 11. Objects

Objects represent nested associative maps and use two-space indentation.

```sdif
owner:
  id team.platform
  role maintainer
```

---

## 12. Lists

Lists can be represented as inline bracketed sequences:

```sdif
tags [registry,validation,release]
```

Alternatively, block lists MAY be represented as repeated `-` scalar fields inside an object block.

---

## 13. Tables

Tables represent uniform arrays of objects. A table block MUST start with a table header naming the table and declaring its columns in square brackets, followed by table rows. Table columns are separated by the literal horizontal tab character (`HTAB`, U+0009), not by spaces or commas.

```sdif
milestones[id,status,gate]:
  R1	done	verify
  R2	pending	verify
```

For the v1.0 stabilization track, strict mode prohibits inline comments inside table rows.

A column name ending in `$` is a `.sdif.ai` string-preserved column marker. Consumers MUST strip the suffix from the semantic column name and parse all cells in that column as strings. The `$` suffix is a decoding hint only and is not part of the semantic column name.

---

## 14. Relations

Relations describe semantic relationships expressed as subject-predicate-object triples:

```sdif
rel:
  release.v2 depends_on release.v1
```

A predicate MUST be allowed by the declared schema or vocabulary. In v1 source, a relation has exactly three parts.

---

## 15. Declarative Rules

Rules represent validation constraints using parenthesized expressions. They are not executable code.

```sdif
rules:
  (deny missing(evidence))
  (warn eq(authority,Unknown))
```

A rule row MUST correspond to a logical `RuleExpression(action, function, args)` node.

---

## 16. Narrative Blocks

Narrative blocks capture multi-line unstructured text using triple-quote delimiters `"""`.

```sdif
intent """
Define the validation requirements.
"""
```

Narrative blocks nested within object blocks MUST close with the triple-quote delimiter aligned at the exact indentation level where it opened. Body lines within the narrative block are normalized by removing the indentation prefix corresponding to the opening line's indentation level.

---

## 17. Profiles

### 17.1 SDIF Source

The Source profile (`.sdif`) is optimized for human and agent authoring. It MAY contain comments, blank lines, narrative blocks, and flexible ordering.

### 17.2 SDIF Canonical

The Canonical profile (`.sdif.canon`) is optimized for machine workflows. It MUST NOT include comments, blank lines, or stylistic variants, and MUST be deterministically ordered and formatted for stable hashing.

### 17.3 SDIF AI View

The AI View profile (`.sdif.ai`) is optimized for compact AI model context windows. It MAY employ compact table rows without indentation and alias headers:

```sdif
@sdif.ai 1.0
alias[k=kind,st=status]
k Plan
```

---

## 18. Parsing Requirements

A conforming parser MUST construct a syntactic AST representing the document structure, matching the Minimum normative AST. Table cells are captured as raw strings in the initial AST. The parser MUST preserve string literals and quotedness metadata verbatim.

---

## 19. Validation Requirements

A conforming validator MUST load an SDIF schema and apply type and structure constraints as a post-parsing step. Schema-driven typing is applied during validation or normalization, not during raw parsing.

A validator MUST support the following schema validation keywords:
- `fields[name,type,required,default]:`
- `tables[name,ordered,primary_key]:`
- `columns[table,name,type,required]:`
- `relations[predicate,subject_type,object_type,required]:`
- `rule_functions[name,min_args,max_args]:`

The validation contract MUST check:
- - Required fields.
- - Types.
- - Enumerations.
- - Allowed tables.
- - Required columns.
- - Allowed relation predicates.
- - Allowed rule functions.

---

## 20. Canonicalization

Canonicalization converts an SDIF Source document into its unique canonical representation. Conforming canonicalizers MUST use UTF-8 encoding, LF line endings, and two-space indentation, removing all comments and blank lines. The output MUST end with exactly one LF character.

Stable hashing is performed by computing SHA-256 over the canonicalized bytes:

```text
sha256(sdif_canonical_bytes)
```

---

## 21. JSON Mapping

SDIF maps directly to JSON structures. Scalar fields map to key-value properties, objects map to nested JSON objects, tables map to uniform lists of objects, and relations map to an array of triple objects containing `subject`, `predicate`, and `object`.

To represent heterogeneous or nested lists in JSON without expanding the core SDIF source grammar with list-of-block syntax, SDIF uses a reserved repeated `__item` object block convention. A scalar value inside a nested list is wrapped in a field named `__value`.

---

## 22. Diagnostics

Conforming tools SHOULD produce structured diagnostics containing:
- `code` (e.g., `SDIF_TABLE_ARITY`)
- `severity` (`info`, `warn`, `deny`, `error`, `fatal`)
- `message`
- `line`
- `column`
- `path`
- `hint`

---

## 23. Security Considerations

Conforming parsers and validators MUST implement defenses against:

1. **Unbounded Include Loops**: A parser MUST reject include loops. `@include` is disabled by default.
2. **Fs Exploits**: Traversal of parent directories MUST NOT be permitted unless explicitly enabled by local policy.
3. **Alias Expansion Exploits**: Conforming AI tools MUST reject alias configurations targeting protected terms.
4. **Hostile Tables**: Limits MUST be enforced on cell sizes, column width, and total row counts.
5. **Narrative Block Prompt Injections**: AI tools MUST treat narrative blocks strictly as document data and never as execution directives.

---

## 24. Versioning and Extensibility

### 24.1 Format Versioning

The `@sdif` version identifier represents the format specification. The package version may advance independently from the document format version.

### 24.2 Media Type and File Extensions

The recommended file extensions are:
- `.sdif`: SDIF Source
- `.sdif.canon`: SDIF Canonical
- `.sdif.ai`: SDIF AI View

The proposed MIME type is `application/sdif`.

### 24.3 Internationalization

Conforming SDIF text MUST be processed as UTF-8. Non-ASCII characters are valid inside keys, values, and narrative blocks.

---

## 25. Conformance

### 25.1 Parser Conformance

A conforming SDIF Parser:

1. MUST parse UTF-8 text and reject syntax errors.
2. MUST build a syntactic AST preserving quotes and raw cell strings.
3. MUST reject unclosed quoted fields or trailing content after close.

### 25.2 Serializer Conformance

A conforming SDIF Serializer:

1. MUST output valid SDIF text.
2. MUST escape reserved characters when generating string fields.

### 25.3 Canonicalizer Conformance

A conforming SDIF Canonicalizer:

1. MUST output UTF-8 text ending in a single LF.
2. MUST sort fields and tables deterministically.
3. MUST remove comments and redundant whitespace.

### 25.4 Validator Conformance

A conforming SDIF Validator:

1. MUST apply schema-driven type checking.
2. MUST evaluate declarative rules.
3. MUST emit structured diagnostics for violations.

### 25.5 AI Projection Conformance

A conforming AI Projector:

1. MUST support `@sdif.ai` header and aliases.
2. MUST preserve string types on columns with the `$` suffix.
3. MUST project back to canonical SDIF source reversibly.

---

## Appendix A. Grammar

The EBNF grammar for SDIF core syntax:

```ebnf
document        = spacing, statement*, EOF ;
statement       = blank_line | comment_line | directive_line | alias_header | field_line | object_block | table_block | relation_block | rule_block | narrative_block ;
blank_line       = horizontal_space*, newline ;
comment_line     = horizontal_space*, comment, newline ;
comment          = "#", not_newline* ;
newline          = "\n" | "\r\n" ;
horizontal_space = " " | "\t" ;
spacing          = (blank_line | comment_line)* ;
directive_line  = "@", directive_name, directive_args?, inline_comment?, newline ;
directive_name  = IDENT ;
directive_args  = horizontal_space+, value, (horizontal_space+, value)* ;
field_line      = key, horizontal_space+, value, inline_comment?, newline ;
key             = IDENT ;
object_block       = key, ":", inline_comment?, newline, indented_statement+ ;
indented_statement = INDENT, statement_body, DEDENT? ;
statement_body     = field_line | object_block | table_block | relation_block | rule_block | narrative_block | comment_line | blank_line ;
list            = "[", list_items?, "]" ;
list_items      = value, (",", value)* ;
table_block     = table_header, table_row+ ;
table_header    = key, "[", column_list, "]", ":", inline_comment?, newline ;
column_list     = column, (",", column)* ;
column          = IDENT, ["$"] ;
alias_header    = "alias", "[", alias_entry, (",", alias_entry)*, "]", inline_comment?, newline ;
alias_entry     = IDENT, "=", IDENT ;
table_row       = INDENT, table_cell, (HTAB, table_cell)*, inline_comment?, newline ;
table_cell      = table_cell_char* ;
HTAB            = ? U+0009 horizontal tab ? ;
relation_block  = "rel", ":", inline_comment?, newline, relation_row+ ;
relation_row    = INDENT, relation_subject, horizontal_space+, relation_predicate, horizontal_space+, relation_object, inline_comment?, newline ;
rule_block      = "rules", ":", inline_comment?, newline, rule_row+ ;
rule_row        = INDENT, expression, inline_comment?, newline ;
expression      = "(", action_name, horizontal_space+, condition, ")" ;
action_name     = "deny" | "warn" ;
condition       = compact_call | IDENT ;
compact_call    = function_name, "(", (value | compact_call), (",", (value | compact_call))* , ")" ;
multiline_string  = "\"\"\"", multiline_content, "\"\"\"" ;
IDENT           = IDENT_START, IDENT_CONT* ;
IDENT_START     = "A".."Z" | "a".."z" | "_" ;
IDENT_CONT      = IDENT_START | "0".."9" | "-" ;
```

---

## Appendix B. Examples

### B.1 Complete Technical Plan

```sdif
@sdif 1.0
kind Plan
id release.v2.validation_plan
schema example.plan.v1
authority Canonical
lifecycle Active

created_at 2026-05-20T18:00:00Z
status open
priority P0
owner team.platform

title "Release v2 validation plan"

intent """
Define the minimum validation requirements for release v2.
"""

scope[in,out]:
	schema validation	ui redesign
	canonicalization	feature expansion

milestones[id,status,gate,evidence]:
	R1	done	validate-syntax	reports/syntax.md
	R2	done	validate-canonical	reports/canonical.md
	R3	pending	validate-schema	reports/schema.md
	R4	pending	validate-semantics	reports/semantics.md

rel:
  R3 depends_on R2
  R4 depends_on R3
  release.v2.validation_plan validated_by validation.report.v2

rules:
  (deny missing(evidence))
  (deny dangling(rel))
  (deny invalid(lifecycle))
  (warn eq(authority,Unknown))
```

### B.2 Evidence Validation Report

```sdif
@sdif 1.0
kind EvidenceReport
id validation.report.v2
schema example.evidence_report.v1
authority Derived
lifecycle Active

subject release.v2.validation_plan
verified_at 2026-05-20T18:15:00Z
verifier validator.core
result pass

checks[id,result,message]:
	syntax	pass	source document parsed successfully
	canonical	pass	canonical output is stable

artifacts[id,path,sha256]:
	summary	reports/validation-summary.md	sha256:0000000000000000000000000000000000000000000000000000000000000000

rel:
  validation.report.v2 validates release.v2.validation_plan
```

### B.3 Semantic Registry

```sdif
@sdif 1.0
kind Registry
id example.registry
schema example.semantic_registry.v1
authority Canonical
lifecycle Active

entries[id,kind,lifecycle,authority,path,evidence]:
	example.schema	Schema	Active	Canonical	schemas/example.schema.sdif	reports/schema.md
	validator.core	Tool	Active	Canonical	tools/validator.md	reports/validator.md

rel:
  example.registry governed_by example.schema
  example.registry validated_by validator.core

rules:
  (deny missing(entries.evidence))
  (deny dangling(rel))
```

---

## Appendix C. Python Reference Implementation Notes

### C.1 Command Line Interface

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

### C.2 Token Estimation and tiktoken

The `sdif tokens` CLI checks byte size and estimates token usage. It leverages `tiktoken/cl100k_base` if installed, falling back to a 4-bytes-per-token heuristic.

The repository benchmark derives JSON compact, JSON pretty, YAML, XML, CSV Bundle, SDIF, SDIF AI, and optionally TOON from the same canonical JSON fixture source. `SDIF AI` is produced from the generated SDIF document with the `.sdif.ai` projection so the benchmark tracks the AI-context surface separately from canonical SDIF. For benchmark fairness, that projection chooses the smaller of a headerless context-window view and an explicit alias-header view; aliases are only useful when their decoding header pays for itself. The projection also uses compact table rows and `$` string-preserved columns when those choices reduce repeated syntax without changing semantics. It always reports an `Estimate` column using the same deterministic 4-UTF-8-bytes-per-token fallback as `sdif tokens`, and uses `tiktoken` as the primary ordering and ratio metric when that optional dependency is installed. `CSV Bundle` is intentionally named as a bundle because a full semantic SDIF document may contain metadata, nested values, relations, and rules that cannot fit in a single honest flat CSV table.

### C.3 Reference AST Model

The parser constructs the following AST node structure:
- `Document(directives, statements)`
- `Directive(name, args)`
- `Field(key, value, quoted=False)`
- `ObjectBlock(key, statements)`
- `Table(name, columns, rows, quoted_columns=frozenset())`
- `Relation(subject, predicate, object, object_quoted=False)`
- `Rule(source, expression=None)`
- `Narrative(key, text)`

---

## Appendix D. Reserved Extension Surfaces

Versioned or not-yet-implemented extensions include remote includes, remote schemas, complex namespaces, deep graph validation, digital signatures, advanced type unions, semantic numeric/date normalization, and non-declarative rule execution.

These are explicitly outside the scope of SDIF 1.0 stable core. Conforming 1.0 parsers and validators MUST reject these extensions unless explicit profile flags are set.

---

## Appendix E. Changelog

- **1.0.0**: Stable release of the Semantic Data Interchange Format core schema.
- **1.1.0**: Split specification files to independent repository layout, keeping pure specifications in the `sdif-spec` repository and reference implementation notes decoupled from the normative core.
