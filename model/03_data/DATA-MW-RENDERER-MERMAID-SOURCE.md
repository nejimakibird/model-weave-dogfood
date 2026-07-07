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
| colorStyleLines | Color Scheme style行 | string list | | N | source | | DFD / Business Flow / Weave Mapで生成されるclassDefまたはstyle行 |
| businessFlowStepShapeLines | Business Flow Step node行 | string list | | N | source | [[DATA-MW-APP-PROCESS-STEP]] | app_process Steps.kindからshapeを選んだMermaid node行 |
| renderIdPrefix | render ID prefix | string | | N | renderIdPrefix | | Mermaid描画時のprefix |

## Notes

- 本データはモデルの正本（Source of Truth）ではなく、Markdownから生成される派生出力（Derived Output）です。
- current explicit render mode は mermaid / mermaid-detail であり、auto は選択済みrender modeとして扱わない。
- DFD Mermaid は buildDfdMermaidSource で flowchart LR を生成する。
- DFD / Business Flow / Weave Mapでは、解決済みColor SchemeがMermaid source内のstyle行やclassDefとして反映される場合がある。
- app_process Business Flowでは、`Steps.kind` からStep node shapeを選び、Color Schemeがある場合は `target=app_process` と `kind=<Steps.kind>` でStep色を解決する。
- Color Scheme反映済みMermaid sourceは派生出力であり、元のMarkdownモデル本文やDomain定義を書き換えない。
- Applied Color Scheme sectionはViewer側の表示であり、Mermaid source本文には含めない。
- PNG exportで色が保持される場合も、Mermaid sourceまたは描画済みSVGからの派生出力であり、Markdown正本の変更ではない。
- Class / ER Mermaid は overviewとdetailで出力形式が分かれ、detailは mermaid-detail に対応する。
- warningは ResolvedDiagram やParseResultなどの入力側データに保持され、本データの直接フィールドとしては扱わない。
- Markdownテーブル安全性のため、type列は単純型で表現します。

## Source Links

| path | notes |
|---|---|
| src/renderers/dfd-mermaid.ts | symbol: buildDfdMermaidSource; kind: function; DFD Mermaid source生成 |
| src/renderers/app-process-business-flow.ts | symbol: buildAppProcessBusinessFlowMermaidSource; kind: function; Business Flow Mermaid source生成とColor Scheme適用 |
| src/renderers/app-process-business-flow.ts | symbol: getStepShapeKind; kind: function; Business Flow Step kindからshape種別を選ぶ |
| src/renderers/app-process-business-flow.ts | symbol: formatAppProcessStepNode; kind: function; Business Flow Step node行を生成する |
| src/renderers/weave-map-mermaid.ts | symbol: buildWeaveMapMermaidSource; kind: function; Weave Map Mermaid source生成とColor Scheme適用 |
| src/core/color-scheme.ts | symbol: resolveColorStyle; kind: function; target / kindからColor Scheme styleを解決 |
| src/views/applied-color-scheme-renderer.ts | symbol: renderAppliedColorSchemeSectionContent; kind: function; Applied Color Scheme sectionはViewer表示として扱う |
| src/export/png-export.ts | symbol: exportDiagramRenderableAsPng; kind: function; 描画済み図をPNGとして出力する |
| src/renderers/class-er-mermaid.ts | symbol: renderClassMermaidDiagram; kind: function; class mermaid表示 |
| src/renderers/class-er-mermaid.ts | symbol: renderClassMermaidDetailDiagram; kind: function; class mermaid-detail表示 |
| src/renderers/class-er-mermaid.ts | symbol: renderErMermaidDiagram; kind: function; ER mermaid表示 |
| src/renderers/class-er-mermaid.ts | symbol: renderErMermaidDetailDiagram; kind: function; ER mermaid-detail表示 |
| src/core/render-mode.ts | symbol: RenderMode; kind: type; mermaid / mermaid-detail を含むrender mode |
