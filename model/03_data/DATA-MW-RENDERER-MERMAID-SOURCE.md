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
ResolvedDiagram や app_process Business Flow などから生成される派生出力であり、render mode解決ルールそのものは別モデルで扱います。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| sourceText | ソース全文 | string | | Y | source | | flowchart LR / classDiagram / erDiagram 等 |
| diagramType | Mermaid図種別 | string | | N | diagramType | | sourceText先頭行から分かる図種別 |
| renderMode | 描画モード | string | | N | renderMode | | mermaid または mermaid-detail |
| renderIdPrefix | render ID prefix | string | | N | renderIdPrefix | | Mermaid描画時のprefix |

## Notes

- 本データはモデルの正本（Source of Truth）ではなく、Markdownから生成される派生出力（Derived Output）です。
- current explicit render mode は mermaid / mermaid-detail であり、auto は選択済みrender modeとして扱わない。
- DFD Mermaid は buildDfdMermaidSource で flowchart LR を生成する。
- Class / ER Mermaid は overviewとdetailで出力形式が分かれ、detailは mermaid-detail に対応する。
- warningは ResolvedDiagram やParseResultなどの入力側データに保持され、本データの直接フィールドとしては扱わない。
- Markdownテーブル安全性のため、type列は単純型で表現します。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/renderers/dfd-mermaid.ts | buildDfdMermaidSource | function | DFD Mermaid source生成 |
| src/renderers/class-er-mermaid.ts | renderClassMermaidDiagram | function | class mermaid表示 |
| src/renderers/class-er-mermaid.ts | renderClassMermaidDetailDiagram | function | class mermaid-detail表示 |
| src/renderers/class-er-mermaid.ts | renderErMermaidDiagram | function | ER mermaid表示 |
| src/renderers/class-er-mermaid.ts | renderErMermaidDetailDiagram | function | ER mermaid-detail表示 |
| src/core/render-mode.ts | RenderMode | type | mermaid / mermaid-detail を含むrender mode |
