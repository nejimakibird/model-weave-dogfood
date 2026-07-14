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
| rule | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | Relationship ViewとColor Scheme表示の実効解決 |
| rule | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] | app_process Business Flow限定のFlow接続更新 |
| rule | [[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]] | Business Flow Step kindのshape / color解決 |
| source | [[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]] | Flow接続モードとStep参照操作先の一時状態 |
| process | [[PROC-MW-VIEWER-EXPORT-PNG]] | PNG出力とExport and Openの操作フロー |

## Mappings

| target_ref | source_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[SCR-MW-VIEWER-MAIN-FRAME]].currentModelTitle | currentModelName | 現在モデル名を表示。空の場合はファイル名または空状態表示へfallback |  | Y | アクティブファイル由来 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].renderModeSelector | supportedRenderModes | supportedRenderModes と effectiveRenderMode を元に選択肢と選択状態を設定 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | DFDでは非表示または無効化。effectiveRenderModeも参照 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].previewCanvas | previewContent | renderStatusに応じてGraphModelまたはMermaidSourceまたは状態メッセージを表示 | [[RULE-MW-DFD-MERMAID-ONLY]] | N | GraphModel / MermaidSource / empty / error を集約 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].focusModeButton | focusMode | focusModeEnabledをViewer root classとbutton状態へ反映 |  | N | implemented。Viewer全体の集中表示 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].viewOnlyButton | viewOnlyMode | viewOnlyEnabledとviewOnlyTargetをbutton状態とView Only stageへ反映 |  | N | implemented。描画済み図だけを一時表示する |
| [[SCR-MW-VIEWER-MAIN-FRAME]].reviewSummaryPanel | reviewSummary | model情報、diagnostics件数、relationship件数、Source Links件数、Weave Map可否をchip表示へ変換 |  | N | implemented。renderReviewSummaryPanel相当 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].reviewPanelArea.slotStructure | reviewPanelSections | modeに応じて下部Review Panelのslot構成を決める |  | N | implemented。collection diagramでは専用slotsを使う |
| [[SCR-MW-VIEWER-MAIN-FRAME]].reviewPanelArea.availableTabs | lowerPanelTabState.availableTabs | available tabsをViewer下部tab UIへ反映 |  | N | implemented。lower panel tabsの構成要素 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].reviewPanelArea.activeTab | lowerPanelTabState.activeTab | active tabをViewer下部tab UIへ反映 |  | N | implemented。renderer switchingやBusiness Flow direction変更後も安定維持 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].reviewPanelArea.modeLayout | modeLowerPaneLayout | PreviewState.modeごとのrender methodでbottomPane、lowerSlots、またはlower paneなしを選ぶ |  | N | implemented。mode別Review Panel差分 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].diagnosticsPanel | diagnostics | 診断パネルへ渡す | [[MAP-MW-RENDERER-DIAGNOSTIC-TO-UI]] | N | error / warning / info |
| [[SCR-MW-VIEWER-MAIN-FRAME]].businessFlowConnectButton | businessFlowConnectMode | 接続モードの有効状態をボタンの押下状態とラベルへ反映 | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] | N | app_process Business Flow表示時だけ対象 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].businessFlowConnectStatus | businessFlowConnectSourceStepId | 接続元Stepの未選択または選択中状態を表示 | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] | N | 接続モード中の一時状態 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].businessFlowStepInteractionTarget | businessFlowInteractionTargets | Step参照操作先をBusiness Flow SVGノードへ付与 |  | N | hover preview / click target用 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].businessFlowStepKindShape | resolvedColorScheme | target=app_process と Steps.kindからStep色を反映 | [[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]] | N | Business Flow Step coloring |
| [[SCR-MW-VIEWER-MAIN-FRAME]].relationshipViewSection | impactSummary | inbound / outbound / unresolved / value usage / related Source Linksを表示 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。enableRelationshipViewが有効な場合の派生表示 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].weaveMapBlock | weaveMapMermaidSource | ImpactSummaryから生成したMermaid sourceを表示 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。Relationship View内の派生視覚化 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].copyImpactSummaryButton | impactSummary.formattedMarkdown | formatImpactSummaryAsMarkdownでcopy用文字列を生成 |  | N | implemented。clipboardへ渡す操作 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].relatedSourceLinksList | impactSummary.relatedSourceLinks | self / inbound / outbound由来のSource Linksを一覧化 |  | N | implemented。Source Links Explorerの一覧状態とは別 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].sourceLinksSection | sourceLinks | Source Links sectionを表示し、Copy Path / Open操作を提供 |  | N | implemented。Source Links Explorerとは別 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].detailsSection | previewDetails | object / diagram / connection detailsを下部details領域へ移動または表示 |  | N | implemented。moveDetailSections相当 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].mermaidSourcePanel | mermaidSourceVisible | Mermaid sourceがある表示では生成ソースpanelを表示 |  | N | implemented。custom-only表示では表示しない |
| [[SCR-MW-VIEWER-MAIN-FRAME]].mermaidRenderDebugPanel | showMermaidRenderDebug | debug設定がtrueでMermaid表示の場合だけ描画診断を表示 |  | N | implemented。Mermaid renderer用 |
| [[SCR-MW-VIEWER-MAIN-FRAME]].appliedColorSchemeSection | appliedColorSchemeRows | view targetに対応するColor Scheme行を表示 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。targetがある場合だけ表示し、Weave Mapがある場合はtarget=weave_mapを含む |
| [[SCR-MW-VIEWER-MAIN-FRAME]].exportPngButton | pngExportAvailable | 出力用renderableが作れる場合にPNG出力操作を有効にする |  | N | implemented。図本体のみを派生出力する |
| [[SCR-MW-VIEWER-MAIN-FRAME]].exportAndOpenPngButton | pngExportAvailable | PNG出力後にOS側で開く操作を有効にする |  | N | implemented。[[PROC-MW-VIEWER-EXPORT-PNG]] でopenExportedPngへ続く |

