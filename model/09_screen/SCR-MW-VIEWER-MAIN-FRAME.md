---
type: screen
id: SCR-MW-VIEWER-MAIN-FRAME
name: Model Weave Viewer Main Frame
screen_type: dashboard
kind: view
tags:
  - ModelWeave
  - Screen
  - Viewer
---

# Model Weave Viewer Main Frame

## Summary
Model Weave Viewer全体のレイアウトを定義し、上部ツールバー、中央プレビュー領域、下部診断パネルの関係を明確にする。
ズームコントロール、レンダラー選択、診断表示、Relationship View、Weave Map、Applied Color Scheme表示などの主要なUI要素を配置する。

## Layout

| id | label | kind | purpose | notes |
|---|---|---|---|---|
| viewer_root | Model Weave Viewer Root Container | container | Viewer全体のルート要素 | |
| toolbar | Viewer Toolbar | toolbar | ズーム、レンダラー選択などの操作を含む上部ツールバー | focus buttonと各図のzoom toolbarを含む |
| preview_area | Main Preview Area | content-pane | モデルのメイン表示領域（ダイアグラム、テーブル、テキストなど） | |
| diagnostics_panel_area | Diagnostics Panel Area | content-pane | 診断メッセージを表示する下部パネル領域 | |
| review_panel_area | Review Panel Area | content-pane | Review Summary、Diagnostics、Relationships / Impact、Source Links、Weave Map、Details、Mermaid Sourceを表示する下部領域 | implemented。Viewer split shellのbottomPaneまたはcollection diagram lower slots相当 |
| details_panel_area | Details Panel Area | content-pane | Details、Applied Color Schemeなどの補助sectionを表示する下部領域 | implemented。Review Panel内のdetails slotまたはbottomPane相当 |

## Fields

