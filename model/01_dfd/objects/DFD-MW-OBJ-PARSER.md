---
type: dfd_object
id: DFD-MW-OBJ-PARSER
name: Core Parser
kind: process
tags:
  - DFD
  - Parser
---

# Core Parser

## Summary

Obsidian Vault内のMarkdownアセットを読み込み、frontmatter / schemaを判定して各parserへ委譲する主要プロセスオブジェクト。
parseVaultFile は detectFileType による schema/type 検出、parser dispatch、schema-driven relations parsing、Source Links parsing、warnings出力を担う。

## Details

| key | value | notes |
|---|---|---|
| owner | Core / Parsers | vault-index.ts と parsers 配下 |
| boundary | parseVaultFile | Vault fileからParseResultを生成 |
| detection | detectFileType | schema/typeからfileTypeを判定 |
| dispatch | parser modules | fileTypeごとのparserへ委譲 |
| relations | parseRelationsFile | schema: model_relations_v1 |
| source_links | parseSourceLinks | 各parserがSource Linksを解析 |
| output | [[DATA-MW-CORE-PARSED-MODEL]] | ParsedFileModel |
| warnings | [[DATA-MW-CORE-DIAGNOSTIC]] | ParseResult.warnings |
| related_process | [[PROC-MW-PARSER-PARSE-VAULT-FILE]] | 解析プロセス詳細 |

## Source Links

| path | notes |
|---|---|
| src/core/vault-index.ts | parseVaultFile / indexSingleFile |
| src/core/schema-detector.ts | detectFileType |
| src/parsers/relations-parser.ts | schema-driven relations parser |
| src/parsers/source-links-parser.ts | Source Links parser |
| src/parsers/dfd-diagram-parser.ts | DFD diagram parser |
