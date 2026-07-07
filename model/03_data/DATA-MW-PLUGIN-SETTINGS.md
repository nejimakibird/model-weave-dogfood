---
type: data_object
id: DATA-MW-PLUGIN-SETTINGS
name: Model Weaveプラグイン設定
kind: settings
data_format: object
tags:
  - ModelWeave
  - Settings
  - Viewer
---

# Model Weaveプラグイン設定

## Summary

Model Weaveプラグイン全体の設定値を表す論理データ構造。
Viewer表示、Renderer選択、Viewport初期化、Mermaid描画、UI表示などに影響する設定値を保持する。
設定はMarkdownモデルの正本ではなく、Obsidianプラグイン設定として保存される実行時構成である。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| defaultClassRenderMode | Class既定レンダリングモード | string | | N | defaultClassRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented。class / class_diagram 用。既定値 custom。許容値 custom / mermaid / mermaid-detail |
| defaultErRenderMode | ER既定レンダリングモード | string | | N | defaultErRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented。er_entity / er_diagram 用。既定値 custom。許容値 custom / mermaid / mermaid-detail |
| defaultDfdRenderMode | DFD既定レンダリングモード | string | | N | defaultDfdRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented。dfd_diagram 用。既定値 mermaid。許容値 mermaid |
| defaultProcessRenderMode | Process既定レンダリングモード | string | | N | defaultProcessRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented。app_process 用。既定値 custom。許容値 custom |
| defaultBusinessFlowDirection | Business Flow既定方向 | string | | N | defaultBusinessFlowDirection | [[RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION]] | implemented。app_process Business Flow用。既定値 LR。許容値 LR / TD |
| defaultScreenRenderMode | Screen既定レンダリングモード | string | | N | defaultScreenRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | implemented。screen 用。既定値 custom。許容値 custom |
| defaultDomainsViewMode | Domains既定表示モード | string | | N | defaultDomainsViewMode | | implemented。domains 用。既定値 mindmap。許容値 mindmap / area / tree |
| defaultDomainDiagramViewMode | Domain Diagram既定表示モード | string | | N | defaultDomainDiagramViewMode | | implemented。domain_diagram 用。既定値 mindmap。許容値 mindmap / area / tree |
| defaultRenderMode | 旧既定レンダリングモード | string | | N | defaultRenderMode | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] | legacy / migration fallback。normalizeModelWeaveSettings が旧値を defaultClassRenderMode / defaultErRenderMode の補助入力として読む。現行設定の主フィールドではない |
| defaultZoom | 既定ズーム | string | | N | defaultZoom | [[RULE-MW-VIEWER-ZOOM-LIMITS]] | implemented。既定値 fit。許容値 fit / 100 |
| fontSize | Viewer文字サイズ | string | | N | fontSize | | implemented。既定値 normal。許容値 small / normal / large |
| nodeDensity | ノード密度 | string | | N | nodeDensity | | implemented。既定値 normal。許容値 compact / normal / relaxed |
| localSourceRoot | ローカルソースルート | string | | N | localSourceRoot | | implemented。既定値 empty string。Preview内Source Linksのrelative path解決に使う。normalizeModelWeaveSettings は旧 sourceRoot も補助入力として読む |
| defaultColorSchemeRef | 既定Color Scheme参照 | string | | N | defaultColorSchemeRef | | implemented。対応diagramの配色に使う color_scheme へのvault refまたはpath。未設定時は組み込み色を使う |
| enableRelationshipView | Relationship View有効化 | boolean | | N | enableRelationshipView | | implemented。既定値 true。Relationship / impact summary の表示準備に使う |
| showMermaidRenderDebug | Mermaid描画デバッグ表示 | boolean | | N | showMermaidRenderDebug | | implemented。既定値 false。Mermaid描画診断表示に使う |
| uiLanguage | UI言語 | string | | N | uiLanguage | | implemented。既定値 auto。許容値 auto / en / ja |
| autoFitOnOpen | オープン時自動フィット | boolean | | N | autoFitOnOpen | [[RULE-MW-VIEWER-AUTO-FIT-ON-LOAD]] | planned。Auto Fit挙動の将来設定。現行ModelWeaveSettingsには未実装 |
| preserveViewportState | Viewport状態保持 | boolean | | N | preserveViewportState | [[RULE-MW-VIEWER-VIEWPORT-STATE-PERSISTENCE]] | planned。Viewport状態保持の将来設定。現行ModelWeaveSettingsには未実装 |
| mermaidThemeMode | Mermaidテーマモード | string | | N | mermaidThemeMode | | planned。Mermaidテーマ制御の将来設定。現行ModelWeaveSettingsには未実装 |
| viewerFontScale | Viewer文字サイズ倍率 | string | | N | viewerFontScale | | planned。Viewer表示調整用の将来設定。現行ModelWeaveSettingsには未実装 |
| diagnosticsVisible | 診断パネル表示 | boolean | | N | diagnosticsVisible | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | planned。診断パネル表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| statusBarVisible | ステータスバー表示 | boolean | | N | statusBarVisible | [[SCR-MW-VIEWER-STATUS-BAR]] | planned。ステータスバー表示制御の将来設定。現行ModelWeaveSettingsには未実装 |
| enableSourceLinks | Source Links有効化 | boolean | | N | enableSourceLinks | | future / non-source-backed。現行ModelWeaveSettingsには存在しない。Preview内Source Links section supportはこの設定でgatingされない |
| settingsVersion | 設定バージョン | string | | N | settingsVersion | | planned。将来の設定移行用。現行src/settings/model-weave-settings.tsには未実装 |

