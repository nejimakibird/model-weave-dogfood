---
type: rule
id: RULE-MW-RENDERER-RENDER-MODE-RESOLUTION
name: 描画モード解決ルール
tags:
  - Renderer
  - Core
---

# 描画モード解決ルール

## Summary

複数の指定箇所（設定、ファイル、UI）における描画モード指定の優先順位と、サポート状況に基づくフォールバックロジックを定義します。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| input | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | RenderModeResolver | Y | 判定に使用する全パラメータ |

## Logic

1. **最優先 (Toolbar Override)**: `toolbarOverrideMode` が存在する場合は、その値を採用する。
2. **次優先 (Frontmatter)**: `frontmatterRenderMode` が有効な値（custom / mermaid）であれば採用する。
3. **第三優先 (Plugin Settings)**: グローバル設定の `defaultRenderMode` が `auto` 以外であれば採用する。
4. **最終解決 (Format Default)**: 上記がすべて `auto` または未指定の場合、`modelType` ごとの標準モードを適用する。
    - `dfd_diagram`: `mermaid`
    - `class / er_entity` 等: `custom`
5. **サポート検証**: 決定されたモードが `supportedModes` に含まれない場合、警告を出して種別ごとのフォールバック先（原則 `custom`）へ解決する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/core/render-mode.ts | resolveRenderMode | function | 優先順位に基づいた解決実装 |

## Notes
- class / er_entity の単体表示は custom を既定とする。
- class_diagram / er_diagram は対応状況に応じて custom / mermaid を選択可能とする。
- DFD は RULE-MW-DFD-MERMAID-ONLY に従い mermaid 固定とする。