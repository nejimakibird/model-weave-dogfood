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

`type: flow_diagram` のMarkdownを、Flow Diagram MVPの `FlowDiagramModel` へ解析する処理。
現行実装では `parseFlowDiagramFile` がDFD-like parserを共有しつつ、Flow Diagram専用の `Objects` / `Flows` headerと `kind: screen_communication` を検証する。

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
| parseObjects | parser_resolver | Objectsを解析する | process | markdownFile | parsedFlowDiagram | | | | id / label / kind / ref / domain / notes |
| parseFlows | parser_resolver | Flowsを解析する | process | markdownFile | parsedFlowDiagram | | | | id / from / to / kind / trigger / data / condition / notes |
| parseSourceLinks | source_links | Source Linksを解析する | process | markdownFile | parsedFlowDiagram | | | | `## Source Links` を保持する |
| end | model_storage | 解析結果を返す | end | parsedFlowDiagram | | | | | Vault Indexへ渡される |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | readFrontmatter | | 開始 | |
| readFrontmatter | validateKind | | kind確認 | |
| validateKind | parseObjects | | 続行 | warningがあっても解析を継続する |
| parseObjects | parseFlows | | Flows解析 | Objects header不正時は後段診断が抑制される |
| parseFlows | parseSourceLinks | | Source Links | |
| parseSourceLinks | end | | 完了 | |

## Notes

- Flow Diagramは `type: flow_diagram` として検出される。`dfd_diagram` ではない。
- MVPではInternal Detail Viewだけを扱う。Surface View、Communication View、folding、projection、transition coverage生成はfutureである。
- `Flows.from` / `Flows.to` はローカル `Objects.id` を参照する。

## Source Links

| path | notes |
|---|---|
| src/parsers/dfd-diagram-parser.ts | Steps: start, readFrontmatter, validateKind, parseObjects, parseFlows. parseFlowDiagramFile |
| src/parsers/source-links-parser.ts | Steps: parseSourceLinks. Source Links section解析 |
| src/types/models.ts | Steps: end. FlowDiagramModel / DfdDiagramObjectEntry / DfdFlowModel |