## Notes

- 本データはMarkdownモデルの正本ではなく、Obsidianプラグイン設定として保持される実行時設定を表す。
- 現行実装側の設定項目は src/settings/model-weave-settings.ts の ModelWeaveSettings / DEFAULT_MODEL_WEAVE_SETTINGS を正とする。
- この data_object は0.1.17時点の現行実装済み設定とdogfood先行設計項目を含むため、ファイル全体としては partial と扱う。
- dogfood側のみの項目は、現時点でModel Weave本体に実装済みとは断定しない。
- `## Source Links` の解析、Preview内表示、Copy Path / Open、Relationship summary内のRelated Source Links集約は現行実装済みとして扱う。
- enableSourceLinks は現行ModelWeaveSettingsに存在しないため、実装済み設定とは扱わない。Source Links Explorer向けの将来構想または非source-backed項目として扱う。
- 現行実装済みの既定レンダリング設定は defaultClassRenderMode / defaultErRenderMode / defaultDfdRenderMode / defaultProcessRenderMode / defaultScreenRenderMode に分かれている。
- defaultRenderMode は旧設定名であり、normalizeModelWeaveSettings の legacyDefaultRenderMode として class / ER 既定値の補助入力に使われる。
- 各 render mode の実効値解決は [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] に委ねる。
- defaultBusinessFlowDirection は app_process Business Flow の既定方向であり、toolbar override や frontmatter flow_direction より低い優先度で使われる。
- defaultDomainsViewMode / defaultDomainDiagramViewMode は domains / domain_diagram の既定表示モードとしてViewerへ渡される。
- defaultColorSchemeRef は color_scheme 参照の保存設定であり、解決できない場合は組み込み配色へフォールバックする。
- defaultZoom / fontSize / nodeDensity / localSourceRoot / showMermaidRenderDebug / uiLanguage は Viewer表示設定に影響する。
- localSourceRoot は Preview内Source Linksのrelative path解決に使われる実装済み設定である。
- enableRelationshipView は Relationship View / impact summary の表示準備を制御する実装済み設定である。
- autoFitOnOpen / preserveViewportState は Viewer / Viewport の将来設定として扱う。
- 実装上の設定項目名と完全一致しない場合は、Source Linksで確認して後続タスクで補正する。

## Source Links

| path | notes |
|---|---|
| src/settings/model-weave-settings.ts | symbol: ModelWeaveSettings; kind: type; プラグイン設定構造 |
| src/settings/model-weave-settings.ts | symbol: DEFAULT_MODEL_WEAVE_SETTINGS; kind: constant; 既定設定値 |
| src/settings/model-weave-settings.ts | symbol: normalizeModelWeaveSettings; kind: function; 設定値の正規化と旧defaultRenderMode / sourceRootの移行補助 |
| src/main.ts | symbol: ModelWeaveSettingTab.display; kind: method; 設定タブで公開される設定項目 |
| src/core/render-mode.ts | symbol: resolveRenderMode; kind: function; 既定レンダリングモードの解決 |
| src/core/app-process-business-flow-direction.ts | symbol: resolveAppProcessBusinessFlowDirection; kind: function; Business Flow directionのtoolbar / frontmatter / settings / fallback解決 |
| src/core/color-scheme.ts | symbol: resolveDefaultColorScheme; kind: function; defaultColorSchemeRef の解決と組み込み配色へのfallback |
| src/renderers/source-links-renderer.ts | symbol: renderSourceLinks; kind: function; Preview内Source Links表示とCopy Path / Open操作 |
| src/views/modeling-preview-view.ts | symbol: prepareFileViewportState; kind: method; defaultZoom / viewport状態の反映 |
