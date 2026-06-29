---
type: app_process
id: PROC-MW-VIEWER-EXPORT-PNG
name: Viewer PNG出力
process_type: viewer_action
tags:
  - ModelWeave
  - Viewer
  - Export
  - PNG
  - Process
  - BusinessFlow
---

# Viewer PNG出力

## Summary

Viewer上の描画済み図をPNGファイルとして派生出力し、必要に応じてOS側の既定アプリで開く処理。
Markdownモデル本文は変更せず、現在の描画結果からexport surfaceを取得してPNGを生成する。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Model Weave architecture domains |

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| viewerState | [[DATA-MW-VIEWER-STATE]] | [[SCR-MW-VIEWER-MAIN-FRAME]] | Y | 現在のPreview状態と描画済み図 |
| exportAction | object | [[SCR-MW-VIEWER-MAIN-FRAME]] | Y | Export PNGまたはExport and Open PNGの操作 |
| renderedDiagram | object | Viewer renderer | N | export surfaceを持つ描画済み図 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| pngFile | object | exports folder | `exports/<model>__<renderer>.png` に保存される派生出力 |
| exportNotice | object | Viewer notice | 出力完了、出力不可、または失敗を通知する |
| openRequest | object | OS default app | Export and Openの場合だけ実行される |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | viewer_ui | PNG出力を開始する | start | exportAction |  |  |  | [[SCR-MW-VIEWER-MAIN-FRAME]] | Viewer toolbarの操作から開始する |
| receiveAction | viewer_ui | 出力操作を受け付ける | input | exportAction | openAfterExport |  | exportCurrentDiagramAsPngWithNotice | [[SCR-MW-VIEWER-MAIN-FRAME]] | Export and Openの場合はopenAfterExportをtrue相当として扱う |
| buildRenderable | preview_pipeline | 出力用renderableを準備する | process | viewerState | renderedDiagram |  | buildCurrentDiagramExportRenderable |  | 現在modeに対応する出力用描画関数を選ぶ |
| checkRenderable | preview_pipeline | 出力対象の有無を判定する | decision | renderedDiagram |  |  |  |  | renderableがない場合は出力不可 |
| renderOffscreen | renderer_area | offscreenで図を描画する | process | renderedDiagram | exportRoot |  | exportDiagramRenderableAsPng |  | forExport相当の描画を一時DOMに作る |
| buildSnapshot | renderer_area | export surfaceを取得する | process | exportRoot | exportSnapshot |  | buildDomDiagramExportSnapshot |  | data-model-weave-export-surfaceを対象にする |
| checkSnapshot | renderer_area | snapshotを検証する | decision | exportSnapshot |  |  |  |  | boundsが取れない場合は失敗扱い |
| encodePng | renderer_area | PNGバイナリを生成する | process | exportSnapshot | pngBuffer |  | renderSnapshotToPng |  | Mermaid SVGまたはDOM surfaceをPNG化する |
| savePng | model_storage | PNGファイルを保存する | process | pngBuffer | pngFile |  | exportDiagramSnapshotAsPng |  | vault内のexports folderへ保存する |
| checkOpen | viewer_ui | 出力後に開くか判定する | decision | exportAction, pngFile |  |  |  |  | Export and Openの場合だけOS側へ渡す |
| openPng | viewer_ui | PNGをOS側で開く | screen | pngFile | openRequest |  | openExportedPng | [[SCR-MW-VIEWER-MAIN-FRAME]] | desktop adapterとElectron shellが使える場合に開く |
| showSuccess | viewer_ui | 成功通知を表示する | screen | pngFile | exportNotice |  | Notice | [[SCR-MW-VIEWER-MAIN-FRAME]] | 出力先パスを通知する |
| showUnavailable | viewer_ui | 出力不可を通知する | screen | viewerState | exportNotice |  | Notice | [[SCR-MW-VIEWER-MAIN-FRAME]] | 現在の表示が出力準備できていない場合 |
| showError | viewer_ui | 出力失敗を通知する | screen | exportSnapshot | exportNotice |  | showPngExportFailureNotice | [[SCR-MW-VIEWER-MAIN-FRAME]] | bounds不正や保存失敗を通知する |
| end | viewer_ui | PNG出力を終了する | end | exportNotice |  |  |  |  | Markdownモデル本文は変更しない |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | receiveAction |  | 操作受付 | toolbar操作を受け付ける |
| receiveAction | buildRenderable |  | 対象準備 | 現在のPreview状態から出力対象を作る |
| buildRenderable | checkRenderable |  | 判定 | renderableの有無を確認する |
| checkRenderable | showUnavailable | `renderedDiagram is missing` | 出力不可 | 現在のviewがPNG出力対象ではない |
| checkRenderable | renderOffscreen | `renderedDiagram exists` | 出力可能 | offscreen描画へ進む |
| renderOffscreen | buildSnapshot |  | snapshot | export surfaceを探す |
| buildSnapshot | checkSnapshot |  | bounds確認 | サイズ取得結果を確認する |
| checkSnapshot | showError | `exportSnapshot is invalid` | 失敗 | surfaceまたはboundsが不正 |
| checkSnapshot | encodePng | `exportSnapshot is valid` | PNG生成 | snapshotをPNGへ変換する |
| encodePng | savePng |  | 保存 | vaultへPNGを書き込む |
| savePng | checkOpen |  | open判定 | 操作種別を確認する |
| checkOpen | openPng | `openAfterExport is true` | 開く | OS側の既定アプリへ渡す |
| checkOpen | showSuccess | `openAfterExport is false` | 通知 | 出力完了だけを通知する |
| openPng | showSuccess |  | 通知 | open処理後に出力完了を通知する |
| showUnavailable | end |  | 終了 | 出力せず終了する |
| showError | end |  | 終了 | エラー通知後に終了する |
| showSuccess | end |  | 終了 | 成功通知後に終了する |

