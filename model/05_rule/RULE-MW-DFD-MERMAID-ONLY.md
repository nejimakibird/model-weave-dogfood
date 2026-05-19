---
type: rule
id: RULE-MW-DFD-MERMAID-ONLY
name: DFD Mermaid固定ルール
tags:
  - DFD
  - Renderer
---

# DFD Mermaid固定ルール

## Summary

V0.7以降の DFD（dfd_diagram）における描画エンジンの方針を定義します。DFDは高度な保守性と標準性を重視し、Mermaid.js を正本レンダラーとして扱います。

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
| source/model-weave-repo/src/renderers/dfd-mermaid.ts | buildDfdMermaidSource | function | DFD用ソース生成の起点 |