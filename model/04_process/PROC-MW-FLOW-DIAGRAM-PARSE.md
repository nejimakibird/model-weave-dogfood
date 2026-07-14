---
type: app_process
id: PROC-MW-FLOW-DIAGRAM-PARSE
name: Flow Diagram解析
process_type: parser
tags:
  - ModelWeave
  - FlowDiagram
  - Parser
  - BusinessFlow
---

# Flow Diagram解析

## Summary

`type: flow_diagram` のMarkdownを、Flow Diagramの `FlowDiagramModel` へ解析する処理。
現行実装では `parseFlowDiagramFile` がDFD-like parserを共有しつつ、Flow Diagram専用の `Objects` / `Flows`、`Domain Sources`、local `Domains`、frontmatter `flow_view` を解析する。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| markdownFile | [[DATA-MW-CORE-VAULT-FILE]] | [[PROC-MW-PARSER-PARSE-VAULT-FILE]] | Y | frontmatter typeがflow_diagramのMarkdown |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| parsedFlowDiagram | [[DATA-MW-FLOW-DIAGRAM-PARSED-DIAGRAM]] | [[DFD-MW-OBJ-PARSER]] | FlowDiagramModel |
| warnings | [[DATA-MW-CORE-DIAGNOSTIC]] | Preview diagnostics | header / kind / row構造のwarning |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | parser_resolver | Flow Diagram解析を開始する | start | markdownFile | | | | | parseFlowDiagramFile |
| readFrontmatter | parser_resolver | frontmatterを読む | process | markdownFile | | | | | typeとkindを確認する |
| validateKind | parser_resolver | kindを検証する | decision | markdownFile | warnings | | | | `screen_communication` 以外はwarning |
| parseFlowView | parser_resolver | flow_viewを解析する | process | markdownFile | parsedFlowDiagram, warnings | [[RULE-MW-FLOW-DIAGRAM-VIEW-STATE]] | | | detail / screen。invalid値はwarning |
| parseDomainSources | parser_resolver | Domain Sourcesを解析する | process | markdownFile | parsedFlowDiagram, warnings | | | | external Domains参照 |
| parseLocalDomains | parser_resolver | local Domainsを解析する | process | markdownFile | parsedFlowDiagram, warnings | | | | local定義は同一IDを上書きする |
| parseObjects | parser_resolver | Objectsを解析する | process | markdownFile | parsedFlowDiagram | | | | id / label / kind / ref / domain / notes |
| parseFlows | parser_resolver | Flowsを解析する | process | markdownFile | parsedFlowDiagram | | | | id / from / to / kind / trigger / data / condition / notes |
| parseSourceLinks | source_links | Source Linksを解析する | process | markdownFile | parsedFlowDiagram | | | | `## Source Links` を保持する |
| end | model_storage | 解析結果を返す | end | parsedFlowDiagram | | | | | Vault Indexへ渡される |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | readFrontmatter | | 開始 | |
| readFrontmatter | validateKind | | kind確認 | |
| validateKind | parseFlowView | | flow_view | |
| parseFlowView | parseDomainSources | | Domain Sources | |
| parseDomainSources | parseLocalDomains | | local Domains | |
| parseLocalDomains | parseObjects | | Objects | warningがあっても解析を継続する |
| parseObjects | parseFlows | | Flows解析 | Objects header不正時は後段診断が抑制される |
| parseFlows | parseSourceLinks | | Source Links | |
| parseSourceLinks | end | | 完了 | |

## Notes

- Flow Diagramは `type: flow_diagram` として検出される。`dfd_diagram` ではない。
- Detail / Screen viewはv0.1.20で実装済みである。任意projection定義、Communication view、generic folding、transition coverage生成はfutureである。
- `Flows.from` / `Flows.to` はローカル `Objects.id` を参照する。
- `flow_view` は初期表示入力であり、toolbar切替後のViewer stateやMarkdown正本を固定しない。
- `## Domain Sources` とlocal `## Domains` はFlow Diagram modelへ保持され、RendererでDomain name、parent、kindを使う。

## Source Links

| path | notes |
|---|---|
| src/parsers/dfd-diagram-parser.ts | Steps: start, readFrontmatter, validateKind, parseObjects, parseFlows. parseFlowDiagramFile |
| src/parsers/dfd-diagram-parser.ts | Steps: parseFlowView, parseDomainSources, parseLocalDomains. flow_view and domain parsing |
| src/parsers/source-links-parser.ts | Steps: parseSourceLinks. Source Links section解析 |
| src/types/models.ts | Steps: end. FlowDiagramModel / DfdDiagramObjectEntry / DfdFlowModel |
