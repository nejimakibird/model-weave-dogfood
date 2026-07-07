---
type: dfd_diagram
id: DFD-MW-CORE-L0
name: Model Weave Core Level 0
level: 0
tags:
  - DFD
  - Core
---

# Model Weave Core Level 0

## Summary

Model Weave プラグイン内部の主要なデータフローを示します。
Markdown ソースの読み込みからパース、Vault Index構築、参照解決、Source Links、Impact Summary、Color Scheme解決、Rendererを介した Preview View 表示までの実装境界を示します。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Model Weave architecture domains |

## Objects

| id | label | kind | ref | domain | notes |
|---|---|---|---|---|---|
| MD_FILES | Markdown Model Files | external | | vault_content | Obsidian Vault 内の設計アセット群 |
| PARSER | Core Parser | process | [[DFD-MW-OBJ-PARSER]] | parser_resolver | schema/type検出とparser dispatch |
| VAULT_INDEX | Vault Index | datastore | [[DFD-MW-OBJ-VAULT-INDEX]] | model_storage | 解析済みモデルとlookupの実行時index |
| RESOLVER | Resolver | process | [[DFD-MW-OBJ-RESOLVER]] | relation_resolution | 参照解決とResolvedDiagram生成 |
| SOURCE_LINKS | Source Links | process | [[DFD-MW-OBJ-SOURCE-LINKS]] | source_links | Source Links section解析とPreview action |
| IMPACT_ANALYZER | Impact Analyzer | process | [[DFD-MW-OBJ-IMPACT-ANALYZER]] | impact_analysis | 関係/影響summary生成 |
| SETTINGS | Plugin Settings | datastore | [[DFD-MW-OBJ-SETTINGS]] | settings_configuration | 正規化済み設定とviewer preferences |
| RENDERER | Renderer | process | [[DFD-MW-OBJ-RENDERER]] | renderer_area | Preview DOM / Mermaid / Business Flow描画 |
| VIEWER | Preview View | external | [[DFD-MW-OBJ-PREVIEW-VIEW]] | viewer_ui | ModelingPreviewView |

## Flows

| id | from | to | data | notes |
|---|---|---|---|---|
| F01 | MD_FILES | PARSER | [[DATA-MW-CORE-VAULT-FILE]] | Markdown入力; Vault Markdown file |
| F02 | PARSER | VAULT_INDEX | [[DATA-MW-CORE-PARSED-MODEL]] | 解析済みモデル登録; ParsedFileModel |
| F03 | PARSER | VAULT_INDEX | [[DATA-MW-CORE-DIAGNOSTIC]] | parser warning登録; warningsByFilePath |
| F04 | PARSER | SOURCE_LINKS | [[DATA-MW-SOURCE-LINK]] | Source Links解析結果; ParsedFileModel.sourceLinks |
| F05 | VAULT_INDEX | RESOLVER | [[DATA-MW-VAULT-MODEL-INDEX]] | indexed model lookup; relation/member lookupを含む |
| F06 | SETTINGS | VIEWER | [[DATA-MW-VIEWER-STATE]] | viewer preferences; applyViewerSettings |
| F07 | SETTINGS | RENDERER | [[DATA-MW-PLUGIN-SETTINGS]] | render/debug settings; render mode default / Mermaid debug |
| F07A | SETTINGS | RENDERER | [[DATA-MW-PLUGIN-SETTINGS]].defaultColorSchemeRef | Color Scheme解決入力; ViewerPreferencesへの単純転記ではない |
| F08 | RESOLVER | RENDERER | [[DATA-MW-RENDERER-GRAPH-MODEL]] | 解決済み図面; ResolvedDiagram |
| F09 | RENDERER | VIEWER | [[DATA-MW-RENDERER-MERMAID-SOURCE]] | Mermaid source / Preview DOM; Mermaidまたはcustom preview |
| F10 | SOURCE_LINKS | VIEWER | [[DATA-MW-SOURCE-LINK]] | Source Links表示/action; Copy Path / Open resolved path |
| F11 | VAULT_INDEX | IMPACT_ANALYZER | [[DATA-MW-VAULT-MODEL-INDEX]] | 関係探索入力; inbound / outbound探索 |
| F12 | IMPACT_ANALYZER | VIEWER | [[DATA-MW-IMPACT-SUMMARY]] | Impact Summary; relationship summary表示 |
| F13 | VAULT_INDEX | VIEWER | [[DATA-MW-CORE-DIAGNOSTIC]] | Diagnostics; warning / error / info |
| F14 | PARSER | RESOLVER | [[DATA-MW-FLOW-DIAGRAM-PARSED-DIAGRAM]] | Flow Diagram MVP; type: flow_diagram |

## Notes

- 本図はシステム全体の概要を示す Level 0 図です。
- Resolver は参照/関係解決境界であり、Rendererとは分けて扱う。
- Color SchemeはSettingsのdefaultColorSchemeRefから描画時に解決され、DFD / Business Flow / Domain group / Weave Map の見た目へ反映される。
- DFDはMermaid固定方針であり、Color Schemeが適用されてもFlow Connect Modeや汎用グラフ編集の対象にはしない。
- Flow Diagram MVPは `dfd_diagram` とは別形式であり、Parser / Resolver / RendererのDFD-like実装経路を一部共有する。
- Source Links Explorer は将来機能であり、本図では実装済みの Source Links section support のみを扱う。
- PNG Exportは描画結果の派生出力であり、Markdownモデルの正本内容を変更しない。

## Source Links

| path | notes |
|---|---|
| src/main.ts | buildVaultIndex呼び出しとPreview更新 |
| src/core/vault-index.ts | parseVaultFile / buildVaultIndex |
| src/core/relation-resolver.ts | resolveDiagramRelations |
| src/core/impact-analyzer.ts | buildImpactSummary |
| src/renderers/diagram-renderer.ts | renderer dispatch |
| src/renderers/dfd-mermaid.ts | Flow Diagram MVP rendering |
| src/core/color-scheme.ts | resolveDefaultColorScheme / resolveColorStyle |
| src/views/modeling-preview-view.ts | Viewer内表示 |
| src/settings/model-weave-settings.ts | settings normalization |
