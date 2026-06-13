---
type: app_process
id: PROC-MW-RENDERER-GENERATE-MERMAID-SOURCE
name: Mermaidソース生成
process_type: transform
kind: renderer
tags:
  - Renderer
---

# Mermaidソース生成

## Summary

`DATA-MW-RENDERER-GRAPH-MODEL` を、Mermaid.js エンジンで描画可能なテキストソース（DSL）に変換します。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Model Weave architecture domains |

## Triggers

| id | kind | source | event | notes |
|---|---|---|---|---|
| T1 | system | Build Graph | 構築完了時 | |

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| graph | [[DATA-MW-RENDERER-GRAPH-MODEL]] | Graph Builder | Y | 抽象グラフモデル |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| source | [[DATA-MW-RENDERER-MERMAID-SOURCE]] | Mermaid Renderer | Mermaidソース |

## Transitions

| id | event | to | condition | notes |
|---|---|---|---|---|
| TR1 | 生成完了 | - | | Mermaidエンジンにテキストを渡す |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | renderer_area | Mermaidソース生成開始 | start | graph |  |  |  |  | ResolvedDiagram を受け取る |
| chooseGenerator | renderer_area | 生成関数を選択する | decision | graph |  |  |  |  | DFD / Class / ER / objectで分岐する |
| initLines | renderer_area | ヘッダー行を作る | process | graph | sourceLines |  |  |  | flowchart / classDiagram / erDiagram |
| addStyles | renderer_area | 必要なstyle行を追加する | process | sourceLines | sourceLines |  |  |  | classDef と palette を使う |
| mapNodes | renderer_area | node IDを割り当てる | process | graph | nodeMap |  |  |  | sanitizeMermaidId 等を使う |
| emitNodes | renderer_area | node行を生成する | process | nodeMap | sourceLines |  |  |  | labelをMermaid用に整形する |
| emitEdges | renderer_area | edge行を生成する | process | graph | sourceLines |  |  |  | source / target を接続する |
| hasEdgeLabel | renderer_area | edge label有無を判定する | decision | graph |  |  |  |  | labelがあれば edge に出す |
| addLabeledEdge | renderer_area | label付きedgeを追加する | process | sourceLines | sourceLines |  |  |  | edge labelをsanitizeする |
| addPlainEdge | renderer_area | labelなしedgeを追加する | process | sourceLines | sourceLines |  |  |  | 通常の接続線を出す |
| joinSource | renderer_area | Mermaidソースを結合する | process | sourceLines | source |  |  |  | linesを改行で結合する |
| end | renderer_area | Mermaidソース生成完了 | end | source |  |  |  |  | Mermaid Rendererへ渡す |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| chooseGenerator | initLines | `diagram.kind === "dfd"` | DFD | buildDfdMermaidSource |
| chooseGenerator | initLines | `renderMode === "mermaid"` | overview | overview生成 |
| chooseGenerator | initLines | `renderMode === "mermaid-detail"` | detail | detail生成 |
| hasEdgeLabel | addLabeledEdge | `edge.label` | labeled | label付きedge |
| hasEdgeLabel | addPlainEdge | `!edge.label` | plain | labelなしedge |
| addLabeledEdge | joinSource |  | join | sourceへ結合する |
| addPlainEdge | joinSource |  | join | sourceへ結合する |

## Errors

- **E-GEN-001**: 対応する Mermaid 生成関数がない場合

## Notes

- DFD は buildDfdMermaidSource で flowchart LR を生成する。
- Class / ER の overview は flowchart LR を生成する。
- Class detail は classDiagram、ER detail は erDiagram を生成する。
- detail系では classDef を出さない生成経路がある。
- node ID と label は Mermaid 構文を壊さないように整形される。
- edge の endpoint が nodeMap にない場合、そのedge行は出力されない。

## Source Links

| path | notes |
|---|---|
| src/renderers/dfd-mermaid.ts | Steps: chooseGenerator, initLines, addStyles, mapNodes, emitNodes, emitEdges. buildDfdMermaidSource がDFD用Mermaid sourceを組み立てる |
| src/renderers/class-er-mermaid.ts | Steps: chooseGenerator, initLines, emitNodes, emitEdges. Class / ER overview and detail Mermaid sourceを生成する |
| src/renderers/mermaid-helpers.ts | Steps: mapNodes, emitNodes. sanitizeMermaidId / escapeMermaidLabel がMermaid IDとlabelを整形する |
