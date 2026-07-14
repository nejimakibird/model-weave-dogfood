---
type: screen
id: SCR-MW-SETTINGS-TAB
name: Model Weave設定タブ
screen_type: settings
kind: settings_screen
tags:
  - ModelWeave
  - Settings
  - UI
---

# Model Weave設定タブ

## Summary

Model Weaveプラグインの設定値を表示・変更するObsidian設定タブのUIを定義する。
Renderer、Business Flow、Domains表示、Flow Diagram既定表示、Viewer、Relationship View、Mermaid debug、UI language、Local source root、Color Schemeに関する設定項目を扱う。

## Layout

| id | label | kind | purpose | notes |
|---|---|---|---|---|
| settings_root | 設定タブルート | container | 設定画面全体のコンテナ | |
| renderer_settings_area | Renderer設定領域 | section | 形式別の既定描画モード設定 | implemented。ModelWeaveSettingTab.display では Viewer heading 配下に表示される論理区分 |
| business_flow_settings_area | Business Flow設定領域 | section | app_process Business Flowの既定方向設定 | implemented。app_process Business Flow限定 |
| domain_view_settings_area | Domain表示設定領域 | section | domains / domain_diagram の既定表示モード設定 | implemented |
| viewport_settings_area | Viewport設定領域 | section | 既定ズーム設定 | implemented。Auto Fit / 状態保持の個別設定は現行UIにはない |
| viewer_ui_settings_area | Viewer UI設定領域 | section | フォント、ノード密度、Relationship View、Mermaid debug、UI言語の設定 | implemented |
| source_link_settings_area | Source Links設定領域 | section | Local source root の設定 | partial。Source Links有効化トグルではなく、relative Source Links 解決用の root を扱う |
| color_scheme_settings_area | Color Scheme設定領域 | section | 既定Color Scheme参照の設定 | implemented |
| settings_action_area | 設定操作領域 | section | 表示更新などの操作 | implemented。Reset操作は現行UIにはない |

## Fields

