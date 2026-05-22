---
type: data_object
id: DATA-MW-RENDERER-RENDER-MODE-INPUT
name: 描画モード解決用入力データ
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
| modelType | モデル種別 | CODE-MW-CORE-MODEL-TYPE | | Y | | [[CODE-MW-CORE-MODEL-TYPE]] | 対象アセットのtype |
| frontmatterRenderMode | フロントマター指定 | string | | N | | | frontmatter.render_mode |
| defaultRenderMode | 既定描画モード | string | | Y | | | プラグイン設定のグローバル既定値 |
| toolbarOverrideMode | ツールバー上書き | string | | N | | | Viewer上での一時的な選択状態 |
| supportedModes | サポートモード群 | string | | Y | | | getSupportedRenderModes の結果を入力として利用する。対象種別で実装済みのモードリスト。有効なモード名を保持する。論理的には複数値 |
| isDfd | DFDフラグ | boolean | | Y | | | DFD系モデルかどうかの判定結果 |

## Notes

- 本オブジェクトは解析フェーズ直後、レンダリングパイプラインの初期段階で構築されます。
- defaultRenderMode は設定由来の入力値であり、最終的な effective render mode は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に従う。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/core/render-mode.ts | ResolveRenderModeInput | interface | 実装上の入力型定義 |
| source/model-weave-repo/src/core/render-mode.ts | getSupportedRenderModes | function | supportedModes 入力の導出元 |
