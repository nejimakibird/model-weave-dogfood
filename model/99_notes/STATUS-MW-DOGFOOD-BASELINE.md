---
id: STATUS-MW-DOGFOOD-BASELINE
name: Model Weave Dogfood Baseline
tags:
  - Status
  - Baseline
  - Coverage
---

# Model Weave Dogfood Baseline

## Summary

This file records which Model Weave release version the dogfood model currently reflects.

Dogfood is not the source implementation.
It is a source-backed documentation and reverse-engineering model for Model Weave itself.

## Current Baseline

| item | value | notes |
|---|---|---|
| dogfood_alignment_version | 0.1.19 | v0.1.19 alignment is reflected through COV-013 |
| source_reference | model-weave v0.1.19 | Source repository is reference-only during dogfood updates |
| dogfood_branch_base | develop | Dogfood documentation work is based on develop |
| last_alignment_task | COV-013 | Flow Diagram MVP / Diagnostics / Color Scheme alignment |
| active_catch_up_task | COV-014 | v0.1.18 diagnostics, lower panel, Quick Fix, and table-row handling catch-up |
| maturity_policy | source-backed only | implemented requires source confirmation |

## Release Coverage

| release | status | notes |
|---|---|---|
| v0.1.17 | reflected | Business Flow, Domain Sources, Color Scheme, Source Links section support reflected in prior dogfood work |
| v0.1.18 | reflected | Lower panel tabs, diagnostics bulk copy, manual guidance, Quick Fix MVP, and empty table row handling reflected through COV-014 |
| v0.1.19 | reflected / reviewing | Flow Diagram MVP, Color Scheme preview editing, Applied Color Scheme compact display, and diagnostics guidance reflected through COV-013 |

## Known Catch-up Items

| id | release | area | status | notes |
|---|---|---|---|---|
| COV-014A | v0.1.18 | Lower panel tabs | reflected | Stable tabs across graph / non-graph review views and renderer switching |
| COV-014B | v0.1.18 | Diagnostics copy actions | reflected | Bulk Markdown copy for all diagnostics / errors / warnings / notes |
| COV-014C | v0.1.18 | Manual-edit guidance | reflected | Diagnostic details include manual repair guidance |
| COV-014D | v0.1.18 | Quick Fix MVP | reflected | Missing frontmatter id / name only; missing type is not auto-fixed |
| COV-014E | v0.1.18 | Markdown table rows | reflected | Fully empty data rows ignored; partially filled rows still diagnosed |
| COV-013A | v0.1.19 | Flow Diagram samples | pending | Representative dogfood sample is separated from release alignment |

## Rules

- Do not mark a feature implemented only because it appears in docs or release notes.
- Do not update `model-weave/` during dogfood work.
- Do not hide unresolved diagnostics by duplicating definitions unless that matches the actual FORMAT design.
- Keep future concepts such as Source Links Explorer, Reference Explorer, Model Index View, Flow Diagram Surface View, Communication View, folding, projection, and transition coverage generation separate from implemented behavior.
- Quick Fix MVP is limited to missing frontmatter id / name. It does not automatically repair missing type, invalid table headers, or arbitrary Markdown body issues.
- COV-014D reflects Quick Fix MVP as [[DATA-MW-DIAGNOSTIC-QUICK-FIX]] and [[PROC-MW-DIAGNOSTIC-QUICK-FIX-APPLY]].
- COV-014B reflects diagnostics bulk Markdown copy as [[DATA-MW-DIAGNOSTIC-COPY-ACTION]] and Diagnostics Panel actions.
- COV-014C reflects manual-edit guidance as [[DATA-MW-DIAGNOSTIC-MANUAL-GUIDANCE]] and Diagnostics Panel details.
- COV-014E reflects Markdown table row handling in [[DATA-MW-CORE-MARKDOWN-TABLE]] and [[RULE-MW-PARSER-SECTION-STRUCTURE]].
- COV-014A reflects stable lower panel tabs as [[DATA-MW-LOWER-PANEL-TAB-STATE]] and Viewer Main Frame UI.

## Source Links

| path | notes |
|---|---|
| model-weave/docs/releases/v0.1.18.md | v0.1.18 lower panel tabs, diagnostics copy actions, Quick Fix MVP, and empty table row release notes |
| model-weave/src/views/modeling-preview-view.ts | Lower panel tabs, diagnostics bulk copy actions, diagnostic details, and missing frontmatter Quick Fix implementation |
| model-weave/src/parsers/markdown-table.ts | Fully empty Markdown table data rows are ignored before row validation |