| id | label | kind | layout | data_type | required | ref | rule | notes |
|---|---|---|---|---|---|---|---|---|
| defaultClassRenderModeSelect | Class既定レンダリングモード | select | renderer_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultClassRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented。Default Class render mode。custom / mermaid / mermaid-detail |
| defaultErRenderModeSelect | ER既定レンダリングモード | select | renderer_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultErRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented。Default ER render mode。custom / mermaid / mermaid-detail |
| defaultDfdRenderModeSelect | DFD既定レンダリングモード | select | renderer_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultDfdRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented。Default DFD render mode。mermaid |
| defaultProcessRenderModeSelect | Process既定レンダリングモード | select | renderer_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultProcessRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented。Default Process render mode。custom |
| defaultBusinessFlowDirectionSelect | Business Flow既定方向 | select | business_flow_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultBusinessFlowDirection | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。Default Business Flow direction。LR / TD |
| defaultScreenRenderModeSelect | Screen既定レンダリングモード | select | renderer_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultScreenRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented。Default Screen render mode。custom |
| defaultDomainsViewModeSelect | Domains既定表示モード | select | domain_view_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultDomainsViewMode | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。Default Domains view mode。mindmap / area / tree |
| defaultDomainDiagramViewModeSelect | Domain Diagram既定表示モード | select | domain_view_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultDomainDiagramViewMode | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。Default Domain Diagram view mode。mindmap / area / tree |
| defaultFlowDiagramViewModeSelect | Flow Diagram既定表示モード | select | domain_view_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultFlowDiagramViewMode | [[RULE-MW-FLOW-DIAGRAM-VIEW-STATE]] | implemented。Default Flow Diagram view。detail / screen |
| defaultRenderModeSelect | 旧既定レンダリングモード | select | renderer_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | legacy / not current UI。normalizeModelWeaveSettings の移行補助として旧 defaultRenderMode は読まれるが、現行Settings Tabには単一selectとして表示されない |
| mermaidThemeModeSelect | Mermaidテーマモード | select | renderer_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].mermaidThemeMode | | planned。現行ModelWeaveSettingsには未実装 |
| defaultZoomSelect | 既定ズーム | select | viewport_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultZoom | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | implemented。Default zoom。fit / 100 |
| autoFitOnOpenToggle | オープン時Auto Fit | checkbox | viewport_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].autoFitOnOpen | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | planned。Auto Fit設定UIの先行設計。現行ModelWeaveSettingsには未実装 |
| preserveViewportStateToggle | Viewport状態保持 | checkbox | viewport_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].preserveViewportState | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | planned / partial。viewport保持UIの先行設計。現行ModelWeaveSettingsには未実装 |
| fontSizeSelect | Viewer文字サイズ | select | viewer_ui_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].fontSize | | implemented。Font size。small / normal / large |
| nodeDensitySelect | ノード密度 | select | viewer_ui_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].nodeDensity | | implemented。Node density。compact / normal / relaxed |
| enableRelationshipViewToggle | Relationship View有効化 | checkbox | viewer_ui_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].enableRelationshipView | | implemented。Relationship View toggle |
| showMermaidRenderDebugToggle | Mermaid描画デバッグ表示 | checkbox | viewer_ui_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].showMermaidRenderDebug | | implemented。Show Mermaid Render Debug toggle |
| uiLanguageSelect | UI言語 | select | viewer_ui_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].uiLanguage | | implemented。UI language。auto / en / ja |
| viewerFontScaleSelect | Viewer文字サイズ | select | viewer_ui_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].viewerFontScale | | planned。現行ModelWeaveSettingsには未実装。fontSizeとの関係整理が必要 |
| diagnosticsVisibleToggle | 診断パネル表示 | checkbox | viewer_ui_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].diagnosticsVisible | | planned。診断パネル表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| statusBarVisibleToggle | ステータスバー表示 | checkbox | viewer_ui_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].statusBarVisible | | planned。ステータスバー表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| localSourceRootText | ローカルソースルート | text | source_link_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].localSourceRoot | | implemented。Local source root。relative Source Links outside vault の解決基準 |
| defaultColorSchemeRefText | 既定Color Scheme参照 | text | color_scheme_settings_area | string | N | [[DATA-MW-PLUGIN-SETTINGS]].defaultColorSchemeRef | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。Default color scheme。color_scheme へのvault refまたはpath |
| enableSourceLinksToggle | Source Links有効化 | checkbox | source_link_settings_area | boolean | N | [[DATA-MW-PLUGIN-SETTINGS]].enableSourceLinks | | future。Source Links Explorer / Source Links索引化がfutureのため現行実装済みではない |
| refreshOpenViewsButton | 開いているビューを更新 | button | settings_action_area | action | N | [[DATA-MW-PLUGIN-SETTINGS]] | | implemented。Refresh open views。open previewを現在設定で再描画する |
| resetSettingsButton | 設定リセット | button | settings_action_area | action | N | [[DATA-MW-PLUGIN-SETTINGS]] | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | planned process。既定設定へ戻す論理操作。現行Settings Tabには未実装 |

## Actions

