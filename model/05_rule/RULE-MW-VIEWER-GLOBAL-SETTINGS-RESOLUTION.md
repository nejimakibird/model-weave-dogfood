---
type: rule
id: RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION
name: Viewerグローバル設定解決ルール
kind: settings_rule
tags:
  - ModelWeave
  - Viewer
  - Settings
  - Rule
---

# Viewerグローバル設定解決ルール

## Summary

Model Weaveプラグイン設定から、Viewer / Renderer / Viewport が実際に使用する実効設定値を解決するルールを定義する。
未設定または不正値がある場合は、安全な既定値へフォールバックする。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| pluginSettings | [[DATA-MW-PLUGIN-SETTINGS]] | Settings | Y | Obsidianプラグイン設定 |
| viewerState | [[DATA-MW-VIEWER-STATE]] | Viewer | N | Viewer実行時状態 |
| viewportState | [[DATA-MW-VIEWPORT-STATE]] | Viewer | N | Viewport表示状態 |
| renderModeInput | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | Renderer | N | レンダリングモード解決入力 |
| renderModeRule | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | Rule | Y | render modeの解決方針 |
| dfdMermaidOnlyRule | [[RULE-MW-DFD-MERMAID-ONLY]] | Rule | N | DFDのMermaid固定方針 |
| zoomLimitRule | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | Rule | Y | zoom値の制約 |
| autoFitRule | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | Rule | N | auto fit方針 |
| viewportPersistenceRule | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | Rule | N | viewport状態保持方針 |

## Conditions