## Notes

- PNG exportは描画済み図の派生出力であり、Markdownモデル本文、frontmatter、Source Links、Diagnosticsを変更しない。
- export対象は `data-model-weave-export-surface="true"` を持つ図本体である。
- toolbar、Review Panel、Diagnostics、lower pane、Details、Mermaid Source、Mermaid Render DebugはPNG出力対象に含めない。
- Applied Color Schemeの結果は描画済みDOMまたはSVGの見た目としてPNGへ反映されるが、Applied Color Scheme section自体はPNGへ含めず、Markdown正本も変更しない。
- Mermaid出力はSVGをPNG化し、custom renderer出力はDOM surfaceをsnapshot化してPNG化する。
- Weave MapのPNG出力はRelationship View内の派生表示を対象にする。専用Markdown形式は作らない。
- Export and Openはdesktop vault adapterとElectron shellが利用できる場合にOS側の既定アプリへ渡す。
- Source Links Explorer、Reference Explorer、Model Index Viewの横断Explorer機能はこのprocessの対象外である。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | Steps: receiveAction, buildRenderable, showUnavailable, openPng, showSuccess, showError. exportCurrentDiagramAsPngWithNotice / exportCurrentDiagramAsPngAndOpenWithNotice / buildCurrentDiagramExportRenderable / openExportedPng がViewer側の入口 |
| src/export/png-export.ts | Steps: renderOffscreen, buildSnapshot, checkSnapshot, encodePng, savePng. exportDiagramRenderableAsPng / buildDomDiagramExportSnapshot / renderSnapshotToPng / exportDiagramSnapshotAsPng がPNG出力を実行する |
| src/renderers/zoom-toolbar.ts | Steps: start, receiveAction. createZoomToolbar がExport PNGとExport and Open PNGボタンを作る |
| src/renderers/mermaid-shared.ts | Steps: renderOffscreen, buildSnapshot. Mermaid rendererのexport surfaceとforExport挙動 |
| src/renderers/app-process-business-flow.ts | Steps: renderOffscreen. Business FlowのforExport描画とColor Scheme反映 |
| src/renderers/weave-map-mermaid.ts | Steps: renderOffscreen. Weave MapのMermaid sourceとlayer coloring |
