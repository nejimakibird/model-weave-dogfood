---
type: screen
id: SCR-MW-SOURCE-LINK-EXPLORER-VIEW
name: Source Links Explorer View
screen_type: viewer
kind: explorer_view
tags:
  - ModelWeave
  - SourceLinks
  - Explorer
  - UI
---

# Source Links Explorer View

## Summary
Model Weaveモデル資産と実装ソースコードの対応関係を一覧表示する画面。
Source Linksに記述されたpath / symbol / kind / notesを確認し、対象モデル資産や実装ソースへの追跡を支援する。

## Layout

| id | label | kind | purpose | notes |
|---|---|---|---|---|
| source_link_explorer_root | Source Links Explorer Root | section | 画面全体のコンテナ | |
| source_link_summary_area | Source Links Summary Area | section | Source Link件数や未設定件数の表示 | |
| source_link_filter_area | Source Links Filter Area | search_area | path / symbol / kind / model type の検索条件指定 | |
| source_link_list_area | Source Links List Area | table_area | Source Links一覧の表示 | |
| source_link_row | Source Link Row | other | Source Links一覧の1行表示 | sourceLinkList内で反復表示される行 |
| source_link_detail_area | Source Link Detail Area | detail | 選択Source Linkの詳細表示 | |
| source_link_action_area | Source Link Action Area | action_area | モデル資産やソースパスへのジャンプ操作 | |

## Fields

| id | label | kind | layout | data_type | required | ref | rule | notes |
|---|---|---|---|---|---|---|---|---|
| sourceLinkCountLabel | Source Link数 | label | source_link_summary_area | number | N | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].sourceLinkCount | | Source Links総数 |
| linkedAssetCountLabel | Link付き資産数 | label | source_link_summary_area | number | N | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].linkedAssetCount | | Source Linksを持つModelAsset数 |
| missingSourceLinkCountLabel | Link未設定資産数 | label | source_link_summary_area | number | N | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].missingSourceLinkCount | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | Source Linksを持たないModelAsset数 |
| filteredSourceLinkCountLabel | 表示Source Link数 | label | source_link_summary_area | number | N | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].filteredSourceLinkCount | | フィルタ後のSource Link数 |
| sourcePathFilterInput | ソースパス検索 | input | source_link_filter_area | string | N | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].sourcePathFilter | | path検索 |
| symbolFilterInput | シンボル検索 | input | source_link_filter_area | string | N | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].symbolFilter | | symbol検索 |
| sourceKindFilterSelect | Source Link種別 | select | source_link_filter_area | string | N | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].sourceKindFilter | | function / class / type / constant 等 |
| modelTypeFilterSelect | モデル種別 | select | source_link_filter_area | string | N | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].modelTypeFilter | | data_object / rule / mapping 等 |
| sourceLinkSearchInput | 全文検索 | input | source_link_filter_area | string | N | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].searchText | | path / symbol / notes / assetId検索 |
| sourceLinkList | Source Link一覧 | list | source_link_list_area | object | Y | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].visibleSourceLinks | | 複数のSource Linkを一覧表示 |
| sourceLinkAssetLabel | 対応Asset | label | source_link_row | string | N | [[ENT-MW-SOURCE-LINK]].assetId | | 対応するModelAsset |
| sourcePathLabel | ソースパス | label | source_link_row | string | N | [[ENT-MW-SOURCE-LINK]].path | [[RULE-MW-PATH-SHORTENER]] | 表示上は短縮してよい |
| sourceSymbolLabel | シンボル | label | source_link_row | string | N | [[ENT-MW-SOURCE-LINK]].symbol | | 関数名 / 型名 / クラス名など |
| sourceKindLabel | 種別 | label | source_link_row | string | N | [[ENT-MW-SOURCE-LINK]].kind | | function / class / type / constant 等 |
| sourceLinkNotesLabel | Notes | label | source_link_row | string | N | [[ENT-MW-SOURCE-LINK]].notes | | Source Links行の補足 |
| selectedSourceLinkDetail | 選択Source Link詳細 | panel | source_link_detail_area | object | N | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].selectedSourceLinkId | | 選択中Source Linkの詳細 |
| relatedModelAssetsList | 関連モデル資産 | list | source_link_detail_area | object | N | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].relatedModelAssets | | 対応するModelAsset一覧 |
| relatedDiagnosticsList | 関連診断一覧 | list | source_link_detail_area | object | N | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].relatedDiagnostics | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | Source Links不足などの診断 |
| openModelAssetButton | モデル資産を開く | button | source_link_action_area | action | N | [[ENT-MW-SOURCE-LINK]].assetId | | 対応するモデル資産を開く |
| openSourcePathButton | ソースパスを開く | button | source_link_action_area | action | N | [[ENT-MW-SOURCE-LINK]].path | | 実装ソースのパスを開く |
| copySourcePathButton | ソースパスをコピー | button | source_link_action_area | action | N | [[ENT-MW-SOURCE-LINK]].path | | path / symbol をコピー |

