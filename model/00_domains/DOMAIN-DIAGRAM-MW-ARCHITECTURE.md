---
type: domain_diagram
id: DOMAIN-DIAGRAM-MW-ARCHITECTURE
name: Model Weave Architecture Domain Diagram
render_mode: tree
tags:
  - ModelWeave
  - DomainDiagram
---

# Model Weave Architecture Domain Diagram

## Summary

Model Weave dogfood vault の代表 DFD / app_process / domain_diagram で使う Domain Sources を統合表示するための Domain Diagram です。
Color Schemeが有効な場合、Domain DiagramのArea / Tree表示では `target=domain` とDomain kindに基づく配色が適用されます。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Model Weave dogfood architecture domains |

## Notes

- Domain Sourcesは `type: domains` ファイルを参照する。
- 参照先が解決できない場合や `type: domains` でない場合は診断対象になる。
- sourceSummaries、conflicts、warnings はDomain Diagram表示と診断のための派生結果であり、参照元のDomains定義を書き換えない。
- 本図の表示モード既定値はSettingsの `defaultDomainDiagramViewMode` に従い、toolbar側の一時状態とは分けて扱う。
- Color Schemeは描画結果に適用され、Domain定義そのものは変更しない。

## Source Links

| path | notes |
|---|---|
| src/core/domain-diagram-resolver.ts | resolveDomainDiagram / resolveDomainSources がDomain Sourcesを解決する |
| src/views/modeling-preview-view.ts | Domain Diagram表示時にColor Schemeと表示モードをPreviewへ渡す |

