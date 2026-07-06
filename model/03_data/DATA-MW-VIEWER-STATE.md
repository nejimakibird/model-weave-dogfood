---
type: data_object
id: DATA-MW-VIEWER-STATE
name: Viewer内部状態
kind: state
data_format: object
tags:
  - Viewer
  - Runtime
  - State
---

# Viewer内部状態

## Summary
Viewer の実行時における現在の表示内容、レンダリング状態、Viewer表示設定を保持する内部データオブジェクト。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| currentModelName | 現在モデル名 | string | | N | currentModel.name | | partial。PreviewState の model / title / filePath から表示名として導出される概念項目 |
| supportedRenderModes | サポート描画モード | string | | N | rendererSelection.supportedModes | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | implemented / derived。RendererSelectionState.supportedModes。getSupportedRenderModes 由来の論理的な複数値 |
| effectiveRenderMode | 実効描画モード | string | | N | rendererSelection.effectiveMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented。src/core/render-mode.ts の resolveRenderMode により決定される実効描画モード |
| businessFlowDirection | Business Flow表示方向 | string | | N | resolvedBusinessFlowDirection | | implemented / derived。app_process Business Flowの実効方向。toolbar override、frontmatter flow_direction、viewerPreferences.defaultBusinessFlowDirection から解決される |
| businessFlowConnectMode | Flow接続モード | boolean | | N | appProcessConnectFlowState | | implemented。app_process Business Flow限定の一時Viewer状態。DFDやClass / ERの汎用接続編集ではない |
| businessFlowConnectFilePath | Flow接続対象パス | string | | N | appProcessFlowConnectFilePath | [[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]] | implemented / runtime。接続モードの対象app_processファイル |
| businessFlowConnectSourceStepId | Flow接続元Step ID | string | | N | appProcessFlowConnectSourceStepId | [[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]] | implemented / runtime。接続元として選択中のSteps.id |
| businessFlowInteractionTargets | Business Flow参照操作先 | GraphInteractionTarget list | | N | buildAppProcessBusinessFlowInteractionTargets | [[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]] | implemented / derived。Business Flow Stepのhover preview / click target |
| viewOnlyMode | View Only状態 | boolean | | N | viewOnlyEnabled | | implemented。Preview内の表示専用モード。Business FlowのConnect Flow切替後も保持される |
| focusMode | Focus Mode状態 | boolean | | N | focusModeEnabled | | implemented。Viewer全体の集中表示モード。Escapeで解除される |
| viewOnlyTarget | View Only対象 | object | | N | viewOnlyTarget | | implemented / runtime。View Only stageへ一時的に移動する描画済み図のDOM対象 |
| stepInteractionTarget | Step参照操作先 | object | | N | businessFlowStepTarget | | implemented / derived。app_process Steps / Inputs / Outputs からhover previewやclick targetを解決する |
| previewContent | プレビュー内容 | object | | N | state.model | | partial。PreviewState の mode ごとの model / diagram / context / summary情報をUIマッピング上で集約する概念項目 |
| reviewSummary | Review Summary表示状態 | object | | N | renderReviewSummaryPanel | | implemented / derived。model名、model type、diagnostics件数、relationship件数、Source Links件数、Weave Map可否のchip表示 |
| reviewPanelSections | Review Panel表示セクション | string list | | N | bottomPane / lowerSlots | | implemented / derived。Review Summary、Diagnostics、Relationships / Impact、Source Links、Weave Map、Details、Mermaid Sourceなどの下部表示領域 |
| modeLowerPaneLayout | mode別下部ペイン構成 | object | | N | renderCurrentState switch | | implemented / derived。PreviewState.modeごとのrender methodが選ぶbottomPane、lowerSlots、またはlower paneなしの構成 |
| previewDetails | Preview詳細表示 | object | | N | moveDetailSections | | implemented / derived。object details、diagram details、connection detailsなどの下部details表示 |
| sourceLinks | Source Links表示入力 | SourceLink list | | N | model.sourceLinks | [[DATA-MW-SOURCE-LINK]] | implemented。Preview内Source Links sectionの表示とCopy Path / Open操作に使う |
| mermaidSourceVisible | Mermaid Source表示有無 | boolean | | N | renderMermaidSourceIntoShell | [[DATA-MW-RENDERER-MERMAID-SOURCE]] | implemented / derived。Mermaid sourceを生成する表示では下部パネルへ表示される |
| pngExportAvailable | PNG出力可能状態 | boolean | | N | buildCurrentDiagramExportRenderable | [[PROC-MW-VIEWER-EXPORT-PNG]] | implemented / derived。出力用renderableを作れる描画済み図があるかどうか |
| diagnostics | 診断情報一覧 | DATA-MW-CORE-DIAGNOSTIC | | N | state.warnings | [[DATA-MW-CORE-DIAGNOSTIC]] | implemented。PreviewState.warnings と renderDiagnostics で表示される診断情報 |
| renderStatus | 描画状態 | string | | Y | state.mode | | partial。PreviewState.mode に近い描画状態の概念項目。実装のmode名と完全一致とは断定しない |
| renderMessage | 描画状態メッセージ | string | | N | state.message | | implemented / partial。empty / error 等のPreviewState.messageに対応する状態メッセージ |
| activeFilePath | アクティブファイルパス | string | | N | getCurrentFilePath | | implemented。ModelingPreviewView.getCurrentFilePath で表示対象ファイルを取得 |
| autoFitEnabled | Auto Fit有効 | boolean | | N | autoFitEnabled | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | planned / partial。auto fit挙動は実装済みだが、現行ModelWeaveSettingsの設定項目としては未実装 |
| preserveViewportState | Viewport状態保持 | boolean | | N | preserveViewportState | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | planned / partial。viewportStateCache による保持挙動はあるが、現行ModelWeaveSettingsの設定項目としては未実装 |
| diagnosticsVisible | 診断パネル表示 | boolean | | N | diagnosticsVisible | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | planned。診断パネル表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| statusBarVisible | ステータスバー表示 | boolean | | N | statusBarVisible | [[SCR-MW-VIEWER-STATUS-BAR]] | planned。ステータスバー表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| defaultBusinessFlowDirection | Business Flow既定方向 | string | | N | viewerPreferences.defaultBusinessFlowDirection | | implemented。保存設定からViewerPreferencesへ渡され、Business Flow方向解決に使われる |
| defaultDomainsViewMode | Domains既定表示モード | string | | N | viewerPreferences.defaultDomainsViewMode | | implemented。domains表示の既定モードとしてViewerへ渡される |
| defaultDomainDiagramViewMode | Domain Diagram既定表示モード | string | | N | viewerPreferences.defaultDomainDiagramViewMode | | implemented。domain_diagram表示の既定モードとしてViewerへ渡される |
| fontSize | Viewer文字サイズ | string | | N | viewerPreferences.fontSize | | implemented。src/settings/model-weave-settings.ts に実装済み。ModelingPreviewView.viewerPreferences からCSS class / CSS variablesへ反映 |
| nodeDensity | ノード密度 | string | | N | viewerPreferences.nodeDensity | | implemented。src/settings/model-weave-settings.ts に実装済み。ModelingPreviewView.viewerPreferences から表示密度のCSS class / spacingへ反映 |
| localSourceRoot | ローカルソースルート | string | | N | viewerPreferences.localSourceRoot | | implemented。Source Linksのrelative path解決に使われる |
| relationshipViewEnabled | Relationship View有効状態 | boolean | | N | settings.enableRelationshipView | [[DATA-MW-PLUGIN-SETTINGS]].enableRelationshipView | implemented。Impact Summary生成とRelationship View表示準備のゲート |
| impactSummary | Impact Summary | DATA-MW-IMPACT-SUMMARY | | N | state.impactSummary | [[DATA-MW-IMPACT-SUMMARY]] | implemented / derived。現在のmodelとVault Indexから生成されるRelationship View入力 |
| weaveMapMermaidSource | Weave Map Mermaidソース | string | | N | state.weaveMapMermaidSource | [[DATA-MW-RENDERER-MERMAID-SOURCE]] | implemented / derived。ImpactSummaryから生成されるRelationship View内のWeave Map表示用ソース |
| resolvedColorScheme | 解決済みColor Scheme | object | | N | renderContext.colorScheme | | implemented / derived。defaultColorSchemeRef から描画時に解決される配色。未設定またはfallback時は組み込み配色を使う |
| appliedColorSchemeTargets | Applied Color Scheme対象 | string list | | N | renderAppliedColorScheme.targets | | implemented / derived。表示中viewからdomain / dfd / app_process / weave_mapなどの対象を決める。空の場合はsectionを表示しない |
| appliedColorSchemeRows | Applied Color Scheme表示行 | AppliedColorSchemeRow list | | N | getAppliedColorSchemeRowsForTargets | | implemented / derived。現在の対象に対するconfiguredまたはbuilt-inの表示行。source列で由来を区別する |
| showMermaidRenderDebug | Mermaid描画デバッグ表示 | boolean | | N | viewerPreferences.showMermaidRenderDebug | | implemented。Mermaid描画診断表示に使われる |
| uiLanguage | UI言語 | string | | N | viewerPreferences.uiLanguage | | implemented。Viewer内i18nとSource Links表示言語に使われる |
| viewerFontScale | Viewer文字サイズ倍率 | string | | N | viewerFontScale | | planned。dogfood先行項目。現行ModelWeaveSettingsには未実装。fontSize との関係整理が後続課題 |
| mermaidThemeMode | Mermaidテーマモード | string | | N | mermaidThemeMode | | planned。Mermaidテーマ制御の将来設定。現行ModelWeaveSettingsには未実装 |
| sourceLinksEnabled | Source Links有効 | boolean | | N | sourceLinksEnabled | | future / non-source-backed。現行ModelWeaveSettingsには存在しない。Preview内Source Links sectionはsourceLinksEnabledではgatingされない |

