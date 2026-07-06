---
type: rule
id: RULE-MW-VIEWER-TOOLBAR-VISIBILITY
name: Viewerツールバー表示制御ルール
kind: display_rule
tags:
  - Viewer
  - UI
  - Rule
---

# Viewerツールバー表示制御ルール

## Summary

Model Weave Viewer上部ツールバーに表示するコントロールの表示、非表示、有効、無効の判定方針を定義します。

対象は `fitButton`、`zoomOutButton`、`zoomInButton`、`resetZoomButton`、`renderModeSelector` です。

表示制御は、現在のViewer状態、対象モデル種別、サポートされる描画モード、DFD Mermaid-only 方針に基づきます。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| viewerState | [[DATA-MW-VIEWER-STATE]] | Viewer | Y | 現在の表示対象、描画状態、診断状態 |
| renderModeInput | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | Renderer | Y | 描画モード解決に必要な入力 |
| mainFrame | [[SCR-MW-VIEWER-MAIN-FRAME]] | Viewer UI | Y | ツールバーを含むメイン画面 |
| renderModeRule | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | Rule | Y | 実効描画モードの解決方針 |
| dfdMermaidOnlyRule | [[RULE-MW-DFD-MERMAID-ONLY]] | Rule | N | DFDのMermaid固定方針 |

## References

| ref | usage | notes |
|---|---|---|
| [[SCR-MW-VIEWER-MAIN-FRAME]] | target_screen | ツールバーを含むViewer全体 |
| [[DATA-MW-VIEWER-STATE]] | state_source | ツールバー表示判定の状態入力 |
| [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | related_rule | renderModeSelector の候補と実効値 |
| [[RULE-MW-DFD-MERMAID-ONLY]] | related_rule | DFDではレンダラー切替を行わない |

## Conditions

- `fitButton`、`zoomOutButton`、`zoomInButton`、`resetZoomButton` は、`previewCanvas` に描画可能な内容がある場合に表示します。
- `renderStatus` が `empty`、`unsupported`、または `error` の場合、ズーム系ボタンは非表示または無効化します。
- `renderModeSelector` は、`supportedRenderModes` が2件以上あり、ユーザーが切り替える意味のある形式でのみ表示します。
- DFDの場合、`[[RULE-MW-DFD-MERMAID-ONLY]]` に従い `renderModeSelector` は表示しない、または非活性にします。
- table/text-only形式では、`renderModeSelector` は表示しません。
- `renderModeSelector` による toolbar override は一時的なUI状態であり、Markdown本文やfrontmatterを書き換えません。
- `renderModeSelector` の候補値と実効値は、`[[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]]` に従います。

## Messages

| condition | message | severity | notes |
|---|---|---|---|
| dfd renderer fixed | DFD diagrams are rendered using Mermaid only. | info | DFDでレンダラー選択を表示しない場合の補足 |
| preview unavailable | Preview unavailable for this file type. | info | 非対応ファイル時 |
| no active model | No active model to display. Open a Model Weave file. | info | アクティブファイルがない場合 |

## Notes

- 本ルールはツールバーの表示制御方針を定義します。
- 描画モードの実効値決定そのものは `[[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]]` に委ねます。
- DFDの固定方針は `[[RULE-MW-DFD-MERMAID-ONLY]]` を参照します。
- ツールバー操作はViewer上の一時状態であり、Markdownモデルを直接変更しません。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | symbol: appendRendererSelection; kind: method; Renderer selector の表示制御 |
| src/views/modeling-preview-view.ts | symbol: renderCurrentState; kind: method; Viewer状態に応じたUI描画 |
| src/core/render-mode.ts | symbol: getSupportedRenderModes; kind: function; 対象モデルで利用可能な描画モード取得 |
| src/core/render-mode.ts | symbol: resolveRenderMode; kind: function; 実効描画モードの解決 |
