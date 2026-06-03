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

V0.7以降の DFD（dfd_diagram）における描画エンジンの方針を定義します。DFDは高度な保守性と標準性を重視し、Mermaid.js を正本レンダラーとして扱います。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| diagram | [[DATA-MW-DFD-PARSED-DIAGRAM]] | DFD parser | Y | dfd_diagram の解析結果 |
| renderMode | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | RenderModeResolver | Y | DFD の supportedModes 判定に使う |

## Conditions

- `modelType` が `dfd_diagram` である場合に適用する。

## Logic

1. **レンダラー固定**: 実効描画モード（effectiveMode）は常に `mermaid` とする。
2. **モード無視**: `render_mode: custom` が指定された場合も、サポート外として `mermaid` にフォールバックさせ、Warning を出力する。
3. **自動解決**: `auto` 指定時は無条件で `mermaid` を選択する。

## Notes

- DFD プレビューにおいては、ユーザーの混乱を避けるためツールバーの「Renderer selector」を表示対象外（または非活性）とします。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/renderers/dfd-mermaid.ts | buildDfdMermaidSource | function | DFD用ソース生成の起点 |
