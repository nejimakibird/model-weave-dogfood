---
type: mapping
id: MAP-MW-SOURCE-LINK-EXPLORER-STATE-TO-UI
name: Source Links Explorer State to UI Mapping
kind: data_to_screen
source: "[[DATA-MW-SOURCE-LINK-EXPLORER-STATE]]"
target: "[[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]]"
tags:
  - ModelWeave
  - SourceLinks
  - Explorer
  - Mapping
  - UI
---

# Source Links Explorer State to UI Mapping

## Summary

future / plannedの [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]] が保持するSource Link一覧、件数、検索条件、選択状態、関連診断を [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]] の各表示項目へ反映するマッピングを定義する。
Model Weave 0.1.17時点では、Preview内Source Links sectionの表示・Copy/Openは実装済みだが、このExplorer画面へのUI反映は本体srcで確認できない。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]] | Source Links Explorer表示状態 |
| source | [[ENT-MW-SOURCE-LINK]] | 表示対象のSource Link行 |
| source | [[ENT-MW-MODEL-ASSET]] | Source Linkに対応するModelAsset |
| source | [[DATA-MW-CORE-DIAGNOSTIC]] | 関連診断 |
| target | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]] | Source Links Explorer画面 |
| rule | [[RULE-MW-PATH-SHORTENER]] | path短縮表示 |
| rule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | missing / error / warning 表示 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].sourceLinkCount | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourceLinkCountLabel | 件数をそのまま表示 | | N | Source Links総数 |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].linkedAssetCount | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].linkedAssetCountLabel | 件数をそのまま表示 | | N | Source Linksを持つModelAsset数 |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].missingSourceLinkCount | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].missingSourceLinkCountLabel | 件数をそのまま表示 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 0より大きい場合はwarning相当 |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].filteredSourceLinkCount | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].filteredSourceLinkCountLabel | 件数をそのまま表示 | | N | フィルタ後件数 |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].sourcePathFilter | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourcePathFilterInput | 現在値として表示 | | N | path検索 |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].symbolFilter | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].symbolFilterInput | 現在値として表示 | | N | symbol検索 |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].sourceKindFilter | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourceKindFilterSelect | 現在値として表示 | | N | kindフィルタ |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].modelTypeFilter | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].modelTypeFilterSelect | 現在値として表示 | | N | model typeフィルタ |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].searchText | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourceLinkSearchInput | 現在値として表示 | | N | 全文検索 |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].visibleSourceLinks | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourceLinkList | 一覧として反復表示 | | Y | 複数のSource Linkを表示 |
| [[ENT-MW-SOURCE-LINK]].assetId | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourceLinkAssetLabel | そのまま表示 | | N | 対応するModelAsset |
| [[ENT-MW-SOURCE-LINK]].path | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourcePathLabel | 短縮表示 | [[RULE-MW-PATH-SHORTENER]] | N | 完全pathは内部的に保持 |
| [[ENT-MW-SOURCE-LINK]].symbol | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourceSymbolLabel | そのまま表示 | | N | 関数名 / 型名 / クラス名など |
| [[ENT-MW-SOURCE-LINK]].kind | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourceKindLabel | そのまま表示 | | N | シンボル種別 |
| [[ENT-MW-SOURCE-LINK]].notes | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourceLinkNotesLabel | そのまま表示 | | N | Source Links行の補足 |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].selectedSourceLinkId | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].selectedSourceLinkDetail | 選択詳細を表示 | | N | selectSourceLink後に更新 |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].relatedModelAssets | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].relatedModelAssetsList | 関連資産を一覧表示 | | N | 対応するModelAsset一覧 |
| [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].relatedDiagnostics | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].relatedDiagnosticsList | 関連診断を一覧表示 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | Source Links不足など |

## Rules

- `sourceLinkCount` / `linkedAssetCount` / `missingSourceLinkCount` / `filteredSourceLinkCount` はSummary領域へ表示する。
- `missingSourceLinkCount` が1以上の場合、warning相当の表示を許容する。
- `visibleSourceLinks` は `sourceLinkList` に反復表示する。
- [[ENT-MW-SOURCE-LINK]] の物理名 `assetId` / `path` / `symbol` / `kind` / `notes` を表示列へ反映する。
- `sourcePathLabel` は [[RULE-MW-PATH-SHORTENER]] に従い、表示上のみ短縮する。
- `openSourcePath` / `copySourcePath` では短縮表示ではなく完全なpathを使用する。
- `relatedModelAssetsList` は選択Source Linkに対応するModelAssetを表示する。
- `relatedDiagnosticsList` はSource Links不足や未確認リンクなど、Source Linksに関連する診断を表示する。
- 本mappingは表示用の派生変換であり、Markdown本文やfrontmatterは変更しない。

## Notes

- [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]] は [[DATA-MW-VAULT-MODEL-INDEX]] から導出される派生状態である。
- 本mappingはfuture / plannedのSource Links Explorer向けであり、現行Preview内Source Links tableの表示mappingとは分けて扱う。
- Source Linksはモデル資産から実装ソースへ辿るためのナビゲーション情報である。
- `filterSourceLinks` / `selectSourceLink` / `openModelFile` / `openSourcePath` / `copySourcePath` は論理プロセス名であり、後続でapp_process化できる。
- 表示上のパス短縮はUI表示にのみ適用する。
- [[ENT-MW-SOURCE-LINK]] の物理名（assetId / path / symbol / kind / notes）を参照している。

## Source Links

| path | notes |
|---|---|
| src/core/vault-index.ts | symbol: VaultIndex; kind: class; Source Links情報の索引元 |
| src/core/schema-detector.ts | symbol: detectModelType; kind: function; モデル資産種別の検出 |
| src/main.ts | symbol: openDiagnosticLocation; kind: function; ファイルジャンプ処理の参考 |
| src/views/modeling-preview-view.ts | symbol: renderCurrentState; kind: method; Viewer表示更新の参考 |
