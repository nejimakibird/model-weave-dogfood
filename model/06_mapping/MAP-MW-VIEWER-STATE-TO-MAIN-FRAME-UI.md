---
type: mapping
id: MAP-MW-VIEWER-STATE-TO-MAIN-FRAME-UI
name: Viewer State to Main Frame UI Mapping
kind: other
source: "[[DATA-MW-VIEWER-STATE]]"
target: "[[SCR-MW-VIEWER-MAIN-FRAME]]"
tags:
  - ModelWeave
  - Mapping
  - Viewer
---

# Viewer State to Main Frame UI Mapping

## Summary
Viewer の内部状態 `DATA-MW-VIEWER-STATE` が [[SCR-MW-VIEWER-MAIN-FRAME]] の各 UI 要素へどのように反映されるかを定義する。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-VIEWER-STATE]] | Viewerの内部実行状態 |
| target | [[SCR-MW-VIEWER-MAIN-FRAME]] | Viewer 全体の画面構成 |
| rule | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | レンダラー解決ルール |
| rule | [[RULE-MW-DFD-MERMAID-ONLY]] | DFD 限定ルール |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| currentModelName | [[SCR-MW-VIEWER-MAIN-FRAME]].currentModelTitle | 現在モデル名を表示。空の場合はファイル名または空状態表示へfallback | | Y | アクティブファイル由来 |
| supportedRenderModes | [[SCR-MW-VIEWER-MAIN-FRAME]].renderModeSelector | supportedRenderModes と effectiveRenderMode を元に選択肢と選択状態を設定 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | DFDでは非表示または無効化。effectiveRenderModeも参照 |
| previewContent | [[SCR-MW-VIEWER-MAIN-FRAME]].previewCanvas | renderStatusに応じてGraphModelまたはMermaidSourceまたは状態メッセージを表示 | [[RULE-MW-DFD-MERMAID-ONLY]] | N | GraphModel / MermaidSource / empty / error を集約 |
| diagnostics | [[SCR-MW-VIEWER-MAIN-FRAME]].diagnosticsPanel | 診断パネルへ渡す | [[MAP-MW-RENDERER-DIAGNOSTIC-TO-UI]] | N | error/warning/info/note |

## Rules
- `renderStatus = unsupported` の場合、Messages.previewUnavailable 相当の文言を previewCanvas に表示する。
- `renderStatus = error` の場合、Messages.renderError 相当の文言を previewCanvas に表示する。
- `renderStatus = empty` の場合、Messages.noActiveModel 相当の文言を previewCanvas に表示する。
- Messages.id は現在のマッピングターゲットには直接使用せず、スクリーン内のメッセージ定義として管理する。

## Notes
- 実装上の `ModelingPreviewView.state` の更新に連動して、本マッピングに基づき UI が再描画される。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/views/modeling-preview-view.ts | ModelingPreviewView | class | `state` プロパティが UI へ反映される起点 |
| source/model-weave-repo/src/core/render-mode.ts | resolveRenderMode | function | モード選択肢の解決 |
| source/model-weave-repo/src/core/relation-resolver.ts | resolveDiagramRelations | function | プレビュー用データの生成 |
| source/model-weave-repo/src/renderers/dfd-mermaid.ts | renderDfdMermaidDiagram | function | Mermaid 描画の実行 |