| id | label | kind | target | event | invoke | transition | rule | notes |
|---|---|---|---|---|---|---|---|---|
| changeDefaultClassRenderMode | Class既定レンダリングモード変更 | ui_action | defaultClassRenderModeSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。updateSettings({ defaultClassRenderMode }) |
| changeDefaultErRenderMode | ER既定レンダリングモード変更 | ui_action | defaultErRenderModeSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。updateSettings({ defaultErRenderMode }) |
| changeDefaultDfdRenderMode | DFD既定レンダリングモード変更 | ui_action | defaultDfdRenderModeSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。updateSettings({ defaultDfdRenderMode }) |
| changeDefaultProcessRenderMode | Process既定レンダリングモード変更 | ui_action | defaultProcessRenderModeSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。updateSettings({ defaultProcessRenderMode }) |
| changeDefaultBusinessFlowDirection | Business Flow既定方向変更 | ui_action | defaultBusinessFlowDirectionSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。updateSettings({ defaultBusinessFlowDirection }) |
| changeDefaultScreenRenderMode | Screen既定レンダリングモード変更 | ui_action | defaultScreenRenderModeSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。updateSettings({ defaultScreenRenderMode }) |
| changeDefaultDomainsViewMode | Domains既定表示モード変更 | ui_action | defaultDomainsViewModeSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。updateSettings({ defaultDomainsViewMode }) |
| changeDefaultDomainDiagramViewMode | Domain Diagram既定表示モード変更 | ui_action | defaultDomainDiagramViewModeSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。updateSettings({ defaultDomainDiagramViewMode }) |
| changeDefaultFlowDiagramViewMode | Flow Diagram既定表示モード変更 | ui_action | defaultFlowDiagramViewModeSelect | change | saveSettings | | [[RULE-MW-FLOW-DIAGRAM-VIEW-STATE]] | implemented。updateSettings({ defaultFlowDiagramViewMode }) |
| changeDefaultRenderMode | 旧既定レンダリングモード変更 | ui_action | defaultRenderModeSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | legacy / not current UI。現行Settings Tabには単一defaultRenderMode変更UIはない |
| changeMermaidThemeMode | Mermaidテーマモード変更 | ui_action | mermaidThemeModeSelect | change | saveSettings | | | planned process。現行ModelWeaveSettingsには未実装の設定UI |
| changeDefaultZoom | 既定ズーム変更 | ui_action | defaultZoomSelect | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。updateSettings({ defaultZoom }) |
| toggleAutoFitOnOpen | Auto Fit切替 | ui_action | autoFitOnOpenToggle | change | saveSettings | | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | planned process。現行ModelWeaveSettingsには未実装の設定UI |
| togglePreserveViewportState | Viewport状態保持切替 | ui_action | preserveViewportStateToggle | change | saveSettings | | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | planned process。現行ModelWeaveSettingsには未実装の設定UI |
| changeFontSize | Viewer文字サイズ変更 | ui_action | fontSizeSelect | change | saveSettings | | | implemented。updateSettings({ fontSize }) |
| changeNodeDensity | ノード密度変更 | ui_action | nodeDensitySelect | change | saveSettings | | | implemented。updateSettings({ nodeDensity }) |
| toggleEnableRelationshipView | Relationship View切替 | ui_action | enableRelationshipViewToggle | change | saveSettings | | | implemented。updateSettings({ enableRelationshipView }) |
| toggleShowMermaidRenderDebug | Mermaid描画デバッグ表示切替 | ui_action | showMermaidRenderDebugToggle | change | saveSettings | | | implemented。updateSettings({ showMermaidRenderDebug }) |
| changeUiLanguage | UI言語変更 | ui_action | uiLanguageSelect | change | saveSettings | | | implemented。updateSettings({ uiLanguage }) |
| changeLocalSourceRoot | ローカルソースルート変更 | ui_action | localSourceRootText | change | saveSettings | | | implemented。updateSettings({ localSourceRoot }) |
| changeDefaultColorSchemeRef | 既定Color Scheme参照変更 | ui_action | defaultColorSchemeRefText | change | saveSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。updateSettings({ defaultColorSchemeRef }) |
| changeViewerFontScale | Viewer文字サイズ変更 | ui_action | viewerFontScaleSelect | change | saveSettings | | | planned process。現行ModelWeaveSettingsには未実装の設定UI |
| toggleDiagnosticsVisible | 診断パネル表示切替 | ui_action | diagnosticsVisibleToggle | change | saveSettings | | | planned process。現行ModelWeaveSettingsには未実装の設定UI |
| toggleStatusBarVisible | ステータスバー表示切替 | ui_action | statusBarVisibleToggle | change | saveSettings | | | planned process。現行ModelWeaveSettingsには未実装の設定UI |
| toggleSourceLinks | Source Links切替 | ui_action | enableSourceLinksToggle | change | saveSettings | | | future process。Source Links Explorer向けの想定。section解析、Preview表示、Copy / Open actionは現行実装済み |
| refreshOpenViews | 開いているビューを更新 | ui_action | refreshOpenViewsButton | click | refreshOpenModelWeaveViews | | | implemented。クリック後に Refreshed open views notice を表示 |
| resetSettings | 設定リセット | ui_action | resetSettingsButton | click | resetToDefaultSettings | | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | logical / planned process。既定設定へ戻す |

## Messages

| id | text | severity | timing | notes |
|---|---|---|---|---|
| settingsSaved | Settings saved. | info | on_change | logical message。現行Settings Tabの各onChangeは保存するが、このNotice文は出していない |
| openViewsRefreshed | Refreshed open views | info | on_refresh | implemented。Refresh open viewsボタン押下時 |
| settingsReset | Settings reset to defaults. | info | on_reset | 既定設定へ戻した時 |
| invalidSettingFallback | Invalid setting value was replaced by default. | warning | on_invalid_setting | 不正値フォールバック時 |

## Notes

