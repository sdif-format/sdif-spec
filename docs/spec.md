<!-- markdownlint-disable MD010 MD013 MD060 -->
<!--
  This specification intentionally contains literal HTAB characters in SDIF
  table examples and long grammar/contract lines. Those are semantic examples,
  not prose formatting defects.
-->

# SDIF — Semantic Data Interchange Format

## Table of Contents

- [1. Abstract](#1-abstract)
  - [1.1 Motivation](#11-motivation)
  - [1.2 Design Principles](#12-design-principles)
- [2. Status of This Document](#2-status-of-this-document)
  - [2.1 Stable core contract](#21-stable-core-contract)
- [3. Terminology and Conventions](#3-terminology-and-conventions)
  - [3.1 Requirement Keywords](#31-requirement-keywords)
- [4. Overview](#4-overview)
  - [4.1 Comparison with Other Formats](#41-comparison-with-other-formats)
  - [4.2 AI Compatibility and Agent Interoperability](#42-ai-compatibility-and-agent-interoperability)
  - [4.3 Document Lifecycle](#43-document-lifecycle)
  - [4.4 When to Use SDIF](#44-when-to-use-sdif)
  - [4.5 When Not to Use SDIF](#45-when-not-to-use-sdif)
- [5. Goals and Non-Goals](#5-goals-and-non-goals)
  - [5.1 Design Goals](#51-design-goals)
  - [5.2 Non-Goals](#52-non-goals)
- [6. Data Model](#6-data-model)
- [7. Document Structure](#7-document-structure)
- [8. Lexical Rules](#8-lexical-rules)
  - [8.1 Streaming Considerations](#81-streaming-considerations)
- [9. Directives](#9-directives)
- [10. Scalar Fields](#10-scalar-fields)
  - [10.1 Type Precedence](#101-type-precedence)
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
  - [19.1 Schema Responsibilities](#191-schema-responsibilities)
  - [19.2 Minimal Schema Example](#192-minimal-schema-example)
  - [19.3 Validation Pipeline Phases](#193-validation-pipeline-phases)
- [20. Canonicalization](#20-canonicalization)
  - [20.1 Stable Hashing and Signing](#201-stable-hashing-and-signing)
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
- [26. Interoperability Considerations](#26-interoperability-considerations)
- [Appendix A. Grammar](#appendix-a-grammar)
- [Appendix B. Examples](#appendix-b-examples)
- [Appendix C. Reserved Extension Surfaces](#appendix-c-reserved-extension-surfaces)
- [Appendix D. Changelog](#appendix-d-changelog)
- [Appendix E. Style Recommendations](#appendix-e-style-recommendations)
- [Appendix F. Design Rationale](#appendix-f-design-rationale)

---

## 1. Abstract

This document defines the technical specification for SDIF, a compact semantic data interchange format designed for AI agents and deterministic machine workflows, with human-auditable source files.

### 1.1 Motivation

SDIF exists for workflows where structured data is not merely transported, but inspected, validated, canonicalized, compared, signed, summarized, or placed into AI model context windows.

Existing formats solve important parts of this problem. JSON provides a widely deployed data model and excellent API interoperability. YAML improves human authoring but allows flexibility that can complicate deterministic parsing and canonicalization. CSV is compact for uniform tabular data but does not express hierarchy, relations, validation intent, or narrative context. RDF-style formats model semantic relationships but are often too specialized for mixed operational documents. Token-oriented formats reduce repetition for model input but generally do not define a full validation, canonicalization, and evidence-oriented document lifecycle.

SDIF is designed to occupy the space between these formats: compact enough for AI and machine workflows, explicit enough for validation, deterministic enough for hashing and signing, and readable enough for human audit.

### 1.2 Design Principles

SDIF is guided by the following principles:

1. **Semantic density over syntactic convenience**: repeated structure should be expressed once when doing so preserves meaning.
2. **Determinism over flexibility**: syntax should avoid ambiguous constructs that make canonicalization or cross-implementation behavior difficult.
3. **Schema-first interpretation**: raw parsing captures structure, while schema validation assigns stronger semantic meaning.
4. **Human auditability**: source documents should remain readable and reviewable without requiring specialized binary tooling.
5. **AI compatibility without instruction confusion**: narrative text and document content are data, not runtime instructions.
6. **Reversible optimization**: AI-oriented projections may reduce tokens, but must remain reversible to canonical, semantically equivalent SDIF.

---

## 2. Status of This Document

This document is the authoritative technical specification for SDIF 1.0. The main body of this document and Appendix A are normative unless explicitly marked otherwise. Appendices B, C, D, E, and F are informative.

**Format version:** 1.0
**Specification document version:** 1.2.0
**Name:** Semantic Data Interchange Format
**Short name:** SDIF
**Recommended source extension:** `.sdif`
**Recommended canonical extension:** `.sdif.canon`
**Recommended AI-optimized extension:** `.sdif.ai`
**Proposed media type:** `application/sdif`
**Encoding:** UTF-8
**Design mode:** schema-first, semantic-first, token-aware, canonicalization-ready

### 2.1 Stable core contract

This specification document records the stable core specification. The format directive identifies the stable core syntax and semantic contract. `@sdif 1.0` identifies the stable core syntax and semantic contract. The package version may advance independently from the format version.

Stable core behavior includes parsing, the reference AST shape, schema-driven validation, canonical syntax, safe default policies, local includes behind explicit policy, and `.sdif.ai` reversibility to canonical source. Reserved extension surfaces include remote includes, remote schemas, complex namespaces, deep graph validation, digital signatures, advanced type unions, semantic numeric/date normalization, and non-declarative rule execution.

---

## 3. Terminology and Conventions

### 3.1 Requirement Keywords

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 [RFC2119] [RFC8174] when, and only when, they appear in all capitals.

---

## 4. Overview

SDIF combines the strict structural model of JSON, the readability of YAML and TOML, the tabular compactness of CSV, the graph-like expressiveness of semantic triples, the bounded narrative capability of Markdown-style text blocks, and the determinism required for canonicalization, hashing, signing, and reproducible validation.

The format is structured around the core principle of maximizing semantic density per token without sacrificing validation safety or parsing determinism.

### 4.1 Relationship to Other Formats

SDIF is not intended to replace JSON as the default public API format. JSON remains the most interoperable choice for general-purpose data exchange. SDIF instead targets documents where repeated structure, validation metadata, relations, narrative context, and canonicalization requirements appear together.

Compared with YAML, SDIF intentionally provides fewer syntactic forms. This reduces authoring flexibility but improves parser determinism and makes canonical output easier to specify.

Compared with CSV, SDIF keeps the compactness of declaring tabular columns once, but embeds tables inside a richer document model that can also contain metadata, relations, rules, and narrative blocks.

Compared with TOON-style representations, SDIF shares the goal of reducing repetition for AI context usage, but SDIF also defines source, canonical, and AI profiles as distinct lifecycle surfaces. The AI view is an optimization profile, not the whole format.

Compared with RDF-style graph formats, SDIF supports explicit relations but does not attempt to become a full ontology language. Relations are intended to support validation, traceability, and semantic linking inside practical documents.

### 4.2 AI Compatibility and Agent Interoperability

SDIF helps AI models by reducing repetition, making semantics explicit, separating structured data from narrative text, distinguishing relations from fields, supporting declared aliases, and reducing syntactic noise compared to JSON.

AI agents processing SDIF MUST distinguish document content, external instructions, and runtime policy. AI tools SHOULD use clear aliases and avoid excessively wide tables.

### 4.3 Document Lifecycle

An SDIF document may move through several representations during its lifecycle:

1. **SDIF Source**: A human or agent authors an SDIF Source document. The source document may contain comments, flexible ordering, and narrative blocks intended for review.
2. **Syntactic AST**: A parser reads the Source document and constructs a syntax-preserving AST without applying schema-driven value typing.
3. **Schema-Validated Document**: A validator checks the AST against schema-defined types, enums, required fields, allowed tables, relation predicates, and declared rule functions. Declarative rule evaluation may be applied by validators that implement the rule evaluation profile.
4. **SDIF Canonical**: The source document is canonicalized into a deterministically ordered and stripped representation for stable hashing and signing (removing comments, blank lines, and styling variations).
5. **SDIF AI View**: For AI context usage, the document is projected into a token-optimized representation using aliases and compact table rows, while preserving reversibility to canonical, semantically equivalent SDIF.

### 4.4 When to Use SDIF

SDIF is recommended for:

- Operational documents and manifests that require stable signing/hashing.
- Automated workflows and agents exchanging dense data in LLM context windows.
- Schema-first validation pipelines where data fields must match strict types while retaining descriptive narrative sections.

### 4.5 When Not to Use SDIF

SDIF is not recommended for:

- General-purpose public REST APIs (use JSON instead).
- Large, multi-gigabyte flat data sets where columnar databases (like Parquet) are required.
- Storing deeply nested recursive structures that do not naturally map to object blocks and tables.

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

An SDIF source document is composed of:

1. Directives
2. Scalar fields
3. Objects
4. Lists
5. Tables
6. Relations
7. Declarative rules
8. Narrative blocks
9. Source-only comments

Comments are source-only trivia. Comments MAY appear in SDIF Source, but MUST NOT be part of the canonical AST.

---

## 7. Document Structure

A document MUST consist of a sequence of statements ending with an optional sequence of blank lines and comments. Statement types MUST NOT be mixed in ways that violate lexical indentation rules.

---

## 8. Lexical Rules

All SDIF documents MUST be UTF-8 encoded. Line endings MUST be normalized to LF (`\n`) for canonical output, though parsers MAY tolerate CRLF (`\r\n`).

Indentation MUST use spaces. Tabs MUST NOT be used for indentation in strict mode. The horizontal tab character (`HTAB`, U+0009) is strictly reserved as the column separator inside table rows.

Comments start with the hash character (`#`) outside strings and multiline blocks and continue to the end of the line.

### 8.1 Streaming Considerations

SDIF supports streaming because statements are line-oriented, tables/relations can be processed row by row, and narrative blocks are explicitly delimited. However, full semantic validation and canonicalization with sorting require accumulating the entire document.

---

## 9. Directives

Directives start with `@` and provide format metadata. The following directives are reserved in SDIF:

- `@sdif`: Declares the SDIF format version (e.g., `@sdif 1.0`). This directive identifies the stable core syntax and semantic contract.
- `@sdif.ai`: Declares the token-optimized SDIF AI projection.
- `@profile`: Declares the document profile.
- `@vocab`: Declares a vocabulary.
- `@base`: Declares a base URI or semantic namespace.
- `@namespace`: Declares a namespace prefix. The core namespace form is `@namespace prefix iri`. Complex namespace behavior is reserved for future extensions.
- `@include`: Includes another local document or vocabulary. `@include` is disabled by default.

Remote includes and remote schemas are reserved extension surfaces. Conforming core processors MUST reject them unless an explicit extension profile enables them.

---

## 10. Scalar Fields

A scalar field consists of a key identifier and a value separated by horizontal space. A scalar value outside tables MUST be quoted if it contains spaces, commas intended as text, parentheses, brackets, quotes, comment markers, or non-identifier characters.

For example, valid unquoted and quoted scalar fields:

```sdif
status open
priority P0
title "Release validation plan"
created_at 2026-05-20T18:00:00Z
```

Here, `status` and `priority` contain unquoted identifier values. `title` is quoted because it contains spaces. The timestamp `created_at` is parsed according to the datetime precedence rules.

Quoted scalar fields outside tables MUST satisfy:

1. **Single-line closure**: The closing double quote `"` MUST appear on the same logical line as the opening double quote `"`.
2. **No trailing content**: No non-whitespace, non-comment characters may follow the closing double quote on the same line.

The following field is invalid in SDIF because trailing non-whitespace, non-comment content follows the closing quote:

```sdif
title "Release validation plan" extra_content
```

### 10.1 Type Precedence

When an unquoted token could match multiple types, the following precedence applies:

1. `null`
2. boolean
3. datetime
4. date
5. duration
6. number
7. identifier value

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

---

## 13. Tables

Tables represent uniform arrays of objects. A table block MUST start with a table header naming the table and declaring its columns in square brackets, followed by table rows. Table columns are separated by the literal horizontal tab character (`HTAB`, U+0009), not by spaces or commas. Note that in the example below, the spacing between columns consists of literal `HTAB` separators:

```sdif
milestones[id,status,gate]:
  R1	done	verify
  R2	pending	verify
```

Under the stable core configuration, strict mode prohibits inline comments inside table rows.

In SDIF Source and Canonical profiles, table rows MUST be indented. In SDIF AI View, compact non-indented table rows (ai_table_row) MAY be used after a table header. The `ai_table_block` grammar production is valid only under the SDIF AI View profile. Conforming Source and Canonical processors MUST reject non-indented table rows.

A column name ending in `$` is a `.sdif.ai` string-preserved column marker. Consumers MUST strip the suffix from the semantic column name and parse all cells in that column as strings. The `$` suffix is a decoding hint only and is not part of the semantic column name.

---

## 14. Relations

Relations describe semantic relationships expressed as subject-predicate-object triples:

```sdif
rel:
  release.b depends_on release.a
```

A predicate MUST be allowed by the declared schema or vocabulary. In standard core documents, a relation has exactly three parts.

---

## 15. Declarative Rules

Rules represent validation constraints using parenthesized expressions. They are not executable code.

```sdif
rules:
  (deny missing(evidence))
  (warn eq(authority,Unknown))
```

A parser MUST preserve the rule source text. If rule expression parsing is enabled, the parser or validator SHOULD expose a structured `RuleExpression` representation, such as a logical `RuleExpression(action, function, args)` node.

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

Profiles define structural constraints for different use cases. For example, the same data represented under the three profiles:

**SDIF Source**:

```sdif
@sdif 1.0
# Source may contain comments and formatting
kind Plan
id release.v1

status open
priority P0
```

**SDIF Canonical**:

```sdif
@sdif 1.0
kind Plan
id release.v1
priority P0
status open
```

**SDIF AI View**:

```sdif
@sdif.ai 1.0
alias[k=kind,st=status]
k Plan
id release.v1
priority P0
st open
```

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

A valid `.sdif.ai` document MUST include the `@sdif.ai 1.0` directive.

Headerless AI snippets MAY be used as transport- or prompt-local fragments, but they are not standalone conforming `.sdif.ai` documents.

Alias headers are valid only in the SDIF AI View profile unless an explicit extension profile enables them. Conforming Source and Canonical processors MUST reject alias headers.

---

## 18. Parsing Requirements

A conforming parser MUST construct a syntactic AST representing the document structure. Table cells are captured as raw strings in the initial AST. The parser MUST preserve string literals and quotedness metadata verbatim.

### 18.1 Minimum normative AST

The following defines the minimum normative AST for SDIF v1. The minimum abstract document model consists of the following components:

- Directives: Represent meta-commands and configuration.
- Fields: Represent key-value bindings.
- Objects: Represent nested sections.
- Lists: Represent sequences of values.
- Tables: Represent uniform rows. Table cells MUST be captured as raw strings in the initial AST.
- Relations: Represent subject-predicate-object semantic connections.
- Rules: Represent policy conditions.
- Narratives: Represent block text fields.

The Python reference AST is described in the informative [reference-python.md](reference-python.md) document. The Rust implementation AST is described in the informative [reference-rust.md](reference-rust.md) document.

---

## 19. Validation Requirements

A conforming schema validator MUST load an SDIF schema when schema validation is requested and apply type and structure constraints as a post-parsing step. Schema-driven typing is applied during validation or normalization, not during raw parsing. A validator MAY perform syntax-only validation without a schema, but MUST NOT treat an unschematized document as fully schema-validated.

A validator MUST support the following schema validation keywords:

- `fields[name,type,required,default]:`
- `tables[name,ordered,primary_key]:`
- `columns[table,name,type,required]:`
- `relations[predicate,subject_type,object_type,required]:`
- `rule_functions[name,min_args,max_args]:`

The validation contract MUST check:

- Required fields.
- Types.
- Enumerations.
- Allowed tables.
- Required columns.
- Allowed relation predicates.
- Allowed rule functions.

### 19.1 Schema Responsibilities

A schema defines the expected shape and semantics of a document:

1. Document kind through a required `kind` field and/or an `Enum(...)` field type.
2. Required fields.
3. Optional fields and their types (Null, Boolean, Integer, Decimal, String, Identifier, Path, Date, DateTime, Duration, Enum, List).
4. Allowed tables, required columns, and whether table row order is semantically significant.
5. Allowed relation predicates, and allowed rule functions.

### 19.2 Minimal Schema Example

```sdif
@sdif 1.0
kind Schema
id example.plan.core
schema sdif.schema.core
authority Canonical
lifecycle Active

for_kind Plan

fields[name,type,required,default]:
  kind	Enum(Plan)	true	null
  id	Identifier	true	null
  schema	Identifier	true	null
  authority	Enum(Canonical,Reference,Working,Derived,External,Unknown)	false	Unknown
  lifecycle	Enum(Draft,Active,Deprecated,Superseded,Archived,Quarantined)	false	Draft
  title	String	true	null
  status	Enum(open,closed,blocked)	true	open
  priority	Enum(P0,P1,P2,P3)	false	P2

tables[name,ordered,primary_key]:
  milestones	true	id
  scope	false	in

columns[table,name,type,required]:
  milestones	id	Identifier	true
  milestones	status	Enum(done,pending,blocked)	true
  milestones	gate	Identifier	false
  milestones	evidence	Path	false
  scope	in	Identifier	true
  scope	out	Identifier	true

relations[predicate,subject_type,object_type,required]:
  depends_on	Identifier	Identifier	false
  validated_by	Identifier	Identifier	false
  governed_by	Identifier	Identifier	false
  emits	Identifier	Path	false

rule_functions[name,min_args,max_args]:
  missing	1	1
  dangling	1	1
  invalid	1	1
  unknown	1	1
  eq	2	2
```

### 19.3 Validation Pipeline Phases

1. **Lexing**: Validates UTF-8 tokens, closed string literals, and comment placement.
2. **Parsing**: Verifies indentation, closed blocks, table row column arity, and relation triples.
3. **Normalization**: Resolves namespaces, local includes (if enabled), and aliases.
4. **Schema Validation**: Enforces type compatibility, enum lists, required fields, and column shapes.
5. **Semantic Validation**: Evaluates dangling relations, lifecycle constraints, and declarative rule predicates.

---

## 20. Canonicalization

Canonicalization converts an SDIF Source document into a deterministic canonical representation under the active SDIF version and schema policy. Conforming canonicalizers MUST use UTF-8 encoding, LF line endings, and two-space indentation, removing all comments and blank lines. The output MUST end with exactly one LF character.

Unordered tables with a declared primary key are sorted by that key when a schema is provided. Relations and rules are sorted deterministically. Alias expansion, numeric normalization, date-time equivalence, and semantic merging remain future versioned policies.

### 20.1 Stable Hashing and Signing

Stable hashing is performed by computing SHA-256 over the canonicalized bytes:

```text
sha256(sdif_canonical_bytes)
```

Signatures should be applied directly to canonical bytes.

---

## 21. JSON Mapping

SDIF maps directly to JSON structures. Scalar fields map to key-value properties, objects map to nested JSON objects, tables map to uniform lists of objects, and relations map to an array of triple objects containing `subject`, `predicate`, and `object`.

For example, an SDIF snippet and its equivalent JSON:

**SDIF**:

```sdif
kind Plan
id release.v1
milestones[id,status]:
  R1	done
rel:
  release.v1 validated_by R1
```

**JSON**:

```json
{
  "kind": "Plan",
  "id": "release.v1",
  "milestones": [
    {
      "id": "R1",
      "status": "done"
    }
  ],
  "rel": [
    {
      "subject": "release.v1",
      "predicate": "validated_by",
      "object": "R1"
    }
  ]
}
```

To represent heterogeneous or nested lists in JSON without expanding the core SDIF source grammar with list-of-block syntax, SDIF uses a reserved repeated `__item` object block convention. A scalar value inside a nested list is wrapped in a field named `__value`.

Quoted scalar markers and `.sdif.ai` `$` table columns preserve JSON string semantics for values that otherwise look like `null`, booleans, numbers, or lists.

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
2. **Filesystem Access**: Traversal of parent directories MUST NOT be permitted unless explicitly enabled by local policy.
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

The proposed media type is `application/sdif`. The media type is proposed and not yet registered.

### 24.3 Internationalization

Conforming SDIF text MUST be processed as UTF-8. Non-ASCII characters are valid inside string values, table cells, and narrative blocks. Unquoted keys are constrained by the `IDENT` grammar unless a future extension defines quoted or Unicode key syntax.

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
2. MUST order directives, fields, relations, rules, and tables according to the active canonicalization policy.
3. MUST preserve table row order when the schema declares the table as ordered.
4. MUST remove comments and redundant whitespace.

### 25.4 Validator Conformance

A conforming SDIF Validator:

1. MUST apply schema-driven type checking.
2. MUST validate declared rule functions against the schema.
3. MUST emit structured diagnostics for violations.
4. MAY evaluate declarative rules when it implements the rule evaluation profile.

### 25.5 AI Projection Conformance

A conforming AI Projector:

1. MUST support `@sdif.ai` header and aliases.
2. MUST preserve string types on columns with the `$` suffix.
3. MUST project back to canonical SDIF source reversibly.

---

## 26. Interoperability Considerations

To ensure consistent behavior across different implementations, conforming processors MUST adhere to the following rules:

1. **Syntax Strictness**: Conforming processors MUST reject any constructs or syntax styles that are not explicitly permitted by the active profile. Permissive parsing is encouraged only in non-conforming configurations (such as editor plugins providing active autocompletion).
2. **AST Preservation**: Parsers MUST capture and expose raw table cell values as strings in the initial AST. Any type coercion, number parsing, datetime conversion, or semantic checking MUST be applied during the validation phase, not the raw parsing phase.
3. **AI View Reversibility**: The SDIF AI View (`.sdif.ai`) MUST be treated as an optimization projection, not as a standalone semantic model. AI projectors and processors MUST ensure that an AI View document is fully and reversibly reconstructible to a canonical, semantically equivalent SDIF document.
4. **No Custom Syntactic Extensions**: Conforming processors MUST NOT introduce implementation-specific syntaxes, custom inline comments, or alternative column separation mechanisms inside the standard profiles. Custom extensions MUST be constrained to reserved extension surfaces (Appendix C) and declared profile/vocabulary directives.

---

## Appendix A. Grammar

The complete EBNF grammar for SDIF core syntax:

```ebnf
document        = spacing, statement*, EOF ;

statement       = blank_line
                | comment_line
                | directive_line
                | alias_header
                | field_line
                | object_block
                | table_block
                | relation_block
                | rule_block
                | narrative_block
                ;

blank_line       = horizontal_space*, newline ;
comment_line     = horizontal_space*, comment, newline ;
comment          = "#", not_newline* ;

newline          = "\n" | "\r\n" ;
SP               = " " ;
horizontal_space = SP ;
spacing          = (blank_line | comment_line)* ;

directive_line  = "@", directive_name, directive_args?, inline_comment?, newline ;
directive_name  = "sdif"
                | "sdif.ai"
                | "profile"
                | "vocab"
                | "base"
                | "namespace"
                | "include"
                | extension_directive
                ;
extension_directive = IDENT, (".", IDENT)* ;
directive_args  = horizontal_space+, value, (horizontal_space+, value)* ;

field_line      = key, horizontal_space+, value, inline_comment?, newline ;
key             = IDENT ;

object_block       = key, ":", inline_comment?, newline, indented_statement+ ;
indented_statement = INDENT, statement_body, DEDENT? ;
statement_body     = field_line
                   | object_block
                   | table_block
                   | relation_block
                   | rule_block
                   | narrative_block
                   | comment_line
                   | blank_line
                   ;

list            = "[", list_items?, "]" ;
list_items      = value, (",", value)* ;

table_block        = source_table_block | ai_table_block ;
source_table_block = table_header, table_row+ ;
ai_table_block     = table_header, ai_table_row+ ;
table_header       = key, "[", column_list, "]", ":", inline_comment?, newline ;
column_list     = column, (",", column)* ;
column          = IDENT, ["$"] ;
alias_header    = "alias", "[", alias_entry, (",", alias_entry)*, "]", inline_comment?, newline ;
alias_entry     = IDENT, "=", IDENT ;

table_row       = INDENT, table_cell, (HTAB, table_cell)*, inline_comment?, newline ;
ai_table_row    = table_cell, HTAB, table_cell, (HTAB, table_cell)*, inline_comment?, newline ;
table_cell      = table_cell_char* ;
table_cell_char = any_char_except_htab_or_newline ;
HTAB            = ? U+0009 horizontal tab ? ;

relation_block  = "rel", ":", inline_comment?, newline, relation_row+ ;
relation_row    = INDENT, relation_subject, horizontal_space+, relation_predicate,
                  horizontal_space+, relation_object, inline_comment?, newline ;

relation_subject   = identifier_value ;
relation_predicate = identifier_value ;
relation_object    = identifier_value | string ;

rule_block      = "rules", ":", inline_comment?, newline, rule_row+ ;
rule_row        = INDENT, expression, inline_comment?, newline ;

expression      = "(", action_name, horizontal_space+, condition, ")" ;
action_name     = "deny" | "warn" ;
condition       = compact_call | IDENT ;
compact_call    = function_name, "(", (value | compact_call), (",", (value | compact_call))* , ")" ;
function_name   = IDENT ;
expression_arg  = horizontal_space+, (value | expression) ;

narrative_block   = key, horizontal_space+, multiline_string, inline_comment?, newline? ;
multiline_string  = "\"\"\"", multiline_content, "\"\"\"" ;
multiline_content = any_char_until_triple_quote* ;

value           = null
                | boolean
                | datetime
                | date
                | duration
                | number
                | string
                | list
                | identifier_value
                ;

null            = "null" ;
boolean         = "true" | "false" ;

IDENT           = IDENT_START, IDENT_CONT* ;
IDENT_START     = "A".."Z" | "a".."z" | "_" ;
IDENT_CONT      = IDENT_START | "0".."9" | "-" ;

identifier_value = IDENTIFIER_CHAR+ ;
IDENTIFIER_CHAR  = "A".."Z"
                 | "a".."z"
                 | "0".."9"
                 | "_"
                 | "-"
                 | "."
                 | "/"
                 | ":"
                 ;

number          = integer | decimal | scientific ;
integer         = sign?, digit+ ;
decimal         = sign?, digit+, ".", digit+ ;
scientific      = sign?, digit+, ("." digit+)?, ("e" | "E"), sign?, digit+ ;
sign            = "+" | "-" ;
digit           = "0".."9" ;

string          = quoted_string ;
quoted_string   = "\"", quoted_char*, "\"" ;
quoted_char     = escaped_char | normal_string_char ;
escaped_char    = "\\", ("\\" | "\"" | "n" | "r" | "t" | "u", hex, hex, hex, hex) ;
normal_string_char = any_char_except_quote_backslash_newline ;

inline_comment  = horizontal_space+, "#", not_newline* ;

date            = digit, digit, digit, digit, "-", digit, digit, "-", digit, digit ;
datetime        = date, "T", digit, digit, ":", digit, digit, ":", digit, digit,
                  fractional_seconds?, timezone ;
fractional_seconds = ".", digit+ ;
timezone        = "Z" | sign, digit, digit, ":", digit, digit ;
duration        = "P", duration_body ;
duration_body   = IDENTIFIER_CHAR+ ;
```

---

## Appendix B. Examples

### B.1 Complete Technical Plan

```sdif
@sdif 1.0
kind Plan
id release.b.validation_plan
schema example.plan.core
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
  release.b.validation_plan validated_by validation.report.b

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
id validation.report.b
schema example.evidence_report.core
authority Derived
lifecycle Active

subject release.b.validation_plan
verified_at 2026-05-20T18:15:00Z
verifier validator.core
result pass

checks[id,result,message]:
  syntax	pass	source document parsed successfully
  canonical	pass	canonical output is stable

artifacts[id,path,sha256]:
  summary	reports/validation-summary.md	sha256:0000000000000000000000000000000000000000000000000000000000000000

rel:
  validation.report.b validates release.b.validation_plan
```

### B.3 Semantic Registry

```sdif
@sdif 1.0
kind Registry
id example.registry
schema example.semantic_registry.core
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

## Appendix C. Reserved Extension Surfaces

Versioned or not-yet-implemented extensions include remote includes, remote schemas, complex namespaces, deep graph validation, digital signatures, advanced type unions, semantic numeric/date normalization, and non-declarative rule execution.

These are explicitly outside the scope of the stable core specification. Conforming core parsers and validators MUST reject these extensions unless explicit profile flags are set.

---

## Appendix D. Changelog

- **1.0.0**: Stable release of the Semantic Data Interchange Format core schema.
- **1.1.0**: Split specification files to independent repository layout, keeping pure specifications in the `sdif-spec` repository and reference implementation notes decoupled from the normative core.
- **1.2.0**: Added explanatory narrative, design principles, document lifecycle, interoperability considerations, and design rationale appendix without changing the SDIF 1.0 core format.

---

## Appendix E. Style Recommendations

### E.1 Recommended Document Order

Suggested source order:

1. `@sdif` directive.
2. Profile and vocabulary directives.
3. `kind`.
4. `id`.
5. `schema`.
6. `authority` and `lifecycle`.
7. Timestamps and metadata.
8. Title and summary.
9. Objects, lists, and tables.
10. Relations.
11. Declarative rules.
12. Narrative blocks.

### E.2 Naming and Design Best Practices

- Use `snake_case` for keys and dot notation for hierarchical identifiers.
- Keep tables narrow (e.g., under 8-10 columns).
- Favor semantic clarity in source relations over cryptic names.

---

## Appendix F. Design Rationale

This appendix provides background details explaining why certain design decisions were made in the SDIF 1.0 specification.

### F.1 Why line-oriented syntax

Line-oriented parsing allows processors to stream files and identify structural errors early (e.g. tracking indentation boundaries line-by-line). It also keeps documents highly readable under standard diff and visual merging tools, preventing the layout drift that is common in deeply nested bracketed or brace-oriented configurations.

### F.2 Why tables use HTAB separators

Tabular structures require clear column delimiters. Using spaces as separators is highly fragile because scalar values (like paths or unquoted text fragments) often contain spaces, leading to parsing ambiguity. While comma-separated CSV works for flat files, inline commas in lists and headers make it unsuited as a nested column separator inside structured documents. The horizontal tab (`HTAB`) is a clean, single-byte ASCII character reserved solely for column layout, ensuring zero parsing ambiguity.

### F.3 Why parsing and schema validation are separate

Decoupling syntax parsing from schema-driven validation ensures that parsers remain lightweight, generic, and fast. A parser can construct a valid syntactic AST without knowing the document's vocabulary or rules, facilitating simple translation to other format ASTs (such as JSON or YAML). The validator then acts as a distinct phase, overlaying semantic rules, type assertions, and dependency constraint checks.

### F.4 Why `.sdif.ai` is a profile, not a separate format

Having `.sdif.ai` share the same parser codebase and AST rules as `.sdif` prevents fragmentation. Keeping the AI View as an optimized projection profile guarantees that any AI View snippet can be reconstructed reversibly into standard Canonical SDIF. This ensures that agent exchanges remain compatible with standard database storage, signing, and local human review tools.

### F.5 Why comments are source-only trivia

Comments are meant for human-to-human context. Removing comments from the canonical AST ensures that adding or modifying inline comments does not alter the document's semantic signature, keeping stable hashes and digital signatures invariant.

### F.6 Why non-declarative rule execution is out of scope

SDIF declarative rules (warn/deny) are validation constraints, not code. Permitting custom code execution inside interchange files introduces severe security vulnerabilities (sandbox escapes, remote execution) and undermines parser portability across languages.
