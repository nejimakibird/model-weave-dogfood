---
type: data_object
id: DATA-MW-DFD-PARSED-DIAGRAM
name: DFD解析済みダイアグラム
kind: parsed_diagram
data_format: object
tags:
  - DFD
  - Data
---

# DFD解析済みダイアグラム

## Summary

dfd_diagram Markdownから解析されたDFD専用の論理データ構造です。
現行ソースでは DfdDiagramModel に対応し、BaseFileModel の共通フィールドとDFD固有の objectEntries / flows / nodes / edges を保持します。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| fileType | ファイル種別 | string | | Y | fileType | [[CODE-MW-CORE-MODEL-TYPE]] | dfd-diagram |
| schema | schema | string | | Y | schema | | dfd_diagram |
| path | ファイルパス | string | | Y | path | | Vault内Markdown path |
| frontmatter | frontmatter | object | | Y | frontmatter | | parseFrontmatter結果 |
| sourceLinks | Source Links | SourceLink list | | Y | sourceLinks | [[DATA-MW-SOURCE-LINK]] | parseSourceLinks結果 |
| id | ID | string | | Y | id | | 原典dfd_diagramのID |
| name | 名称 | string | | Y | name | | 原典dfd_diagramの名称 |
| kind | 図面種別 | string | | Y | kind | | dfd |
| level | 階層 | string | | N | level | | 任意項目。DFD階層 |
| description | 説明 | string | | N | description | | Summary section由来 |
| objectRefs | オブジェクト参照 | string list | | Y | objectRefs | | objectEntriesから生成 |
| objectEntries | オブジェクト群 | DfdDiagramObjectEntry list | | Y | objectEntries | [[DATA-MW-DFD-OBJECT-ENTRY]] | dfd_diagram.Objects由来 |
| nodes | ノード群 | DiagramNode list | | Y | nodes | [[DATA-MW-RENDERER-GRAPH-NODE]] | Objects由来の解析時node |
| edges | エッジ群 | DiagramEdge list | | Y | edges | [[DATA-MW-RENDERER-GRAPH-EDGE]] | Flows由来の解析時edge |
| flows | フロー群 | DfdFlowModel list | | Y | flows | [[DATA-MW-DFD-FLOW-ENTRY]] | dfd_diagram.Flows由来 |
| warnings | warning | ValidationWarning list | | N | warnings | [[DATA-MW-CORE-DIAGNOSTIC]] | ParseResult.warnings |

## Notes

- DATA-MW-CORE-PARSED-MODEL は汎用解析済みモデルとして残します。
- 本オブジェクトはDFD専用の解析済みモデルであり、resolveDiagramRelations 後の ResolvedDiagram とは別である。
- parser は `## Objects` から objectEntries / objectRefs / nodes を生成し、`## Flows` から flows / edges を生成する。
- 現行実装のparser出力診断は ParseResult.warnings であり、diagnostics という直接フィールドではない。
- legacy ref-only Objects table は parser互換モードとして扱われるが、本モデルでは主要フィールドとして過剰に展開しない。
- Markdownテーブル安全性のため、複数性や任意性は list / required / notes / ref で表現します。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/types/models.ts | DfdDiagramModel | interface | 実装上のDFDモデル型 |
| src/types/models.ts | DfdDiagramObjectEntry | interface | Objects行 |
| src/types/models.ts | DfdFlowModel | interface | Flows行 |
| src/types/models.ts | DiagramNode | interface | nodes要素 |
| src/types/models.ts | DiagramEdge | interface | edges要素 |
| src/parsers/dfd-diagram-parser.ts | parseDfdDiagramFile | function | DFD parser出力 |
