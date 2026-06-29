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

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| currentModelName | [[SCR-MW-VIEWER-MAIN-FRAME]].currentModelTitle | 現在モデル名を表示。空の場合はファイル名または空状態表示へfallback | | Y | アクティブファイル由来 |
| supportedRenderModes | [[SCR-MW-VIEWER-MAIN-FRAME]].renderModeSelector | supportedRenderModes と effectiveRenderMode を元に選択肢と選択状態を設定 | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | N | DFDでは非表示または無効化。effectiveRenderModeも参照 |
| previewContent | [[SCR-MW-VIEWER-MAIN-FRAME]].previewCanvas | renderStatusに応じてGraphModelまたはMermaidSourceまたは状態メッセージを表示 | [[RULE-MW-DFD-MERMAID-ONLY]] | N | GraphModel / MermaidSource / empty / error を集約 |
| focusMode | [[SCR-MW-VIEWER-MAIN-FRAME]].focusModeButton | focusModeEnabledをViewer root classとbutton状態へ反映 | | N | implemented。Viewer全体の集中表示 |
| viewOnlyMode | [[SCR-MW-VIEWER-MAIN-FRAME]].viewOnlyButton | viewOnlyEnabledとviewOnlyTargetをbutton状態とView Only stageへ反映 | | N | implemented。描画済み図だけを一時表示する |
| reviewSummary | [[SCR-MW-VIEWER-MAIN-FRAME]].reviewSummaryPanel | model情報、diagnostics件数、relationship件数、Source Links件数、Weave Map可否をchip表示へ変換 | | N | implemented。renderReviewSummaryPanel相当 |
| reviewPanelSections | [[SCR-MW-VIEWER-MAIN-FRAME]].reviewPanelArea | modeに応じて下部Review Panelのslot構成を決める | | N | implemented。collection diagramでは専用slotsを使う |
| modeLowerPaneLayout | [[SCR-MW-VIEWER-MAIN-FRAME]].reviewPanelArea | PreviewState.modeごとのrender methodでbottomPane、lowerSlots、またはlower paneなしを選ぶ | | N | implemented。mode別Review Panel差分 |
| diagnostics | [[SCR-MW-VIEWER-MAIN-FRAME]].diagnosticsPanel | 診断パネルへ渡す | [[MAP-MW-RENDERER-DIAGNOSTIC-TO-UI]] | N | error / warning / info |
| businessFlowConnectMode | [[SCR-MW-VIEWER-MAIN-FRAME]].businessFlowConnectButton | 接続モードの有効状態をボタンの押下状態とラベルへ反映 | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] | N | app_process Business Flow表示時だけ対象 |
| businessFlowConnectSourceStepId | [[SCR-MW-VIEWER-MAIN-FRAME]].businessFlowConnectStatus | 接続元Stepの未選択または選択中状態を表示 | [[RULE-MW-BUSINESS-FLOW-CONNECT-FLOW]] | N | 接続モード中の一時状態 |
| businessFlowInteractionTargets | [[SCR-MW-VIEWER-MAIN-FRAME]].businessFlowStepInteractionTarget | Step参照操作先をBusiness Flow SVGノードへ付与 | | N | hover preview / click target用 |
| resolvedColorScheme | [[SCR-MW-VIEWER-MAIN-FRAME]].businessFlowStepKindShape | target=app_process と Steps.kindからStep色を反映 | [[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]] | N | Business Flow Step coloring |
| impactSummary | [[SCR-MW-VIEWER-MAIN-FRAME]].relationshipViewSection | inbound / outbound / unresolved / value usage / related Source Linksを表示 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。enableRelationshipViewが有効な場合の派生表示 |
| weaveMapMermaidSource | [[SCR-MW-VIEWER-MAIN-FRAME]].weaveMapBlock | ImpactSummaryから生成したMermaid sourceを表示 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。Relationship View内の派生視覚化 |
| impactSummary.formattedMarkdown | [[SCR-MW-VIEWER-MAIN-FRAME]].copyImpactSummaryButton | formatImpactSummaryAsMarkdownでcopy用文字列を生成 | | N | implemented。clipboardへ渡す操作 |
| impactSummary.relatedSourceLinks | [[SCR-MW-VIEWER-MAIN-FRAME]].relatedSourceLinksList | self / inbound / outbound由来のSource Linksを一覧化 | | N | implemented。Source Links Explorerの一覧状態とは別 |
| sourceLinks | [[SCR-MW-VIEWER-MAIN-FRAME]].sourceLinksSection | Source Links sectionを表示し、Copy Path / Open操作を提供 | | N | implemented。Source Links Explorerとは別 |
| previewDetails | [[SCR-MW-VIEWER-MAIN-FRAME]].detailsSection | object / diagram / connection detailsを下部details領域へ移動または表示 | | N | implemented。moveDetailSections相当 |
| mermaidSourceVisible | [[SCR-MW-VIEWER-MAIN-FRAME]].mermaidSourcePanel | Mermaid sourceがある表示では生成ソースpanelを表示 | | N | implemented。custom-only表示では表示しない |
| showMermaidRenderDebug | [[SCR-MW-VIEWER-MAIN-FRAME]].mermaidRenderDebugPanel | debug設定がtrueでMermaid表示の場合だけ描画診断を表示 | | N | implemented。Mermaid renderer用 |
| appliedColorSchemeRows | [[SCR-MW-VIEWER-MAIN-FRAME]].appliedColorSchemeSection | view targetに対応するColor Scheme行を表示 | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | N | implemented。targetがある場合だけ表示し、Weave Mapがある場合はtarget=weave_mapを含む |
| pngExportAvailable | [[SCR-MW-VIEWER-MAIN-FRAME]].exportPngButton | 出力用renderableが作れる場合にPNG出力操作を有効にする | | N | implemented。図本体のみを派生出力する |
| pngExportAvailable | [[SCR-MW-VIEWER-MAIN-FRAME]].exportAndOpenPngButton | PNG出力後にOS側で開く操作を有効にする | | N | implemented。[[PROC-MW-VIEWER-EXPORT-PNG]] でopenExportedPngへ続く |

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
- object / dfd-object / domains / domain-diagram はsplit shellのbottomPaneを使い、diagramは専用lower slotsを使う。
- color_scheme と relations は現行実装では標準Review Panelを使わず、内容を直接表示する。
- Mermaid Source / Render DebugはMermaid sourceを生成する表示に限って扱い、Mermaid sourceを持たないcustom-only表示へ強制しない。
- PNG exportはtoolbarや下部Review Panelを含めず、描画済み図本体の派生出力として扱う。
- Export and OpenのOS側open可否はdesktop adapterとElectron shellに依存するため、Viewer Stateの保存項目としては扱わない。
- Focus modeとView OnlyはViewerの一時UI状態であり、保存設定やMarkdown正本を変更しない。

