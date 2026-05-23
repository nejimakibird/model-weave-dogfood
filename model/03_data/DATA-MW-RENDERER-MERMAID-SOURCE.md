---
type: data_object
id: DATA-MW-RENDERER-MERMAID-SOURCE
name: Mermaidソース
kind: model
data_format: object
tags:
  - Renderer
  - Data
---

# Mermaidソース

## Summary

レンダラーによって生成された、Mermaid.jsエンジンで直接描画可能なテキストデータです。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| sourceText | ソース全文 | string | | Y | | | `graph LR ...` 等のテキスト |
| diagramType | 図面種別 | string | | Y | | | flowchart / erDiagram 等 |
| renderMode | 描画モード | string | | Y | | | auto / custom / mermaid |
| diagnostics | 生成時診断 | object | | Y | | [[DATA-MW-CORE-DIAGNOSTIC]] | 論理的には複数要素。生成過程での警告等 |

## Notes

- 本データはモデルの正本（Source of Truth）ではなく、Markdownから生成される派生出力（Derived Output）です。
- Markdownテーブル安全性のため、type列は単純型で表現し、複数性は notes / ref で表現します。
