---
type: rule
id: RULE-MW-DFD-MERMAID-ONLY
name: DFD Mermaid固定ルール
kind: renderer_rule
tags:
  - DFD
  - Renderer
---

# DFD Mermaid固定ルール

## Summary

現行実装の DFD（dfd_diagram）における描画エンジンの方針を定義します。DFDはMermaid.js を固定レンダラーとして扱います。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| diagram | [[DATA-MW-DFD-PARSED-DIAGRAM]] | DFD parser | Y | dfd_diagram の解析結果 |
| renderMode | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | RenderModeResolver | Y | DFD の supportedModes 判定に使う |

## Conditions

- `modelType` が `dfd_diagram` である場合に適用する。

## Logic

1. **レンダラー固定**: 実効描画モード（effectiveMode）は常に `mermaid` とする。
2. **モード無視**: `render_mode: custom` や `render_mode: mermaid-detail` が指定された場合も、サポート外として `mermaid` にフォールバックさせ、Warning を出力する。
3. **legacy入力**: `auto` は現行のselected render modeではなく、旧frontmatter入力として扱い、診断とfallback対象にする。
4. **Color Scheme**: DFD objectは `target=dfd` とObjects.kind、Domain subgraphは `target=domain` とDomain kindで配色される。

## Notes

- DFD プレビューにおいては、ユーザーの混乱を避けるためツールバーの「Renderer selector」を表示対象外（または非活性）とします。
- Color Schemeが適用されても、DFDはFlow Connect Modeや汎用グラフ編集の対象にはならない。

## Source Links

| path | notes |
|---|---|
| src/renderers/dfd-mermaid.ts | symbol: buildDfdMermaidSource; kind: function; DFD用ソース生成の起点 |
| src/core/render-mode.ts | symbol: resolveRenderMode; kind: function; DFDのsupportedModesとfallback |
| src/core/color-scheme.ts | symbol: resolveColorStyle; kind: function; DFD object / Domain subgraphの配色解決 |