| id | label | kind | layout | data_type | required | ref | rule | notes |
|---|---|---|---|---|---|---|---|---|
| currentModelTitle | Current Model Title | label | toolbar | string | Y | | | 現在表示中のモデルのタイトル |
| fitButton | Fit to View Button | button | toolbar |  | N | | | プレビューをビューポートにフィットさせるボタン |
| zoomOutButton | Zoom Out Button | button | toolbar |  | N | | | プレビューを縮小するボタン |
| zoomInButton | Zoom In Button | button | toolbar |  | N | | | プレビューを拡大するボタン |
| resetZoomButton | Reset Zoom Button | button | toolbar |  | N | | | プレビューを100%にリセットするボタン |
| focusModeButton | Focus Mode Button | button | toolbar | action | N | [[DATA-MW-VIEWER-STATE]].focusMode | | implemented。Viewer全体のfocus modeを切り替える |
| viewOnlyButton | View Only Button | button | toolbar | action | N | [[DATA-MW-VIEWER-STATE]].viewOnlyMode | | implemented。現在の図をView Only stageへ移動する |
| renderModeSelector | Renderer Mode Selector | select | toolbar | string | N | | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | レンダラーモードを切り替える。DFDでは非表示または無効化 |
| businessFlowConnectButton | Flow Connect Button | button | toolbar | action | N | [[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]].connectModeEnabled | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] | implemented。app_process Business Flow限定のFlow接続モード切替 |
| businessFlowConnectStatus | Flow Connect Status | label | toolbar | string | N | [[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]].sourceStepId | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] | implemented。接続元Stepの選択状態を表示 |
| previewCanvas | Preview Canvas | panel | preview_area |  | Y | [[DATA-MW-RENDERER-GRAPH-MODEL]] | | 実際のモデルレンダリングが行われる領域 |
| businessFlowStepInteractionTarget | Business Flow Step Interaction Target | object | preview_area | object | N | [[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]].interactionTargets | | implemented。Business Flow Stepのhover preview / click target |
| businessFlowStepKindShape | Business Flow Step Shape | visual | preview_area | string | N | [[DATA-MW-APP-PROCESS-STEP]].kind | [[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]] | implemented。Steps.kindに応じたnode shape |
| reviewPanelArea | Review Panel Area | panel | review_panel_area | object | N | [[DATA-MW-VIEWER-STATE]].reviewPanelSections | | implemented。下部Review Panelの各sectionを配置する領域 |
| reviewSummaryPanel | Review Summary Panel | panel | review_panel_area | object | N | [[DATA-MW-VIEWER-STATE]].reviewSummary | | implemented。model種別、diagnostics件数、Source Links件数、Weave Map可否をchip表示する |
| diagnosticsPanel | Diagnostics Panel | panel | review_panel_area |  | N | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | 診断メッセージ、bulk copy actions、Quick Fix MVPを表示する下部パネル |
| relationshipViewSection | Relationship View Section | panel | review_panel_area | object | N | [[DATA-MW-IMPACT-SUMMARY]] | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。Impact SummaryをViewer下部ペインに表示 |
| copyImpactSummaryButton | Copy Impact Summary Button | button | relationshipViewSection | action | N | [[DATA-MW-IMPACT-SUMMARY]].formattedMarkdown | | implemented。関連サマリをMarkdown文字列としてコピー |
| weaveMapBlock | Weave Map Block | panel | relationshipViewSection | object | N | [[DATA-MW-VIEWER-STATE]].weaveMapMermaidSource | | implemented。Relationship View内の派生視覚化 |
| relatedSourceLinksList | Related Source Links List | list | relationshipViewSection | object | N | [[DATA-MW-IMPACT-SUMMARY]].relatedSourceLinks | | implemented。self / inbound / outbound由来の関連Source Linksを表示 |
| sourceLinksSection | Source Links Section | panel | review_panel_area | object | N | [[DATA-MW-VIEWER-STATE]].sourceLinks | | implemented。Preview内Source Links表示、Copy Path、Open操作を含む |
| detailsSection | Details Section | panel | details_panel_area | object | N | [[DATA-MW-VIEWER-STATE]].previewDetails | | implemented。object detailsやdiagram detailsを下部details slotへ移動して表示する |
| mermaidSourcePanel | Mermaid Source Panel | panel | review_panel_area | text | N | [[DATA-MW-RENDERER-MERMAID-SOURCE]] | | implemented。Mermaid系表示の生成ソースを折りたたみsectionとして表示する |
| mermaidRenderDebugPanel | Mermaid Render Debug Panel | panel | review_panel_area | text | N | [[DATA-MW-VIEWER-STATE]].showMermaidRenderDebug | | implemented。showMermaidRenderDebugがtrueのときMermaid描画診断を表示する |
| appliedColorSchemeSection | Applied Color Scheme Section | panel | details_panel_area | object | N | [[DATA-MW-VIEWER-STATE]].appliedColorSchemeRows | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。resolvedColorSchemeと現在のview targetから適用Color Scheme行を表示 |
| colorSchemeColorEditor | Color Scheme Color Editor | color-input | preview_area | string | N | | | implemented。color_scheme画面でfill / stroke / textをhex colorとして編集する |
| exportPngButton | PNG Export Button | button | toolbar | action | N | | | implemented。出力可能な描画済み図をPNGとして派生出力する |
| exportAndOpenPngButton | PNG Export and Open Button | button | toolbar | action | N | | | implemented。PNG派生出力後にOS側で開く |

## Actions

