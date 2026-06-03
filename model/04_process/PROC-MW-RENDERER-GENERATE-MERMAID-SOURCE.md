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

| id | lane | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | Renderer | Mermaidソース生成開始 | start | graph |  |  |  |  | ResolvedDiagram を受け取る |
| chooseGenerator | Renderer | 生成関数を選択する | decision | graph |  |  |  |  | DFD / Class / ER / objectで分岐する |
| initLines | Renderer | ヘッダー行を作る | process | graph | sourceLines |  |  |  | flowchart / classDiagram / erDiagram |
| addStyles | Renderer | 必要なstyle行を追加する | process | sourceLines | sourceLines |  |  |  | classDef と palette を使う |
| mapNodes | Renderer | node IDを割り当てる | process | graph | nodeMap |  |  |  | sanitizeMermaidId 等を使う |
| emitNodes | Renderer | node行を生成する | process | nodeMap | sourceLines |  |  |  | labelをMermaid用に整形する |
| emitEdges | Renderer | edge行を生成する | process | graph | sourceLines |  |  |  | source / target を接続する |
| hasEdgeLabel | Renderer | edge label有無を判定する | decision | graph |  |  |  |  | labelがあれば edge に出す |
| addLabeledEdge | Renderer | label付きedgeを追加する | process | sourceLines | sourceLines |  |  |  | edge labelをsanitizeする |
| addPlainEdge | Renderer | labelなしedgeを追加する | process | sourceLines | sourceLines |  |  |  | 通常の接続線を出す |
| joinSource | Renderer | Mermaidソースを結合する | process | sourceLines | source |  |  |  | linesを改行で結合する |
| end | Renderer | Mermaidソース生成完了 | end | source |  |  |  |  | Mermaid Rendererへ渡す |

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

| path | symbol | kind | notes |
|---|---|---|---|
| src/renderers/dfd-mermaid.ts | buildDfdMermaidSource | function | DFD用Mermaidソースの組み立て |
| src/renderers/class-er-mermaid.ts | buildClassOverviewMermaidSource | function | クラス図用Mermaidソースの組み立て |
| src/renderers/class-er-mermaid.ts | buildClassDetailMermaidSource | function | クラス詳細Mermaidソースの組み立て |
| src/renderers/class-er-mermaid.ts | buildErOverviewMermaidSource | function | ER概要Mermaidソースの組み立て |
| src/renderers/class-er-mermaid.ts | buildErDetailMermaidSource | function | ER詳細Mermaidソースの組み立て |
| src/renderers/mermaid-helpers.ts | sanitizeMermaidId | function | Mermaid ID整形 |
| src/renderers/mermaid-helpers.ts | escapeMermaidLabel | function | Mermaid label整形 |
