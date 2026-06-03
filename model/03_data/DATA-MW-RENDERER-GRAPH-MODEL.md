---
type: data_object
id: DATA-MW-RENDERER-GRAPH-MODEL
name: 抽象グラフモデル
kind: model
data_format: object
tags:
  - Renderer
  - Data
---

# 抽象グラフモデル

## Summary

特定の描画エンジン（Mermaid等）に依存しない、解決済み図面データの論理構造です。
現行ソースでは ResolvedDiagram に対応し、元の diagram、解決済み nodes / edges、未解決オブジェクト、warningを保持します。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| diagram | 元図面モデル | object | | Y | diagram | [[DATA-MW-CORE-PARSED-MODEL]] | DiagramModelまたはDfdDiagramModel |
| nodes | 解決済みノード | DiagramNode list | | Y | nodes | [[DATA-MW-RENDERER-GRAPH-NODE]] | ResolvedDiagram.nodes |
| edges | 解決済みエッジ | DiagramEdge list | | Y | edges | [[DATA-MW-RENDERER-GRAPH-EDGE]] | ResolvedDiagram.edges |
| missingObjects | 未解決オブジェクト | string list | | Y | missingObjects | | 解決できなかったobject ref |
| warnings | 解決warning | ValidationWarning list | | Y | warnings | [[DATA-MW-CORE-DIAGNOSTIC]] | 解決時のwarning |

## Notes

- 本データは resolveDiagramRelations の戻り値である ResolvedDiagram のdogfood表現である。
- labels / shape / sourceRefs は現行 ResolvedDiagram の直接フィールドではないため、現在のフィールド一覧には含めない。
- warnings は診断パネルやPreview表示で使われる ValidationWarning list であり、旧称 diagnostics では扱わない。
- Mermaid source は別データモデルで扱い、本モデルには含めない。
- Markdownテーブル安全性のため、複数性は list 表記で表現する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/types/models.ts | ResolvedDiagram | interface | 解決済み図面データ |
| src/types/models.ts | DiagramNode | interface | nodes要素 |
| src/types/models.ts | DiagramEdge | interface | edges要素 |
| src/core/relation-resolver.ts | resolveDiagramRelations | function | ResolvedDiagramを生成 |
