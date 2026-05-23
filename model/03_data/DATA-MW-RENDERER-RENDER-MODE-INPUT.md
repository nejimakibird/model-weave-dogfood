---
type: data_object
id: DATA-MW-RENDERER-RENDER-MODE-INPUT
name: 描画モード解決用入力データ
kind: input
data_format: object
tags:
  - Renderer
  - Core
---

# 描画モード解決用入力データ

## Summary

`RenderModeResolver` が「実効描画モード（effectiveMode）」を導出するために必要な情報を集約したデータオブジェクトです。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| filePath | ファイルパス | string | | Y | filePath | | implemented。ResolveRenderModeInput の入力フィールド。診断生成時の対象ファイル |
| formatType | フォーマット種別 | string | | Y | formatType | [[CODE-MW-CORE-MODEL-TYPE]] | implemented。ResolveRenderModeInput の入力フィールド。FileType値 |
| modelKind | モデルkind | string | | N | modelKind | | implemented。ResolveRenderModeInput の入力フィールド。frontmatter kind 等 |
| toolbarOverride | ツールバー上書き | string | | N | toolbarOverride | | implemented。ResolveRenderModeInput の入力フィールド。旧 toolbarOverrideMode に対応 |
| frontmatterRenderMode | フロントマター指定 | string | | N | frontmatterRenderMode | | implemented。ResolveRenderModeInput の入力フィールド。frontmatter.render_mode |
| settingsDefaultRenderMode | 設定既定描画モード | string | | N | settingsDefaultRenderMode | | implemented。ResolveRenderModeInput の入力フィールド。旧 defaultRenderMode に対応 |
| modelType | モデル種別 | string | | N | | [[CODE-MW-CORE-MODEL-TYPE]] | 旧概念名または論理名。実装側では formatType / modelKind へ分解して扱う |
| defaultRenderMode | 既定描画モード | string | | N | | | 旧概念名。実装側の settingsDefaultRenderMode に対応 |
| toolbarOverrideMode | ツールバー上書き | string | | N | | | 旧概念名。実装側の toolbarOverride に対応 |
| supportedModes | サポートモード群 | string | | N | | | 派生情報。getSupportedRenderModes(formatType, modelKind) により導出される。実装入力そのものではない。論理的には複数値 |
| isDfd | DFDフラグ | boolean | | N | | | 派生判定。formatType / modelKind から判定される。実装入力そのものではない |

## Notes

- 本オブジェクトは解析フェーズ直後、レンダリングパイプラインの初期段階で構築されます。
- 現行実装側の入力は src/core/render-mode.ts の ResolveRenderModeInput を正とする。
- この data_object は実装入力とdogfood上の派生情報を含むため、全体としては partial と扱う。
- dogfood上の旧概念名と実装側フィールド名が異なる場合は、実装側フィールド名を優先する。
- defaultRenderMode は旧概念名であり、実装側では settingsDefaultRenderMode として扱う。
- toolbarOverrideMode は旧概念名であり、実装側では toolbarOverride として扱う。
- supportedModes は getSupportedRenderModes により formatType / modelKind から導出される。
- isDfd は formatType / modelKind に基づく派生判定である。
- 最終的な effective render mode は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に従う。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/render-mode.ts | ResolveRenderModeInput | interface | 実装上の入力型定義 |
| src/core/render-mode.ts | resolveRenderMode | function | 描画モードの解決 |
| src/core/render-mode.ts | getSupportedRenderModes | function | supportedModes 入力の導出元 |
