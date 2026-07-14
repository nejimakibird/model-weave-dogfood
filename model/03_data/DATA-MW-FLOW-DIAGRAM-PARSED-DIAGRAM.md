---
type: data_object
id: DATA-MW-FLOW-DIAGRAM-PARSED-DIAGRAM
name: Flow Diagram解析済みダイアグラム
kind: parsed_diagram
data_format: object
tags:
  - FlowDiagram
  - Data
---

# Flow Diagram解析済みダイアグラム

## Summary

`type: flow_diagram` のMarkdownから解析されたFlow Diagramの論理データ構造です。
現行実装では `FlowDiagramModel` に対応し、`schema: flow_diagram`、`kind: screen_communication`、Flow view初期値、Domain Sourcesを持つ。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| fileType | ファイル種別 | string | | Y | fileType | [[CODE-MW-CORE-MODEL-TYPE]] | flow-diagram |
| schema | schema | string | | Y | schema | | flow_diagram |
| path | ファイルパス | string | | Y | path | | Vault内Markdown path |
| frontmatter | frontmatter | object | | Y | frontmatter | | parseFrontmatter結果 |
| sourceLinks | Source Links | SourceLink list | | Y | sourceLinks | [[DATA-MW-SOURCE-LINK]] | parseSourceLinks結果 |
| id | ID | string | | Y | id | | frontmatter id |
| name | 名称 | string | | Y | name | | frontmatter name |
| kind | 図面種別 | string | | Y | kind | | screen_communication |
| description | 説明 | string | | N | description | | Summary section由来 |
| flowView | Flow view | string | | Y | flowView | [[DATA-MW-FLOW-DIAGRAM-VIEW-STATE]] | detail / screen。frontmatter `flow_view` またはfallbackから解析される初期表示 |
| flowViewSpecified | Flow view指定有無 | boolean | | Y | flowViewSpecified | [[DATA-MW-FLOW-DIAGRAM-VIEW-STATE]] | 有効なfrontmatter `flow_view` が指定されたか |
| flowViewRaw | Flow view raw値 | string | | N | flowViewRaw | [[DATA-MW-FLOW-DIAGRAM-VIEW-STATE]] | 不正値診断やinitializationKey生成に使う元値 |
| domainSources | Domain Sources | DomainSourceRef list | | Y | domainSources | [[DOMAINS-MW-ARCHITECTURE]] | `## Domain Sources` 由来 |
| domainSourceSummaries | Domain source summary | object | | N | domainSourceSummaries | | 外部Domains解決結果の表示用summary |
| domains | Local Domains | DomainEntry list | | N | domains | | local `## Domains`。同一IDはDomain Sourcesを上書きする |
| objectRefs | オブジェクト参照 | string list | | Y | objectRefs | | objectEntriesから生成 |
| objectEntries | オブジェクト群 | FlowDiagramObjectEntry list | | Y | objectEntries | [[DATA-MW-FLOW-DIAGRAM-OBJECT-ENTRY]] | `## Objects` 由来 |
| nodes | ノード群 | DiagramNode list | | Y | nodes | [[DATA-MW-RENDERER-GRAPH-NODE]] | Objects由来の解析時node |
| edges | エッジ群 | DiagramEdge list | | Y | edges | [[DATA-MW-RENDERER-GRAPH-EDGE]] | Flows由来の解析時edge |
| flows | フロー群 | FlowDiagramFlowEntry list | | Y | flows | [[DATA-MW-FLOW-DIAGRAM-FLOW-ENTRY]] | `## Flows` 由来 |
| warnings | warning | ValidationWarning list | | N | warnings | [[DATA-MW-CORE-DIAGNOSTIC]] | ParseResult.warnings |

## Notes

- Flow Diagramは既存の `dfd_diagram` とは別形式であり、古典的なDFDとして扱わない。
- v0.1.20ではDetail viewとScreen viewが実装済みである。任意projection定義、Communication view、汎用folding、transition coverage生成はfutureである。
- `Flows.from` / `Flows.to` はローカル `Objects.id` を参照する。外部モデル参照ではない。
- `Flows.data` は通常のラベルとして使える。Wikilinkの場合だけ参照解決と未解決診断の対象になる。
- `Objects.domain` はFlow Diagram内のgroupingとDomain color対象に使われる。
- `## Domain Sources` とlocal `## Domains` はFlow Diagram modelへ保持され、Renderer側のDomain group label、nested group、Color Scheme解決に使われる。
- Domain定義がある場合はunknown domainを診断する。Domain定義がない場合はraw `Objects.domain` によるsynthetic groupingを維持し、既存図を新しいunknown-domain warningで壊さない。

## Source Links

| path | notes |
|---|---|
| src/types/models.ts | symbol: FlowDiagramModel; kind: interface; Flow Diagram解析済みモデル |
| src/types/models.ts | symbol: DomainSourceRef; kind: interface; Flow Diagram Domain Sources |
| src/types/models.ts | symbol: DfdDiagramObjectEntry; kind: interface; Flow Diagram Objects行にも使われる |
| src/types/models.ts | symbol: DfdFlowModel; kind: interface; Flow Diagram Flows行にも使われる |
| src/parsers/dfd-diagram-parser.ts | symbol: parseFlowDiagramFile; kind: function; Flow Diagram parser |
| src/renderers/dfd-mermaid.ts | symbol: buildFlowDiagramMermaidSource; kind: function; Flow Diagram Mermaid source生成 |
