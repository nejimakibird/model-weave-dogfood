---
type: class
id: CLS-MW-RENDERER-RENDER-MODE-RESOLVER
name: RenderModeResolver
kind: class
package: model_weave.renderer
tags:
  - Renderer
  - Core
---

# RenderModeResolver

## Summary

プラグイン設定、モデルのフロントマター、およびツールオーバーライドの状態から、対象アセットに最適な描画モード（Custom / Mermaid / TableText）を決定します。

## Attributes

| name | type | visibility | static | notes |
|---|---|---|---|---|

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| resolveRenderMode | input: RenderModeInput | ResolvedRenderMode | public | Y | モード選択ロジックの実行 |
| getSupportedRenderModes | modelType: string | string | public | Y | implemented。modelType に応じた利用可能render mode群を返す。論理的には複数値 |

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/render-mode.ts | resolveRenderMode | function | 描画モードの解決 |
| src/core/render-mode.ts | getSupportedRenderModes | function | modelType別の対応render mode導出 |
