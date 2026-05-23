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
汎用の DATA-MW-CORE-PARSED-MODEL から、DFDレンダリングに必要な構造を取り出したものであり、Mermaidや描画エンジンには依存しません。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| id | ID | string | | Y | | | 原典dfd_diagramのID |
| name | 名称 | string | | Y | | | 原典dfd_diagramの名称 |
| level | 階層 | number | | N | | | 任意項目。DFD階層。未設定の場合あり |
| objectEntries | オブジェクト群 | object | | Y | | [[DATA-MW-DFD-OBJECT-ENTRY]] | 論理的には複数要素。dfd_diagram.Objects由来 |
| flows | フロー群 | object | | Y | | [[DATA-MW-DFD-FLOW-ENTRY]] | 論理的には複数要素。dfd_diagram.Flows由来 |
| diagnostics | 診断情報 | object | | Y | | [[DATA-MW-CORE-DIAGNOSTIC]] | 論理的には複数要素。解析・解決時の診断情報 |

## Notes

- DATA-MW-CORE-PARSED-MODEL は汎用解析済みモデルとして残します。
- 本オブジェクトはDFD専用の中間表現であり、描画モデル（Graph Model）生成前の論理データを表します。
- Markdownテーブル安全性のため、type列は単純型で表現し、複数性や任意性は required / notes / ref で表現します。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/types/models.ts | DfdDiagramModel | type | 実装上のDFDモデル型に対応 |
