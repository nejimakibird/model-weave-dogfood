---
type: data_object
id: DATA-MW-DFD-PARSED-DIAGRAM
name: DFD解析済みダイアグラム
tags:
  - DFD
  - Data
---

# DFD解析済みダイアグラム

## Summary

dfd_diagram Markdownから解析されたDFD専用の論理データ構造です。
汎用の DATA-MW-CORE-PARSED-MODEL から、DFDレンダリングに必要な構造を取り出したものであり、Mermaidや描画エンジンには依存しません。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| id | ID | string | | Y | | | 原典dfd_diagramのID |
| name | 名称 | string | | Y | | | 原典dfd_diagramの名称 |
| level | 階層 | Optional<number> | | N | | | DFD階層 |
| objectEntries | オブジェクト群 | Array<DATA-MW-DFD-OBJECT-ENTRY> | | Y | | [[DATA-MW-DFD-OBJECT-ENTRY]] | dfd_diagram.Objects由来 |
| flows | フロー群 | Array<DATA-MW-DFD-FLOW-ENTRY> | | Y | | [[DATA-MW-DFD-FLOW-ENTRY]] | dfd_diagram.Flows由来 |
| diagnostics | 診断情報 | Array<DATA-MW-CORE-DIAGNOSTIC> | | Y | | [[DATA-MW-CORE-DIAGNOSTIC]] | 解析・解決時の診断情報 |

## Notes

- DATA-MW-CORE-PARSED-MODEL は汎用解析済みモデルとして残します。
- 本オブジェクトはDFD専用の中間表現であり、描画モデル（Graph Model）生成前の論理データを表します。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/types/models.ts | DfdDiagramModel | type | 実装上のDFDモデル型に対応 |