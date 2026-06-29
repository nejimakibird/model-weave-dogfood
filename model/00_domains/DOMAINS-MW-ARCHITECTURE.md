---
type: domains
id: DOMAINS-MW-ARCHITECTURE
name: Model Weave Architecture Domains
tags:
  - ModelWeave
  - Domains
---

# Model Weave Architecture Domains

## Summary

Model Weave dogfood vault で DFD / app_process / domain_diagram の Domain Sources を確認するため、Model Weave 内部領域の代表的な Domain 階層を定義します。
この domains ファイルは再利用可能なDomain定義であり、各モデル側のローカル `## Domains` が同じidを持つ場合はローカル定義が優先されます。

## Domains

| id | name | kind | parent | description |
|---|---|---|---|---|
| model_weave_app | Model Weave App | application | | Obsidian plugin と dogfood model 全体の境界 |
| markdown_model | Markdown Model | model | model_weave_app | Markdown model format と解析済みモデルの領域 |
| vault_content | Vault Content | data | markdown_model | Obsidian Vault 内の Markdown model files |
| parser_resolver | Parser / Resolver | model | markdown_model | schema/type detection と parser dispatch |
| model_storage | Model Storage | data | markdown_model | Vault Index と lookup cache |
| relation_resolution | Relation Resolution | integration | markdown_model | model relations / member references / resolved diagrams |
| preview_pipeline | Preview Pipeline | integration | model_weave_app | Preview state から renderer / UI へつなぐ処理領域 |
| renderer_area | Renderer Area | renderer | preview_pipeline | Mermaid / table-text / custom preview rendering |
| viewer_ui | Viewer UI | ui | preview_pipeline | Modeling Preview View と補助表示 |
| impact_analysis | Impact Analysis | operations | preview_pipeline | relationship / impact summary generation |
| diagnostics | Diagnostics | operations | preview_pipeline | parser / validator warnings and preview diagnostics |
| source_links | Source Links | integration | preview_pipeline | Source Links section rendering and actions |
| settings_configuration | Settings Configuration | operations | model_weave_app | plugin settings and viewer preferences |
| editor_integration | Editor Integration | integration | model_weave_app | Obsidian editor / active file integration |
| export_engine | Export Engine | export | model_weave_app | PNG export and rendered output generation |

## Notes

- Domain Sourcesとして参照された場合、外部Domainsとして先に読み込まれ、対象モデル内のローカル `## Domains` が同じidを上書きする。
- app_process Business Flowでは `Steps.domain` が解決済みDomain idに対応する場合、Domain groupとして表示される。
- DFDでは `Objects.domain` が解決済みDomain idに対応する場合、Domain subgraphとして表示される。
- unresolved domain は診断対象であり、単なる配色漏れとして扱わない。
- Color Schemeが有効な場合、Domain groupは `target=domain` と `kind=<Domains.kind>` で配色される。

## Source Links

| path | notes |
|---|---|
| src/core/domain-diagram-resolver.ts | Domain Sourcesを解決し、複数domainsファイルを統合する |
| src/core/app-process-domain-resolver.ts | app_processのDomain SourcesとローカルDomainsを解決する |
| src/parsers/dfd-diagram-parser.ts | DFDのDomain Sources / Domains / Objects.domainを解析する |