## Rules
- `renderStatus = unsupported` の場合、Messages.previewUnavailable 相当の文言を previewCanvas に表示する。
- `renderStatus = error` の場合、Messages.renderError 相当の文言を previewCanvas に表示する。
- `renderStatus = empty` の場合、Messages.noActiveModel 相当の文言を previewCanvas に表示する。
- Messages.id は現在のマッピングターゲットには直接使用せず、スクリーン内のメッセージ定義として管理する。
- `relationshipViewEnabled` がfalseの場合、impactSummary / weaveMapMermaidSource は表示準備されない。
- Flow接続モードはapp_process Business Flow限定の `## Flows` 更新支援として表示する。DFD / Class / ERの接続編集UIとしては扱わない。
- `businessFlowInteractionTargets` は接続モード外のref-aware hover / click targetであり、Source Links ExplorerやReference Explorerの一覧状態ではない。
- Business Flow Step kindはStep nodeのshapeとColor Scheme `target=app_process` の入力として扱う。FlowsやTransitionsのendpointとしては扱わない。
- Relationship ViewとWeave MapはViewer上の派生表示であり、Markdown正本やfrontmatterは変更しない。
- Weave Mapは専用Markdown形式ではなく、ImpactSummaryから生成したRelationship View内のMermaid表示である。
- Applied Color Scheme sectionはRelationship View内のWeave Mapがある場合、weave_map layer coloringの適用状況も表示対象に含める。
- Applied Color Scheme sectionはViewer下部の確認表示であり、Mermaid source本文やPNG export対象そのものではない。
- Review PanelはReview Summary、Diagnostics、Relationships / Impact、Source Links、Weave Map、Details、Mermaid Sourceを下部領域に配置する。すべてのmodeで同じslot構造とは限らない。
- lower panel tabsはReview Panel内の表示切替であり、Markdown正本やfrontmatterを書き換えない。
- lower panel tabsはgraph / non-graph review viewの両方に適用され、renderer switchingやBusiness Flow direction変更後も同一file / mode内でactive tabを維持する。
- Source Links tabはPreview内Source Links sectionを表示するものであり、Source Links Explorerではない。
- object / dfd-object / domains / domain-diagram はsplit shellのbottomPaneを使い、diagramは専用lower slotsを使う。
- color_scheme と relations は現行実装では標準Review Panelを使わず、内容を直接表示する。
- Mermaid Source / Render DebugはMermaid sourceを生成する表示に限って扱い、Mermaid sourceを持たないcustom-only表示へ強制しない。
- PNG exportはtoolbarや下部Review Panelを含めず、描画済み図本体の派生出力として扱う。
- Export and OpenのOS側open可否はdesktop adapterとElectron shellに依存するため、Viewer Stateの保存項目としては扱わない。
- Focus modeとView OnlyはViewerの一時UI状態であり、保存設定やMarkdown正本を変更しない。

