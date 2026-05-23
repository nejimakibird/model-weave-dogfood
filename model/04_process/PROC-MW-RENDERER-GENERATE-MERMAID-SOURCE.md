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

1. **ダイアグラム種別判定**: グラフモデルの `kind` に基づき、`flowchart` や `erDiagram` などのヘッダー行を決定する。
2. **スタイル定義生成**: 共通の `classDef` 行を生成し、ノード種別（external / process 等）に応じたスタイルを定義する。
3. **ノード行生成**: 各ノードを `id["label"]:::class` 形式の文字列に変換する。ラベル内の改行は `<br/>` に置換する。
4. **エッジ行生成**: 始点・終点 ID を結ぶ接続線（`-->` 等）を生成する。エッジラベルがある場合は `|label|` を挿入する。
5. **サニタイズ**: ラベル内の引用符、括弧、パイプ記号（`|`）を Mermaid 構文を破壊しないようにエスケープまたは置換する。

## Errors

- **E-GEN-001**: 非対応のダイアグラム種別

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/renderers/dfd-mermaid.ts | buildDfdMermaidSource | function | DFD用Mermaidソースの組み立て |
| src/renderers/class-er-mermaid.ts | buildClassOverviewMermaidSource | function | クラス図用Mermaidソースの組み立て |