| id | label | kind | target | event | invoke | transition | rule | notes |
|---|---|---|---|---|---|---|---|---|
| fitToView | Fit to View | ui_action | fitButton | click | | | | previewCanvasをビューポートにフィットさせる |
| zoomOut | Zoom Out | ui_action | zoomOutButton | click | | | | previewCanvasを縮小する |
| zoomIn | Zoom In | ui_action | zoomInButton | click | | | | previewCanvasを拡大する |
| resetZoom | Reset Zoom | ui_action | resetZoomButton | click | | | | previewCanvasを100%にリセットする |
| toggleFocusMode | Toggle Focus Mode | ui_action | focusModeButton | click | setFocusMode | | | Viewerのfocus modeを切り替える |
| toggleViewOnly | Toggle View Only | ui_action | viewOnlyButton | click | setViewOnlyMode | | | 現在の図をView Only表示へ切り替える |
| changeRenderMode | Change Renderer Mode | field_event | renderModeSelector | change | | | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | 選択されたレンダラーモードに切り替える |
| toggleBusinessFlowConnectMode | Toggle Flow Connect Mode | ui_action | businessFlowConnectButton | click | [[PROC-MW-BUSINESS-FLOW-CONNECT-FLOW]] | | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] | app_process Business Flow限定で接続モードを切り替える |
| selectBusinessFlowStep | Select Business Flow Step | ui_action | previewCanvas | click | [[PROC-MW-BUSINESS-FLOW-CONNECT-FLOW]] | | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] | 接続モード中は接続元または接続先Stepとして扱う |
| openBusinessFlowStepTarget | Open Business Flow Step Target | ui_action | previewCanvas | click | attachMermaidNodeInteractions | | | 接続モード外ではref-aware click targetを開く |
| refreshPreview | Refresh Preview | system_event | previewCanvas | other | [[PROC-MW-RENDERER-BUILD-GRAPH-MODEL]] | | | プレビューを再描画する |
| openDiagnosticPanel | Open Diagnostic Panel | ui_action | diagnosticsPanel | click | | | | 診断パネルを開く/閉じる、または診断項目にジャンプする |
| copyImpactSummary | Copy Impact Summary | ui_action | copyImpactSummaryButton | click | formatImpactSummaryAsMarkdown | | | Impact SummaryをMarkdown文字列としてclipboardへコピーする |
| openRelationshipModel | Open Relationship Model | ui_action | relationshipViewSection | click | openReferencedFile | | | Relationship ViewまたはWeave Map上の解決済みモデルを開く |
| exportPng | Export PNG | ui_action | exportPngButton | click | [[PROC-MW-VIEWER-EXPORT-PNG]] | | | 描画済み図をPNGとして派生出力する。Markdown本文は変更しない |
| exportAndOpenPng | Export and Open PNG | ui_action | exportAndOpenPngButton | click | [[PROC-MW-VIEWER-EXPORT-PNG]] | | | PNG出力後にopenExportedPngでOS側の既定アプリを開く |
| editColorSchemeCell | Edit Color Scheme Cell | field_event | colorSchemeColorEditor | change | [[PROC-MW-VIEWER-COLOR-SCHEME-EDIT]] | | | `## Colors` の fill / stroke / text セルを更新する |

## Messages

| id | text | severity | timing | notes |
|---|---|---|---|---|
| previewUnavailable | Preview unavailable for this file type. | info | on_unsupported_file | サポートされていないファイルタイプの場合 |
| renderError | Failed to render the model. Check diagnostics for details. | error | on_render_error | レンダリング処理でエラーが発生した場合 |
| noActiveModel | No active model to display. Open a Model Weave file. | info | empty_state | アクティブなファイルがない場合 |
| dfdRendererFixedNotice | DFD diagrams are rendered using Mermaid only. | info | on_dfd_model | DFDでレンダラー選択が固定されている場合の通知 |
| appliedColorSchemeEmpty | No colors defined. | info | on_applied_color_scheme | Applied Color Schemeに表示対象行がない場合 |
| invalidColorSchemeColor | Invalid color value. | warning | on_color_scheme_edit | hex colorとして扱えない入力 |

## Mode Specific Lower Pane

| mode | main pane | lower pane structure | source-backed sections | conditional or absent sections | notes |
|---|---|---|---|---|---|
| object | object context graph | split shell bottomPane | Review Summary、Diagnostics、Relationships / Impact、Source Links、Details | Mermaid SourceはMermaid renderer時のみ | class / er_entityなどのobject preview |
| dfd-object | related diagram graph | split shell bottomPane | Review Summary、Diagnostics、Relationships / Impact、Source Links、Details | Mermaid Sourceはdiagram rendererが生成する場合のみ | DFD objectの詳細と関連図を分けて表示 |
| summary | screen previewまたはBusiness Flow | split shell bottomPaneまたはdetails wrapper | Review Summary、Diagnostics、Overview、Relationships / Impact、Source Links、Details | Mermaid Source、Render Debug、Flow Connect、direction、Applied Color SchemeはBusiness Flow時のみ | screen summaryとapp_process summaryを同じmodeで扱う |
| diagram | collection diagram graph | review、diagnostics、impact、sourceLinks、details、source slots | Review Summary、Diagnostics、Relationships / Impact、Source Links、Details、Mermaid Source | Applied Color Schemeは対象targetがある場合のみ | collection diagram専用のlower slotsを使う |
| domains | domains diagram | split shell bottomPane | Review Summary、Domain Tree、Diagnostics、Domain Relationships、Source Links、Domain Details、Applied Color Scheme | Weave Mapは表示しない | target=domainのColor Schemeを表示 |
| domain-diagram | resolved domain diagram | split shell bottomPane | Review Summary、Domain Sources、Domain Tree、Diagnostics、Conflicts、Domain Relationships、Source Links、Details、Applied Color Scheme | Weave Mapは表示しない | external Domainsとlocal Domainsの解決結果を表示 |
| color-scheme | color scheme details | lower paneなし | Diagnostics、Colors table、color picker | Review Summary、Relationship View、Source Linksは表示しない | color_scheme自身の定義確認画面 |
| relations | relations list | lower paneなし | Relations list | Review Summary、Relationship View、Source Linksは表示しない | schema-driven relations fileの簡易表示 |