- このScreenはObsidianプラグイン設定タブの論理UI定義である。
- 設定値の正本は [[DATA-MW-PLUGIN-SETTINGS]] で表す。
- 現行実装側の設定項目は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS を正とする。
- この screen は現行実装済み設定とdogfood先行設計項目を含むため、全体としては partial / planned と扱う。
- 現行Settings Tabは単一のdefaultRenderModeではなく、defaultClassRenderMode / defaultErRenderMode / defaultDfdRenderMode / defaultProcessRenderMode / defaultScreenRenderMode の形式別selectを表示する。
- defaultRenderMode は旧設定名であり、normalizeModelWeaveSettings の移行補助としてのみ扱う。現行Settings Tabの主UI項目ではない。
- defaultBusinessFlowDirection は現行Settings Tabに実装済みで、app_process Business Flowの既定方向を設定する。toolbar direction controlは一時的なViewer状態であり、この保存設定より優先される。
- defaultDomainsViewMode / defaultDomainDiagramViewMode は現行Settings Tabに実装済みで、domains / domain_diagram の既定表示モードを設定する。
- defaultFlowDiagramViewMode は現行Settings Tabに実装済みで、frontmatter `flow_view` 未指定Flow Diagramの初期表示を設定する。
- defaultFlowDiagramViewMode変更は有効なfrontmatter `flow_view` を持つファイルを上書きしない。frontmatter未指定ファイルは再初期化時に反映される。
- defaultColorSchemeRef は現行Settings Tabに実装済みで、描画時に color_scheme を解決するための保存設定である。
- defaultColorSchemeRefを変更すると、開いているPreviewの再表示時に解決済みColor SchemeとApplied Color Scheme sectionへ反映される。Markdownモデル本文は変更しない。
- defaultColorSchemeRefが空または未解決の場合、Previewは組み込み配色へのfallback結果を表示する。
- defaultZoom / fontSize / nodeDensity / enableRelationshipView / showMermaidRenderDebug / uiLanguage / localSourceRoot は現行Settings Tabに実装済みの設定UI項目として扱う。
- localSourceRoot は Source Links Open action のrelative path解決にも使われるが、Source Links Explorerの索引化や選択UIを有効化する設定ではない。
- autoFitOnOpen / preserveViewportState / mermaidThemeMode / viewerFontScale / diagnosticsVisible / statusBarVisible はdogfood先行設計項目であり、現行実装済みとは断定しない。
- enableSourceLinks は Source Links Explorer / Source Links索引化が future のため、現行実装済みとは扱わない。
- `saveSettings` は updateSettings / saveData に対応する論理名として扱う。`resetToDefaultSettings` は現行Settings Tabには未実装のplanned操作として扱う。
- Refresh open views は現行Settings Tabに実装済みの操作であり、refreshOpenModelWeaveViews を呼び出す。
- 各onChangeは updateSettings を呼び出し、保存後に開いているModel Weave previewへ設定を反映する。
- 実効値の解決は [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] に委ねる。
- 設定変更はObsidianプラグイン設定へ保存され、Markdownモデル本文やfrontmatterは変更しない。
- 診断パネルやステータスバーの表示制御はUI表示のみを対象とし、内部診断生成やViewer状態の保持そのものを止めるものではない。

## Source Links

| path | notes |
|---|---|
| src/main.ts | symbol: ModelWeaveSettingTab.display; kind: method; Obsidian設定タブで公開される設定項目 |
| src/settings/model-weave-settings.ts | symbol: ModelWeaveSettings; kind: type; プラグイン設定構造 |
| src/settings/model-weave-settings.ts | symbol: DEFAULT_MODEL_WEAVE_SETTINGS; kind: constant; 既定設定値 |
| src/settings/model-weave-settings.ts | symbol: normalizeModelWeaveSettings; kind: function; 設定値の正規化と旧defaultRenderModeの移行補助 |
| src/core/flow-diagram-view-mode.ts | symbol: resolveInitialFlowDiagramViewMode; kind: function; Flow Diagram既定表示のfallback |
| src/main.ts | symbol: ModelWeavePlugin.updateSettings; kind: method; 設定変更の正規化、saveData、Preview更新 |
| src/main.ts | symbol: ModelWeavePlugin.refreshOpenModelWeaveViews; kind: method; 開いているPreviewへ設定を再反映 |
| src/main.ts | symbol: ModelWeavePlugin.getViewerPreferences; kind: method; Viewerへ渡す表示設定の構成 |
| src/core/color-scheme.ts | symbol: resolveDefaultColorScheme; kind: function; defaultColorSchemeRef の解決 |
| src/views/applied-color-scheme-renderer.ts | symbol: renderAppliedColorSchemeSectionContent; kind: function; Applied Color Scheme sectionの表示 |