## Notes
- 実装上の `ModelingPreviewView.state` の更新に連動して、本マッピングに基づき UI が再描画される。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/views/modeling-preview-view.ts | ModelingPreviewView | class | `state` プロパティが UI へ反映される起点 |
| src/views/modeling-preview-view.ts | renderReviewSummaryPanel | method | Review Summary chip群を表示 |
| src/views/modeling-preview-view.ts | createCollectionDiagramLowerPaneSlots | method | 下部Review Panel slotsを構築 |
| src/views/modeling-preview-view.ts | renderObjectState | method | object modeの下部表示を構成 |
| src/views/modeling-preview-view.ts | renderSummaryState | method | summary modeのscreen preview / Business Flow下部表示を構成 |
| src/views/modeling-preview-view.ts | renderDomainsState | method | domains modeの下部表示を構成 |
| src/views/modeling-preview-view.ts | renderDomainDiagramState | method | domain_diagram modeの下部表示を構成 |
| src/views/modeling-preview-view.ts | renderColorSchemeState | method | color_scheme modeを直接表示 |
| src/views/modeling-preview-view.ts | renderDfdObjectState | method | dfd-object modeの下部表示を構成 |
| src/views/modeling-preview-view.ts | renderDiagramState | method | diagram modeのlower slotsを構成 |
| src/views/modeling-preview-view.ts | renderRelationsState | method | relations modeを直接表示 |
| src/views/modeling-preview-view.ts | moveDetailSections | method | details sectionを下部領域へ移動 |
| src/views/modeling-preview-view.ts | appendViewerFocusToolbar | method | Focus mode操作を表示 |
| src/views/modeling-preview-view.ts | setViewOnlyMode | method | View Only一時状態を反映 |
| src/core/render-mode.ts | resolveRenderMode | function | モード選択肢の解決 |
| src/core/relation-resolver.ts | resolveDiagramRelations | function | プレビュー用データの生成 |
| src/renderers/dfd-mermaid.ts | renderDfdMermaidDiagram | function | Mermaid 描画の実行 |
| src/views/modeling-preview-view.ts | updateAppProcessFlowConnectButton | method | Flow接続モード状態をボタンへ反映 |
| src/views/modeling-preview-view.ts | createAppProcessFlowConnectStatus | method | 接続元Step選択状態を表示 |
| src/core/app-process-flow-editor.ts | addAppProcessFlow | function | app_process `## Flows` 更新結果を生成 |
| src/core/app-process-step-interaction-target.ts | resolveAppProcessStepInteractionTarget | function | Business Flow Stepの参照操作先を解決 |
| src/views/mermaid-node-interactions.ts | attachMermaidNodeInteractions | function | Business Flow SVGノードへhover / click targetを付与 |
| src/renderers/app-process-business-flow.ts | getStepShapeKind | function | Business Flow Step kindからshapeを選ぶ |
| src/renderers/app-process-business-flow.ts | addAppProcessStepStyle | function | Business Flow Step kindをColor Scheme target=app_processへ渡す |
| src/views/modeling-preview-view.ts | renderImpactSummarySection | method | Relationship Viewの表示 |
| src/views/modeling-preview-view.ts | renderWeaveMapBlock | method | Relationship View内Weave Mapの表示 |
| src/views/modeling-preview-view.ts | renderAppliedColorScheme | method | Applied Color Scheme sectionの表示 |
| src/core/color-scheme.ts | getAppliedColorSchemeRowsForTargets | function | Applied Color Scheme表示行を構築 |
| src/renderers/mermaid-shared.ts | renderMermaidSourceIntoShell | function | Mermaid Source / Render Debug表示 |
| src/core/impact-analyzer.ts | formatImpactSummaryAsMarkdown | function | Copy Impact Summaryの文字列生成 |
| src/renderers/weave-map-mermaid.ts | buildWeaveMapMermaidSource | function | Weave Map Mermaid source生成 |
| src/renderers/weave-map-mermaid.ts | resolveLayerStyle | function | target=weave_mapのlayer color override |
| src/export/png-export.ts | exportDiagramRenderableAsPng | function | PNG派生出力 |
| src/export/png-export.ts | buildDomDiagramExportSnapshot | function | export surfaceとboundsを取得 |
| src/views/modeling-preview-view.ts | buildCurrentDiagramExportRenderable | method | pngExportAvailable相当の出力用renderableを準備 |
| src/views/modeling-preview-view.ts | openExportedPng | method | PNG出力後にOS側で開く |
| src/renderers/zoom-toolbar.ts | createZoomToolbar | function | PNG出力ボタンとExport and Openボタンを表示 |