## Notes
- 実装上の `ModelingPreviewView.state` の更新に連動して、本マッピングに基づき UI が再描画される。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | symbol: ModelingPreviewView; kind: class; `state` プロパティが UI へ反映される起点 |
| src/views/modeling-preview-view.ts | symbol: renderReviewSummaryPanel; kind: method; Review Summary chip群を表示 |
| src/views/modeling-preview-view.ts | symbol: applyLowerPanelTabsToPane; kind: method; lower panelをtab UIへ変換 |
| src/views/modeling-preview-view.ts | symbol: resolveActiveLowerPanelTab; kind: method; active tabの維持またはfallback |
| src/views/modeling-preview-view.ts | symbol: activeLowerPanelTabId; kind: field; Viewer内の選択中lower tab |
| src/views/modeling-preview-view.ts | symbol: createCollectionDiagramLowerPaneSlots; kind: method; 下部Review Panel slotsを構築 |
| src/views/modeling-preview-view.ts | symbol: renderObjectState; kind: method; object modeの下部表示を構成 |
| src/views/modeling-preview-view.ts | symbol: renderSummaryState; kind: method; summary modeのscreen preview / Business Flow下部表示を構成 |
| src/views/modeling-preview-view.ts | symbol: renderDomainsState; kind: method; domains modeの下部表示を構成 |
| src/views/modeling-preview-view.ts | symbol: renderDomainDiagramState; kind: method; domain_diagram modeの下部表示を構成 |
| src/views/modeling-preview-view.ts | symbol: renderColorSchemeState; kind: method; color_scheme modeを直接表示 |
| src/views/modeling-preview-view.ts | symbol: renderDfdObjectState; kind: method; dfd-object modeの下部表示を構成 |
| src/views/modeling-preview-view.ts | symbol: renderDiagramState; kind: method; diagram modeのlower slotsを構成 |
| src/views/modeling-preview-view.ts | symbol: renderRelationsState; kind: method; relations modeを直接表示 |
| src/views/modeling-preview-view.ts | symbol: moveDetailSections; kind: method; details sectionを下部領域へ移動 |
| src/views/modeling-preview-view.ts | symbol: appendViewerFocusToolbar; kind: method; Focus mode操作を表示 |
| src/views/modeling-preview-view.ts | symbol: setViewOnlyMode; kind: method; View Only一時状態を反映 |
| src/core/render-mode.ts | symbol: resolveRenderMode; kind: function; モード選択肢の解決 |
| src/core/relation-resolver.ts | symbol: resolveDiagramRelations; kind: function; プレビュー用データの生成 |
| src/renderers/dfd-mermaid.ts | symbol: renderDfdMermaidDiagram; kind: function; Mermaid 描画の実行 |
| src/views/modeling-preview-view.ts | symbol: updateAppProcessFlowConnectButton; kind: method; Flow接続モード状態をボタンへ反映 |
| src/views/modeling-preview-view.ts | symbol: createAppProcessFlowConnectStatus; kind: method; 接続元Step選択状態を表示 |
| src/core/app-process-flow-editor.ts | symbol: addAppProcessFlow; kind: function; app_process `## Flows` 更新結果を生成 |
| src/core/app-process-step-interaction-target.ts | symbol: resolveAppProcessStepInteractionTarget; kind: function; Business Flow Stepの参照操作先を解決 |
| src/views/mermaid-node-interactions.ts | symbol: attachMermaidNodeInteractions; kind: function; Business Flow SVGノードへhover / click targetを付与 |
| src/renderers/app-process-business-flow.ts | symbol: getStepShapeKind; kind: function; Business Flow Step kindからshapeを選ぶ |
| src/renderers/app-process-business-flow.ts | symbol: addAppProcessStepStyle; kind: function; Business Flow Step kindをColor Scheme target=app_processへ渡す |
| src/views/modeling-preview-view.ts | symbol: renderImpactSummarySection; kind: method; Relationship Viewの表示 |
| src/views/modeling-preview-view.ts | symbol: renderWeaveMapBlock; kind: method; Relationship View内Weave Mapの表示 |
| src/views/modeling-preview-view.ts | symbol: renderAppliedColorScheme; kind: method; Applied Color Scheme sectionの表示 |
| src/core/color-scheme.ts | symbol: getAppliedColorSchemeRowsForTargets; kind: function; Applied Color Scheme表示行を構築 |
| src/renderers/mermaid-shared.ts | symbol: renderMermaidSourceIntoShell; kind: function; Mermaid Source / Render Debug表示 |
| src/core/impact-analyzer.ts | symbol: formatImpactSummaryAsMarkdown; kind: function; Copy Impact Summaryの文字列生成 |
| src/renderers/weave-map-mermaid.ts | symbol: buildWeaveMapMermaidSource; kind: function; Weave Map Mermaid source生成 |
| src/renderers/weave-map-mermaid.ts | symbol: resolveLayerStyle; kind: function; target=weave_mapのlayer color override |
| src/export/png-export.ts | symbol: exportDiagramRenderableAsPng; kind: function; PNG派生出力 |
| src/export/png-export.ts | symbol: buildDomDiagramExportSnapshot; kind: function; export surfaceとboundsを取得 |
| src/views/modeling-preview-view.ts | symbol: buildCurrentDiagramExportRenderable; kind: method; pngExportAvailable相当の出力用renderableを準備 |
| src/views/modeling-preview-view.ts | symbol: openExportedPng; kind: method; PNG出力後にOS側で開く |
| src/renderers/zoom-toolbar.ts | symbol: createZoomToolbar; kind: function; PNG出力ボタンとExport and Openボタンを表示 |
