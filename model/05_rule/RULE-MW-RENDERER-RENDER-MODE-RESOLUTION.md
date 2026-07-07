---
type: rule
id: RULE-MW-RENDERER-RENDER-MODE-RESOLUTION
name: 描画モード解決ルール
kind: renderer_rule
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

## Conditions

| id | expression | severity | message | notes |
|---|---|---|---|---|
| C1 | toolbarOverride が supportedModes に含まれる | info | toolbar override を採用 | 最優先 |
| C2 | frontmatterRenderMode が supportedModes に含まれる | info | frontmatter render_mode を採用 | 第二優先 |
| C3 | frontmatterRenderMode が `auto` | warning | deprecated値としてfallback | `auto` は selectedMode ではない |
| C4 | frontmatterRenderMode が不正または非対応 | warning | format defaultへfallback | diagnostics を返す |
| C5 | settingsDefaultRenderMode が supportedModes に含まれる | info | settings default を採用 | 第三優先 |
| C6 | format default が非対応 | warning | fallback mode を採用 | 最終fallback |

## Logic

1. **最優先 (Toolbar Override)**: `toolbarOverride` が有効な明示 render mode（`custom` / `mermaid` / `mermaid-detail`）で、対象形式の supportedModes に含まれる場合は採用する。
2. **次優先 (Frontmatter)**: `frontmatterRenderMode` が有効な明示 render mode（`custom` / `mermaid` / `mermaid-detail`）で、対象形式の supportedModes に含まれる場合は採用する。不正値、非対応値、または deprecated な `auto` の場合は診断を出し、後続候補へフォールバックする。
3. **第三優先 (Plugin Settings)**: `settingsDefaultRenderMode` が有効な明示 render mode（`custom` / `mermaid` / `mermaid-detail`）で、対象形式の supportedModes に含まれる場合は採用する。
4. **未指定時 (Format Default / Fallback)**: 上記が未指定または無効の場合、`formatType / modelKind` に基づく format default を試し、未対応なら fallback mode を採用する。
    - `dfd-diagram`: `mermaid`
    - その他: 原則 `custom`
5. **Deprecated auto**: `auto` は有効な selectedMode ではない。frontmatter.render_mode に `auto` が指定された場合のみ deprecated 値として診断を出し、format default へフォールバックする。
6. **サポート検証**: `custom` / `mermaid` / `mermaid-detail` が明示指定された場合、`formatType / modelKind` から導出される supportedModes に含まれるか確認する。未対応の場合は診断またはフォールバックを適用する。
7. **固定レンダリング形式**: `data-object` / `app-process` / `rule` / `codeset` / `message` / `mapping` は selectable render mode を持たず、実装上は table-text renderer を使う。`dfd-object` / `screen` / `markdown` も selectable render mode を持たず、固定または対象外の表示挙動に委ねる。
8. **DFD 例外**: `dfd-diagram` は `mermaid` 固定方針とし、`custom` / `mermaid-detail` 指定は supportedModes に含まれないため採用されない。詳細は [[RULE-MW-DFD-MERMAID-ONLY]] に委ねる。

## Source Links

| path | notes |
|---|---|
| src/core/render-mode.ts | symbol: ResolveRenderModeInput; kind: interface; 実装側の入力フィールド定義 |
| src/core/render-mode.ts | symbol: resolveRenderMode; kind: function; 優先順位に基づいた解決実装 |
| src/core/render-mode.ts | symbol: getSupportedRenderModes; kind: function; formatType / modelKind から supportedModes を導出する実装 |

## Notes
- 現行実装側の正は src/core/render-mode.ts の resolveRenderMode / getSupportedRenderModes である。
- この rule は現行実装に近いが、dogfood上の説明・派生情報を含むため partial と扱う。
- dogfood上の旧概念名 toolbarOverrideMode / defaultRenderMode / modelType は、それぞれ toolbarOverride / settingsDefaultRenderMode / formatType + modelKind を優先して読み替える。
- supportedModes は getSupportedRenderModes(formatType, modelKind) により導出される派生情報であり、ResolveRenderModeInput の生入力ではない。
- isDfd は formatType / modelKind からの派生判定であり、ResolveRenderModeInput の生入力ではない。
- class / er_entity の単体表示は custom を既定とする。
- class / er_entity / class_diagram / er_diagram は対応状況に応じて custom / mermaid / mermaid-detail を選択可能とする。
- `auto` は有効な RenderMode ではなく、frontmatter.render_mode の deprecated 値としてのみ扱う。
- DFD diagram は [[RULE-MW-DFD-MERMAID-ONLY]] に従い mermaid固定方針で扱う。