- 現行のrender mode既定値は defaultClassRenderMode / defaultErRenderMode / defaultDfdRenderMode / defaultProcessRenderMode / defaultScreenRenderMode に分かれる。
- `defaultRenderMode` は旧設定名であり、normalizeModelWeaveSettings が class / ER のlegacy補助入力としてのみ扱う。現行の主設定ではない。
- render modeの優先順位は toolbar override、frontmatter render_mode、settings default、format default / fallback の順に扱う。詳細は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に委ねる。
- DFDでは `[[RULE-MW-DFD-MERMAID-ONLY]]` により `mermaid` 固定を優先し、DFDをClass / ERのrenderer switchingと混同しない。
- `defaultBusinessFlowDirection` は app_process Business Flow のsettings defaultであり、toolbar override、frontmatter flow_direction、settings default、fallback LR の順に実効方向を解決する。
- `defaultDomainsViewMode` / `defaultDomainDiagramViewMode` は domains / domain_diagram の既定表示モードであり、mindmap / area / tree の範囲で正規化される。
- `defaultColorSchemeRef` は color_scheme へのvault refまたはpathであり、解決できない場合は組み込み配色へフォールバックする。
- `defaultColorSchemeRef` は保存設定であり、ViewerPreferencesへの単純転記ではなく、描画時に resolvedColorScheme として解決される。
- Applied Color Scheme sectionは resolvedColorScheme とview targetから表示行を作るViewer表示であり、Markdown本文やfrontmatterを書き換えない。colorSchemeがない場合、またはview targetが空の場合は表示しない。
- `defaultZoom` は src/settings/model-weave-settings.ts に実装済みの設定であり、`fit` の場合は初回表示時に `fitToView` を実行する。
- `defaultZoom` が `100` の場合、Viewport側の `zoom=1.0` / `scale=1.0` 相当を初期値とする。
- `defaultZoom` が不正値の場合、安全側として `fit` または `1.0` にフォールバックする。
- `fontSize` は src/settings/model-weave-settings.ts に実装済みの設定であり、Viewer表示サイズに影響する。具体的な反映は ModelingPreviewView の viewerPreferences と [[DATA-MW-VIEWER-STATE]] に委ねる。
- `nodeDensity` は src/settings/model-weave-settings.ts に実装済みの設定であり、ノード密度・表示間隔に影響する。具体的な反映は ModelingPreviewView の viewerPreferences と [[DATA-MW-VIEWER-STATE]] に委ねる。
- `localSourceRoot` は Source Links のrelative path解決に使う実装済み設定である。
- `uiLanguage` は auto / en / ja に正規化され、autoの場合はObsidian言語に従う。
- `showMermaidRenderDebug` はMermaid描画診断表示に使う実装済み設定である。
- `enableRelationshipView` は Relationship View / Impact Summary / Weave Map の生成と表示準備を制御する実装済み設定である。
- `enableRelationshipView` がfalseの場合、Impact Summary生成、Copy Impact Summary handler、Weave Map表示準備を抑制する。
- `autoFitOnOpen` が `true` の場合、[[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] に従う。ただし、この設定項目は現行ModelWeaveSettingsには未実装のplanned項目として扱う。
- `preserveViewportState` が `true` の場合、[[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] に従い、ファイル単位のviewport状態を復元してよい。ただし、この設定項目は現行ModelWeaveSettingsには未実装のplanned / partial項目として扱う。
- `diagnosticsVisible` が `false` の場合でも、内部診断の生成自体は継続してよい。表示（UIパネル）のみ抑制する。ただし、この設定項目は現行ModelWeaveSettingsには未実装のplanned項目として扱う。
- `statusBarVisible` が `false` の場合、ステータスバーUIのみ非表示にし、Viewer状態（renderStatus等）自体は維持する。ただし、この設定項目は現行ModelWeaveSettingsには未実装のplanned項目として扱う。
- `enableSourceLinks` は Source Links Explorer / Source Links索引化が future のため、現行実装済み設定として扱わない。
- App Process Flow Connect Mode とref-aware Business Flow step hover / click targetは app_process Business Flow 限定のViewer一時状態であり、保存設定として扱わない。
- 設定解決はViewerの一時状態へ反映され、Markdown本文やfrontmatterは変更しない。
- Settings Tabで変更された保存設定は updateSettings で正規化と保存を行い、開いているPreviewへ再反映される。

## Parameters

| name | value | notes |
|---|---|---|
| fallbackDfdRenderMode | mermaid | DFDのformat default |
| fallbackBusinessFlowDirection | LR | Business Flow directionのfallback |
| legacyDefaultRenderModeScope | class / ER | defaultRenderModeは旧設定の補助入力 |
| fallbackZoomMode | fit | defaultZoom不正時 |
| defaultScale | 1.0 | 100%表示 |
| fontSizeDefault | normal | implemented。DEFAULT_MODEL_WEAVE_SETTINGS.fontSize の既定値 |
| nodeDensityDefault | normal | implemented。DEFAULT_MODEL_WEAVE_SETTINGS.nodeDensity の既定値 |
| domainsViewModeDefault | mindmap | implemented。domains / domain_diagram表示モードの既定値 |
| colorSchemeFallback | built-in defaults | defaultColorSchemeRefが未設定または解決不能な場合 |
| preserveViewportDefault | true | planned / partial。現行ModelWeaveSettingsには未実装の先行設計値 |
| diagnosticsVisibleDefault | true | planned。現行ModelWeaveSettingsには未実装の先行設計値 |
| statusBarVisibleDefault | true | planned。現行ModelWeaveSettingsには未実装の先行設計値 |

## Messages

| condition | message | severity | notes |
|---|---|---|---|
| invalid render mode setting | Invalid render mode setting. Fallback applied. | warning | 形式別render mode設定またはfrontmatter render_modeが不正 |
| invalid business flow direction | Invalid Business Flow direction. Fallback applied. | warning | flow_directionまたはdefaultBusinessFlowDirectionが不正 |
| invalid zoom setting | Invalid zoom setting. Fallback applied. | warning | defaultZoomが不正 |
| settings fallback applied | Viewer settings fallback applied. | info | 既定値を使用 |
| color scheme fallback applied | Color Scheme fallback applied. | warning | defaultColorSchemeRefが未解決またはcolor_scheme以外を指す場合 |

## Notes

- 本ルールはObsidianプラグイン設定からViewer実行時の実効値を解決する。
- 現行実装側の設定項目は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS を正とする。
- この rule は現行実装済み設定とdogfood先行設計項目を含むため、全体としては partial と扱う。
- dogfood側のみの設定項目は、現時点でModel Weave本体に実装済みとは断定しない。
- 実際の設定保存はObsidianプラグイン設定に委ねる。
- Renderer選択の詳細は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に委ねる。
- render mode の詳細解決は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に委ねる。
- auto は現行のselected render modeとして扱わず、旧frontmatterや旧設定の互換入力としてのみ扱う。
- Business Flow direction は app_process Business Flow の表示方向であり、`flow_direction` と `render_mode` は別設定である。
- App Process Flow Connect Mode は `## Flows` 更新を支援する入力モードであり、DFD / Class / ER の接続編集や汎用ノードエディタではない。
- ref-aware Business Flow step hover / click target は app_process の Steps / Inputs / Outputs 参照に限定して扱う。
- zoom / pan / fit の表示状態は [[DATA-MW-VIEWPORT-STATE]] に委ねる。
- Viewport初期化と状態復元は [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] および [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] に委ねる。ただし autoFitOnOpen / preserveViewportState は現行ModelWeaveSettingsには未実装の先行設計項目である。
- fontSize / nodeDensity は現行実装済み設定だが、Viewer State / Renderer側の反映モデルは後続で整理する場合がある。
- Applied Color Scheme sectionは domains / domain_diagram、DFD diagram、app_process Business Flow、Relationship / Weave Mapなど、表示中viewのtargetに応じた配色行を表示する。表示行はconfiguredとbuilt-inの由来を区別する。
- Weave MapはRelationship View内の派生表示であり、defaultColorSchemeRefから解決したColor Schemeをlayer coloringへ渡す。
- Weave Map layer coloringは `target=weave_map` のtarget-specific行を使う。global kindやglobal defaultはWeave Map layer既定色を置き換えない。
- PNG exportは配色反映済みの図本体を派生出力するが、Applied Color Scheme sectionやlower paneは出力対象に含めない。
- Impact Summary / Relationship View / Weave Mapは保存設定やMarkdown正本ではなく、Preview更新時の派生表示として扱う。
- mermaidThemeMode / viewerFontScale / diagnosticsVisible / statusBarVisible はplanned項目として扱い、現行実装済みとは断定しない。
- enableSourceLinks は Source Links Explorer / Source Links索引化が future のため、現行実装済みとは扱わない。
- Source Links section support は解析、Preview表示、Copy Path、Open actionを現行実装として扱うが、Source Links Explorer indexing / filtering / selectionとは区別する。
- 本ルールはMarkdownモデルの内容を変更しない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/settings/model-weave-settings.ts | ModelWeaveSettings | type | プラグイン設定構造 |
| src/settings/model-weave-settings.ts | DEFAULT_MODEL_WEAVE_SETTINGS | constant | 既定設定値 |
| src/settings/model-weave-settings.ts | normalizeModelWeaveSettings | function | 設定値の正規化 |
| src/core/render-mode.ts | resolveRenderMode | function | render mode実効値の解決 |
| src/core/app-process-business-flow-direction.ts | resolveAppProcessBusinessFlowDirection | function | Business Flow directionの解決 |
| src/core/color-scheme.ts | resolveDefaultColorScheme | function | defaultColorSchemeRef の解決 |
| src/core/color-scheme.ts | getAppliedColorSchemeRowsForTargets | function | Applied Color Scheme表示行の構築 |
| src/main.ts | ModelWeavePlugin.updateSettings | method | 設定変更の正規化、保存、Preview更新 |
| src/main.ts | ModelWeavePlugin.refreshOpenModelWeaveViews | method | 開いているPreviewへの設定再反映 |
| src/main.ts | ModelWeavePlugin.buildImpactPreviewProps | method | enableRelationshipViewに基づきImpact SummaryとWeave Map propsを準備 |
| src/views/modeling-preview-view.ts | applyViewerSettings | method | fontSize / nodeDensity などViewer表示設定の反映 |
| src/views/modeling-preview-view.ts | renderAppliedColorScheme | method | Applied Color Scheme sectionの表示 |
| src/views/modeling-preview-view.ts | renderImpactSummarySection | method | Relationship Viewの表示 |
| src/views/modeling-preview-view.ts | renderWeaveMapBlock | method | Weave Mapの表示 |
| src/views/applied-color-scheme-renderer.ts | renderAppliedColorSchemeSectionContent | function | Applied Color Schemeのsummaryとtable表示 |
| src/renderers/weave-map-mermaid.ts | resolveLayerStyle | function | target=weave_mapのlayer color override |
| src/views/modeling-preview-view.ts | prepareFileViewportState | method | zoom / viewport初期状態の解決 |
| src/renderers/graph-view-shared.ts | GraphViewportState | interface | zoom / pan / fit 状態 |
