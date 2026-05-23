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
| input | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | RenderModeResolver | Y | ResolveRenderModeInput と dogfood上の派生情報を含む判定入力 |

## Logic

1. **最優先 (Toolbar Override)**: `toolbarOverride` が有効な render mode（auto / custom / mermaid）であれば採用する。
2. **次優先 (Frontmatter)**: `frontmatterRenderMode` が有効な render mode であれば採用する。不正値の場合は診断を出し、後続候補へフォールバックする。
3. **第三優先 (Plugin Settings)**: `settingsDefaultRenderMode` が有効な render mode であれば採用する。
4. **未指定時 (Format Default)**: 上記が未指定または無効の場合、選択値は `auto` とし、`formatType / modelKind` に基づく format default へ委譲する。
    - `dfd-diagram`: `mermaid`
    - その他: 原則 `custom`
5. **auto 解決**: `auto` は実レンダラーではなく、`formatType / modelKind` に基づき `effectiveMode` を `custom` または `mermaid` へ解決する。
6. **サポート検証**: `custom / mermaid` が明示指定された場合、`formatType / modelKind` から導出されるサポート集合に含まれるか確認する。未対応の場合は診断または警告を出し、安全なmodeへフォールバックする。
7. **DFD 例外**: `dfd-diagram` は Mermaid-first / mermaid固定方針とし、`custom` 指定は `mermaid` へフォールバックする。詳細は [[RULE-MW-DFD-MERMAID-ONLY]] に委ねる。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/render-mode.ts | ResolveRenderModeInput | interface | 実装側の入力フィールド定義 |
| src/core/render-mode.ts | resolveRenderMode | function | 優先順位に基づいた解決実装 |
| src/core/render-mode.ts | getSupportedRenderModes | function | formatType / modelKind から supportedModes を導出する実装 |

## Notes
- 現行実装側の正は src/core/render-mode.ts の resolveRenderMode / getSupportedRenderModes である。
- この rule は現行実装に近いが、dogfood上の説明・派生情報を含むため partial と扱う。
- dogfood上の旧概念名 toolbarOverrideMode / defaultRenderMode / modelType は、それぞれ toolbarOverride / settingsDefaultRenderMode / formatType + modelKind を優先して読み替える。
- supportedModes は getSupportedRenderModes(formatType, modelKind) により導出される派生情報であり、ResolveRenderModeInput の生入力ではない。
- isDfd は formatType / modelKind からの派生判定であり、ResolveRenderModeInput の生入力ではない。
- class / er_entity の単体表示は custom を既定とする。
- class_diagram / er_diagram は対応状況に応じて custom / mermaid を選択可能とする。
- DFD は [[RULE-MW-DFD-MERMAID-ONLY]] に従い Mermaid-first / mermaid固定方針で扱う。