## Actions

| id | label | kind | target | event | invoke | transition | rule | notes |
|---|---|---|---|---|---|---|---|---|
| ACT-FILTER-SOURCE-PATH | ソースパス検索 | ui_action | sourcePathFilterInput | input | [[PROC-MW-SOURCE-LINK-EXPLORER-FILTER]] | | | source pathで絞り込む |
| ACT-FILTER-SYMBOL | シンボル検索 | ui_action | symbolFilterInput | input | [[PROC-MW-SOURCE-LINK-EXPLORER-FILTER]] | | | symbolで絞り込む |
| ACT-FILTER-KIND | Source Link種別変更 | ui_action | sourceKindFilterSelect | change | [[PROC-MW-SOURCE-LINK-EXPLORER-FILTER]] | | | kindで絞り込む |
| ACT-FILTER-MODEL-TYPE | モデル種別変更 | ui_action | modelTypeFilterSelect | change | [[PROC-MW-SOURCE-LINK-EXPLORER-FILTER]] | | | model typeで絞り込む |
| ACT-SEARCH-SOURCE-LINK | Source Link検索 | ui_action | sourceLinkSearchInput | input | [[PROC-MW-SOURCE-LINK-EXPLORER-FILTER]] | | | path / symbol / notes検索 |
| ACT-SELECT-SOURCE-LINK | Source Link選択 | ui_action | sourceLinkList | select | [[PROC-MW-SOURCE-LINK-SELECT]] | | | 詳細を表示 |
| ACT-OPEN-MODEL-ASSET | モデル資産を開く | ui_action | openModelAssetButton | click | [[PROC-MW-MODEL-FILE-OPEN]] | | | assetIdからModelAssetを解決して開く |
| ACT-OPEN-SOURCE-PATH | ソースパスを開く | ui_action | openSourcePathButton | click | [[PROC-MW-SOURCE-PATH-OPEN]] | | [[RULE-MW-PATH-SHORTENER]] | 完全なpathを使用 |
| ACT-COPY-SOURCE-PATH | ソースパスをコピー | ui_action | copySourcePathButton | click | [[PROC-MW-SOURCE-PATH-COPY]] | | | path / symbolをコピー |

## Messages

| id | text | severity | timing | notes |
|---|---|---|---|---|
| noSourceLinksFound | No Source Links found. | info | when_empty | Source Linksが見つからない場合 |
| sourceLinksMissing | Some model assets have no Source Links. | warning | when_missing_exists | Source Links未設定資産がある場合 |
| sourceLinkExplorerReady | Source Links explorer ready. | info | when_ready | 表示可能な状態 |
| openSourcePathFailed | Failed to open source path. | error | on_open_failed | ソースパスを開けない場合 |
| sourcePathCopied | Source path copied. | info | on_copy | ソースパスコピー時 |

## Notes
- このScreenはSource Linksを確認するための表示UIであり、Markdownモデルの正本を変更しない。
- [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]] は [[DATA-MW-VAULT-MODEL-INDEX]] から導出される派生状態である。
- 表示上のパス短縮は [[RULE-MW-PATH-SHORTENER]] に従うが、ファイルオープンやコピーには完全なpathを使用する。
- `filterSourceLinks` は [[PROC-MW-SOURCE-LINK-EXPLORER-FILTER]] として詳細化済み。
- `selectSourceLink` は [[PROC-MW-SOURCE-LINK-SELECT]] として詳細化済み。
- `openSourcePath` は [[PROC-MW-SOURCE-PATH-OPEN]] として詳細化済み。
- `copySourcePath` は [[PROC-MW-SOURCE-PATH-COPY]] として詳細化済み。
- `openModelFile` は [[PROC-MW-MODEL-FILE-OPEN]] を流用して詳細化済み。assetIdからModelAssetを解決して開く。
- [[ENT-MW-SOURCE-LINK]] の物理名（assetId / path / symbol / kind / notes）を参照している。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/vault-index.ts | VaultIndex | class | Source Links情報の索引元 |
| src/core/schema-detector.ts | detectModelType | function | モデル資産種別の検出 |
| src/main.ts | openDiagnosticLocation | function | ファイルジャンプ処理の参考 |
| src/views/modeling-preview-view.ts | renderCurrentState | method | Viewer表示更新の参考 |