## Related Screens

- [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]]

## Related Data Objects

- [[DATA-MW-RENDERER-RENDER-MODE-INPUT]]
- [[DATA-MW-RENDERER-GRAPH-MODEL]]
- [[DATA-MW-RENDERER-MERMAID-SOURCE]]
- [[DATA-MW-CORE-DIAGNOSTIC]]
- [[DATA-MW-VIEWER-STATE]]
- [[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]]
- [[DATA-MW-APP-PROCESS-STEP]]
- [[DATA-MW-PLUGIN-SETTINGS]]
- [[DATA-MW-IMPACT-SUMMARY]]

## Related Rules

- [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]]
- [[RULE-MW-DFD-MERMAID-ONLY]]
- [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]]
- [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]]
- [[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]]

## Related Processes

- [[PROC-MW-RENDERER-BUILD-GRAPH-MODEL]]
- [[PROC-MW-RENDERER-GENERATE-MERMAID-SOURCE]]
- [[PROC-MW-BUSINESS-FLOW-CONNECT-FLOW]]
- [[PROC-MW-VIEWER-EXPORT-PNG]]
- [[PROC-MW-VIEWER-COLOR-SCHEME-EDIT]]

## Notes
- `renderModeSelector` の表示/非表示、および有効/無効は、現在のモデルタイプと [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] および [[RULE-MW-DFD-MERMAID-ONLY]] に基づいて制御される。
- `diagnosticsPanel` は [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] のインスタンスとして `diagnostics_panel_area` に表示される。
- Review Panelは実装上、Review Summary、Diagnostics、Relationships / Impact、Source Links、Weave Map、Details、Mermaid Sourceを下部領域へ配置する。
- collection diagramでは review / diagnostics / impact / sourceLinks / details / source のslotsに分けて下部領域を構成する。
- mode別のlower pane構成は `renderObjectState`、`renderSummaryState`、`renderDiagramState` などの各render methodで決まる。すべてのmodeが同じReview Panel構造を持つわけではない。
- color_scheme modeとrelations modeは、現行実装では標準のsplit shellやReview Summaryを使わず、内容を直接表示する。
- Mermaid SourceとMermaid Render DebugはMermaid sourceを生成する表示で利用され、custom-only表示では生成ソースがない場合に表示しない。
- View Onlyは描画済み図を専用stageへ移すViewer一時状態であり、Markdown本文や設定値を書き換えない。
- Focus modeはViewer全体の表示集中モードであり、hover previewが画面端では出にくい場合があるためclick navigationを確実なfallbackとして扱う。
- `relationshipViewSection` は [[DATA-MW-IMPACT-SUMMARY]] を表示するViewer上のsectionであり、Markdownモデル本文を書き換えない。
- Weave MapはRelationship View内でImpactSummaryから生成される派生視覚化であり、専用Markdown形式ではない。
- Relationship Viewには inbound relationships、outbound relationships、unresolved outbound references、CodeSet value usage、related Source Linksが含まれる。
- Diagnostics Panelのbulk Markdown copyは表示中の診断をClipboardへ渡す操作であり、Markdown正本を書き換えない。
- Copy Impact Summaryは表示中のImpactSummaryをMarkdown文字列へ整形してclipboardへ渡す操作であり、保存モデルを作らない。
- `appliedColorSchemeSection` は resolvedColorScheme と現在のview targetから組み立てるViewer表示であり、Markdownモデル本文を書き換えない。colorSchemeがない場合、またはtargetが空の場合は表示しない。
- Applied Color Scheme sectionは `Target / Kind / Preview / Notes / Source` のcompact tableとして表示され、swatch previewはfill / stroke / textをまとめて示す。
- Applied Color Schemeは domains / domain_diagram、DFD diagram、app_process Business Flow、Relationship View内のWeave Mapを含む補助表示で使われる。
- color_scheme画面のcolor picker編集は `## Colors` の fill / stroke / text セルをMarkdown上で更新する専用操作であり、Diagnostics Quick Fixや汎用自動修正とは別である。
- Diagnostics Panel内のQuick Fix MVPは missing frontmatter id / name に限定したユーザー操作であり、invalid table headerや任意Markdown本文を自動修正しない。
- defaultColorSchemeRefが未設定の場合は組み込み配色を使う。未解決またはcolor_scheme以外を指す場合はwarningを出し、組み込み配色へfallbackする。
- Applied Color Scheme section自体はPNG export対象ではない。PNGには配色反映済みの図本体だけが出力される。
- PNG exportは描画済み結果を出力するため色を保持するが、Markdown正本は変更しない。
- PNG exportは図本体を出力対象とし、toolbar、diagnostics、下部details、Mermaid Source、Mermaid Render Debugは出力対象に含めない。
- Export and Openは出力後にdesktop vault adapterとElectron shellを使ってOS側の既定アプリへ渡す。利用できない環境では通知で終了する。
- Flow Connect Modeはapp_process Business Flow限定の `## Flows` 更新支援であり、接続元Stepと接続先StepのクリックからMarkdownを更新する。
- `businessFlowConnectStatus` は接続元Stepの未選択 / 選択中を表示するViewer内状態であり、保存設定ではない。
- ref-aware hover / click targetはBusiness Flow Stepに紐づく input / output / rule / invoke / screen などの参照確認と遷移支援である。
- Business Flow Stepのshapeは `Steps.kind` から決まり、空または未知のkindは通常process shapeへfallbackする。
- Business Flow Stepの色はColor Scheme `target=app_process` と `kind=<Steps.kind>` により解決される。
- `Steps.kind` は表示意味とshape / colorの入力であり、FlowsやTransitionsのendpointではない。
- Flow Connect Modeとref-aware hover / click targetはapp_process Business Flow限定の操作支援であり、DFD / Class / ERの汎用グラフ編集ではない。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | symbol: ModelingPreviewView; kind: class; Viewerのメインビュークラス |
| src/views/modeling-preview-view.ts | symbol: renderCurrentState; kind: method; 現在のプレビュー状態をレンダリングする |
| src/views/modeling-preview-view.ts | symbol: renderReviewSummaryPanel; kind: method; Review Summary chip群を表示する |
| src/views/modeling-preview-view.ts | symbol: createCollectionDiagramLowerPaneSlots; kind: method; collection diagramの下部Review Panel slotsを作成する |
| src/views/modeling-preview-view.ts | symbol: moveDetailSections; kind: method; Mermaid renderer由来のdetails sectionを下部details slotへ移動する |
| src/views/modeling-preview-view.ts | symbol: appendViewerFocusToolbar; kind: method; Viewer全体のfocus modeボタンを作成する |
| src/views/modeling-preview-view.ts | symbol: appendViewOnlyControl; kind: method; 各図のView Onlyボタンをzoom toolbarへ追加する |
| src/views/modeling-preview-view.ts | symbol: setViewOnlyMode; kind: method; View Only stageへの移動と復帰を制御する |
| src/views/modeling-preview-view.ts | symbol: renderObjectState; kind: method; オブジェクトモデルのプレビューをレンダリングする |
| src/views/modeling-preview-view.ts | symbol: renderSummaryState; kind: method; サマリーモデルのプレビューをレンダリングする |
| src/views/modeling-preview-view.ts | symbol: renderDomainsState; kind: method; domains modeの下部表示を構成する |
| src/views/modeling-preview-view.ts | symbol: renderDomainDiagramState; kind: method; domain_diagram modeの下部表示を構成する |
| src/views/modeling-preview-view.ts | symbol: renderColorSchemeState; kind: method; color_scheme modeを直接表示する |
| src/views/modeling-preview-view.ts | symbol: renderDfdObjectState; kind: method; DFDオブジェクトモデルのプレビューをレンダリングする |
| src/views/modeling-preview-view.ts | symbol: renderDiagramState; kind: method; ダイアグラムモデルのプレビューをレンダリングする |
| src/views/modeling-preview-view.ts | symbol: renderRelationsState; kind: method; relations modeを直接表示する |
| src/views/modeling-preview-view.ts | symbol: createViewerSplitShell; kind: method; 上下分割パネルのUIシェルを作成する |
| src/views/modeling-preview-view.ts | symbol: appendRendererSelection; kind: method; レンダラー選択UIをツールバーに追加する |
| src/views/modeling-preview-view.ts | symbol: updateAppProcessFlowConnectButton; kind: method; Flow接続ボタンの表示状態を更新する |
| src/views/modeling-preview-view.ts | symbol: createAppProcessFlowConnectStatus; kind: method; 接続元Stepの選択状態を表示する |
| src/views/modeling-preview-view.ts | symbol: handleAppProcessFlowConnectStepClick; kind: method; Business Flow StepクリックからFlow接続処理を実行する |
| src/core/app-process-flow-editor.ts | symbol: addAppProcessFlow; kind: function; app_process `## Flows` 行を追加する |
| src/core/app-process-step-interaction-target.ts | symbol: resolveAppProcessStepInteractionTarget; kind: function; Stepのref-aware hover / click targetを解決する |
| src/views/mermaid-node-interactions.ts | symbol: attachMermaidNodeInteractions; kind: function; Mermaid SVGノードへhover / click動作を付与する |
| src/renderers/app-process-business-flow.ts | symbol: getStepShapeKind; kind: function; Business Flow Step kindからshapeを選ぶ |
| src/renderers/app-process-business-flow.ts | symbol: addAppProcessStepStyle; kind: function; Business Flow Step kindをColor Scheme target=app_processへ渡す |
| src/views/modeling-preview-view.ts | symbol: renderDiagnostics; kind: function; 診断情報をレンダリングするユーティリティ関数 |
| src/views/modeling-preview-view.ts | symbol: renderImpactSummarySection; kind: method; Relationship View sectionを表示する |
| src/views/modeling-preview-view.ts | symbol: renderWeaveMapBlock; kind: method; Relationship View内のWeave Mapを表示する |
| src/renderers/mermaid-shared.ts | symbol: renderMermaidSourceIntoShell; kind: function; Mermaid SourceとRender Debug panelを生成する |
| src/core/impact-analyzer.ts | symbol: formatImpactSummaryAsMarkdown; kind: function; Copy Impact Summary用Markdownを生成する |
| src/core/weave-map.ts | symbol: buildWeaveMapModel; kind: function; ImpactSummaryからWeave Map派生モデルを生成する |
| src/renderers/weave-map-mermaid.ts | symbol: buildWeaveMapMermaidSource; kind: function; Weave Map Mermaid sourceを生成する |
| src/views/modeling-preview-view.ts | symbol: renderAppliedColorScheme; kind: method; Applied Color Scheme sectionを下部ペインに表示する |
| src/views/applied-color-scheme-renderer.ts | symbol: renderAppliedColorSchemeSectionContent; kind: function; Applied Color Schemeのsummaryとtableを描画する |
| src/core/color-scheme.ts | symbol: getAppliedColorSchemeRowsForTargets; kind: function; view targetに対応するApplied Color Scheme表示行を作る |
| src/core/color-scheme.ts | symbol: resolveDefaultColorScheme; kind: function; defaultColorSchemeRefの未設定 / 未解決 / 不正type fallbackを扱う |
| src/core/color-scheme-table-editor.ts | symbol: updateColorSchemeColorCell; kind: function; `## Colors` のfill / stroke / textセルを更新する |
| src/export/png-export.ts | symbol: exportDiagramRenderableAsPng; kind: function; 描画済み図をPNGとして出力する |
| src/export/png-export.ts | symbol: buildDomDiagramExportSnapshot; kind: function; export surfaceとboundsを取得する |
| src/views/modeling-preview-view.ts | symbol: openExportedPng; kind: method; PNG出力後にOS側でファイルを開く |
| src/views/modeling-preview-view.ts | symbol: buildCurrentDiagramExportRenderable; kind: method; 現在のPreview状態からPNG出力用renderableを作る |
| src/views/modeling-preview-view.ts | symbol: exportCurrentDiagramAsPngWithNotice; kind: method; PNG出力操作と通知を実行する |
| src/views/modeling-preview-view.ts | symbol: exportCurrentDiagramAsPngAndOpenWithNotice; kind: method; PNG出力後にopenExportedPngへ進む |
| src/renderers/zoom-toolbar.ts | symbol: createZoomToolbar; kind: function; PNG出力ボタンとExport and Openボタンを作る |
| src/core/render-mode.ts | symbol: resolveRenderMode; kind: function; レンダラーモードを解決する |
| src/core/render-mode.ts | symbol: getSupportedRenderModes; kind: function; サポートされているレンダラーモードを取得する |
| src/main.ts | symbol: syncPreviewToActiveFile; kind: method; アクティブファイルとプレビューを同期する |
| src/main.ts | symbol: openDiagnosticLocation; kind: method; 診断箇所にジャンプする |