## Notes
- 現行実装側のViewer状態は src/views/modeling-preview-view.ts の PreviewState / ModelingPreviewView を正とする。
- 現行実装側のViewer表示設定は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS を正とする。
- この data_object は現行実装済みViewer状態とdogfood先行設計項目を含むため、全体としては partial と扱う。
- dogfood側のみの項目は、現時点でModel Weave本体に実装済みとは断定しない。
- GraphModel や MermaidSource などの具体的な描画データは、UIマッピング上は `previewContent` に集約して扱う。
- `effectiveRenderMode` は src/core/render-mode.ts の resolveRenderMode と [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] / [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に従う。
- `businessFlowDirection` は app_process Business Flowだけの実効表示方向であり、DFD / Class / ER の汎用グラフ方向設定ではない。
- `businessFlowConnectMode` / `businessFlowConnectFilePath` / `businessFlowConnectSourceStepId` は app_process Business Flowの `## Flows` 更新支援に使う一時Viewer状態である。
- `businessFlowInteractionTargets` と `stepInteractionTarget` は app_process Business Flow の参照確認支援であり、汎用ノードエディタやDFD接続編集としては扱わない。
- ref-aware Business Flow step hover / click targetは `Steps.input` / `Steps.output` / `Steps.rule` / `Steps.invoke` / `Steps.screen` と `Inputs` / `Outputs` の行を使って解決される。
- `focusMode` と `viewOnlyMode` はViewerの一時UI状態であり、ModelWeaveSettingsやMarkdownモデル本文へ保存しない。
- `reviewSummary` と `reviewPanelSections` は下部Review Panelを組み立てる派生表示状態であり、保存データではない。
- `modeLowerPaneLayout` は `PreviewState.mode` と各 `render*State` methodから決まる派生UI構成であり、保存設定やMarkdown正本ではない。
- object / dfd-object / domains / domain-diagramはsplit shellのbottomPane、diagramは専用lower slots、color_scheme / relationsは標準Review Panelなしとして扱う。
- `mermaidSourceVisible` はMermaid sourceを生成する表示に限られる。Mermaid sourceを持たないcustom-only表示には無理に表示しない。
- `pngExportAvailable` は現在の描画結果から判定される派生状態であり、PNGファイルはMarkdown正本ではなく派生出力である。
- PNG出力先パスやOpen after exportの成否はViewer Stateへ保存されず、[[PROC-MW-VIEWER-EXPORT-PNG]] の操作結果とNoticeとして扱う。
- `defaultBusinessFlowDirection` / `defaultDomainsViewMode` / `defaultDomainDiagramViewMode` は保存設定からViewerPreferencesへ渡される表示設定である。
- `relationshipViewEnabled` は保存設定から来る表示ゲートであり、Impact Summaryそのものは `impactSummary` として現在のmodelとVault Indexから生成される。
- `impactSummary` と `weaveMapMermaidSource` は保存データではなく、Preview更新時に作られる派生表示状態である。
- `resolvedColorScheme` は defaultColorSchemeRef から描画時に解決される派生状態であり、現行 getViewerPreferences の戻り値ではない。
- `appliedColorSchemeTargets` と `appliedColorSchemeRows` はViewer表示用の派生状態であり、保存設定やMarkdownモデル本文ではない。
- defaultColorSchemeRefが空の場合は組み込み配色を使う。参照が解決できない場合やcolor_scheme以外を指す場合もwarningを出し、組み込み配色へfallbackする。
- Applied Color Scheme sectionは、resolvedColorSchemeがあり、かつview targetがある場合に表示される。対象がないviewでは表示しない。
- DFDでは `target=dfd` と必要に応じて `target=domain`、app_process Business Flowでは `target=app_process` と `target=domain`、Relationship / Weave Mapでは `target=weave_map` が表示対象になる。
- Weave MapはRelationship View内の派生視覚化であり、`type: weave_map` のような専用Markdown形式として扱わない。
- Weave MapのColor Scheme適用は `target=weave_map` のlayer coloringとして描画結果に反映され、Markdown正本を書き換えない。global kindやglobal defaultはWeave Map layer既定色の置き換えには使わない。
- Applied Color Scheme sectionはViewer表示であり、PNG exportにはsection自体ではなく配色反映済み図本体だけが含まれる。
- `defaultZoom` 由来の表示状態は [[DATA-MW-VIEWPORT-STATE]] の zoom / scale / fit 状態として扱う。
- fontSize / nodeDensity は現行実装済み設定であり、ModelingPreviewView.viewerPreferences からViewerのCSS class / CSS variablesへ反映される。
- viewerFontScale はdogfood先行項目であり、fontSize との関係整理は後続課題とする。
- diagnosticsVisible / statusBarVisible はplannedの表示制御であり、内部状態や診断生成そのものを停止するものではない。
- `## Source Links` の解析、Preview内表示、Copy Path / Open、Relationship summary内のRelated Source Links集約は現行実装済みとして扱う。
- sourceLinksEnabled は Source Links Explorer / Source Links索引化向けのfutureまたは非source-backed項目であり、現行実装済み設定とは扱わない。
- Preview内Source Linksのrelative path解決には ModelWeaveSettings.localSourceRoot が使われる。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | symbol: PreviewState; kind: type; Viewer内部状態 |
| src/views/modeling-preview-view.ts | symbol: RendererSelectionState; kind: interface; supportedRenderModes / effectiveRenderMode の実装側状態 |
| src/views/modeling-preview-view.ts | symbol: ModelingPreviewView; kind: class; stateとviewerPreferencesを保持しUIへ反映するViewer |
| src/views/modeling-preview-view.ts | symbol: applyViewerSettings; kind: method; fontSize / nodeDensity などViewer表示設定の反映 |
| src/views/modeling-preview-view.ts | symbol: renderCurrentState; kind: method; Viewer状態の描画反映 |
| src/views/modeling-preview-view.ts | symbol: renderReviewSummaryPanel; kind: method; Review Summary表示状態の構築 |
| src/views/modeling-preview-view.ts | symbol: createCollectionDiagramLowerPaneSlots; kind: method; Review Panel下部slotsの構築 |
| src/views/modeling-preview-view.ts | symbol: renderObjectState; kind: method; object modeのsplit shell下部構成 |
| src/views/modeling-preview-view.ts | symbol: renderSummaryState; kind: method; summary modeのscreen preview / Business Flow構成 |
| src/views/modeling-preview-view.ts | symbol: renderDomainsState; kind: method; domains modeの下部構成 |
| src/views/modeling-preview-view.ts | symbol: renderDomainDiagramState; kind: method; domain_diagram modeの下部構成 |
| src/views/modeling-preview-view.ts | symbol: renderColorSchemeState; kind: method; color_scheme modeの直接表示 |
| src/views/modeling-preview-view.ts | symbol: renderDfdObjectState; kind: method; dfd-object modeのsplit shell下部構成 |
| src/views/modeling-preview-view.ts | symbol: renderDiagramState; kind: method; diagram modeのlower slots構成 |
| src/views/modeling-preview-view.ts | symbol: renderRelationsState; kind: method; relations modeの直接表示 |
| src/views/modeling-preview-view.ts | symbol: appendViewerFocusToolbar; kind: method; focusMode UIの構築 |
| src/views/modeling-preview-view.ts | symbol: setViewOnlyMode; kind: method; View Only一時状態の制御 |
| src/renderers/mermaid-shared.ts | symbol: renderMermaidSourceIntoShell; kind: function; Mermaid Source / Render Debug表示 |
| src/export/png-export.ts | symbol: exportDiagramRenderableAsPng; kind: function; PNG派生出力 |
| src/views/modeling-preview-view.ts | symbol: buildCurrentDiagramExportRenderable; kind: method; pngExportAvailable相当の出力対象を準備する |
| src/views/modeling-preview-view.ts | symbol: openExportedPng; kind: method; Export and OpenのOS側open処理 |
| src/main.ts | symbol: ModelWeavePlugin.getViewerPreferences; kind: method; 保存設定からViewerPreferencesを構成 |
| src/settings/model-weave-settings.ts | symbol: ModelWeaveSettings; kind: type; 現行実装済み設定項目 |
| src/settings/model-weave-settings.ts | symbol: DEFAULT_MODEL_WEAVE_SETTINGS; kind: constant; defaultZoom / fontSize / nodeDensity の既定値 |
| src/settings/model-weave-settings.ts | symbol: ModelWeaveViewerPreferences; kind: type; Viewerへ渡される表示設定 |
| src/core/render-mode.ts | symbol: resolveRenderMode; kind: function; effectiveRenderMode の解決 |
| src/core/app-process-business-flow-direction.ts | symbol: resolveAppProcessBusinessFlowDirection; kind: function; Business Flow directionの解決 |
| src/core/color-scheme.ts | symbol: resolveDefaultColorScheme; kind: function; defaultColorSchemeRef の解決 |
| src/core/color-scheme.ts | symbol: AppliedColorSchemeRow; kind: interface; Applied Color Scheme表示行 |
| src/core/color-scheme.ts | symbol: getAppliedColorSchemeRowsForTargets; kind: function; Applied Color Scheme表示行の構築 |
| src/views/applied-color-scheme-renderer.ts | symbol: renderAppliedColorSchemeSectionContent; kind: function; Applied Color Scheme sectionの表示 |
| src/renderers/weave-map-mermaid.ts | symbol: resolveLayerStyle; kind: function; target=weave_mapのlayer color overrideとfallback |
| src/core/impact-analyzer.ts | symbol: buildImpactSummary; kind: function; Impact Summary生成 |
| src/renderers/app-process-business-flow.ts | symbol: buildAppProcessBusinessFlowInteractionTargets; kind: function; ref-aware Business Flow step hover / click targetの構築 |
| src/views/modeling-preview-view.ts | symbol: handleAppProcessFlowConnectStepClick; kind: method; Flow接続モードのStepクリック処理 |
| src/core/app-process-flow-editor.ts | symbol: addAppProcessFlow; kind: function; app_process `## Flows` 行の追加と結果判定 |
| src/core/app-process-step-interaction-target.ts | symbol: resolveAppProcessStepInteractionTarget; kind: function; Business Flow Stepの参照操作先を解決 |
| src/views/mermaid-node-interactions.ts | symbol: attachMermaidNodeInteractions; kind: function; Business Flow Stepのhover preview / click targetをSVGへ付与 |
| src/renderers/source-links-renderer.ts | symbol: renderSourceLinks; kind: function; Source Links sectionのPreview表示とCopy Path / Open操作 |
| src/core/impact-analyzer.ts | symbol: collectRelatedSourceLinks; kind: function; Relationship / impact summaryに関連Source Linksを集約 |
| src/core/weave-map.ts | symbol: buildWeaveMapModel; kind: function; ImpactSummaryからWeave Map派生モデルを生成 |
| src/renderers/weave-map-mermaid.ts | symbol: buildWeaveMapMermaidSource; kind: function; Weave Map Mermaid source生成とlayer coloring |
