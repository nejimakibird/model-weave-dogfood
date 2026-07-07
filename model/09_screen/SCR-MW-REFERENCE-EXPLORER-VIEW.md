---
type: screen
id: SCR-MW-REFERENCE-EXPLORER-VIEW
name: Reference Explorer View
screen_type: viewer
kind: explorer_view
tags:
  - ModelWeave
  - Reference
  - Explorer
  - UI
---

# Reference Explorer View

## Summary
Vault内モデル資産間の参照関係を一覧表示するfuture / plannedの画面。
未解決参照、参照元、参照先、参照種別、関連診断を確認し、必要に応じて参照元ファイルや診断箇所へ移動できるようにする。
Model Weave 0.1.17時点では、Impact Summary / Relationship View / Weave Mapは実装済みだが、独立したReference Explorer Viewは本体srcで確認できない。

## Layout

| id | label | kind | purpose | notes |
|---|---|---|---|---|
| reference_explorer_root | Reference Explorer Root | section | 画面全体のコンテナ | |
| reference_summary_area | Reference Summary Area | section | 参照件数や未解決参照数の表示 | |
| reference_filter_area | Reference Filter Area | search_area | 解決状態・参照種別・検索条件の指定 | |
| reference_list_area | Reference List Area | table_area | 参照一覧の表示 | |
| reference_row | Reference Row | other | 参照一覧の1行表示 | referenceList内で反復表示される行 |
| reference_detail_area | Reference Detail Area | detail | 選択参照の詳細と関連診断表示 | |
| reference_action_area | Reference Action Area | action_area | 参照元/参照先へのジャンプ操作 | |

## Fields

| id | label | kind | layout | data_type | required | ref | rule | notes |
|---|---|---|---|---|---|---|---|---|
| referenceCountLabel | 参照数 | label | reference_summary_area | number | N | [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceCount | | 参照総数 |
| unresolvedReferenceCountLabel | 未解決参照数 | label | reference_summary_area | number | N | [[DATA-MW-REFERENCE-EXPLORER-STATE]].unresolvedReferenceCount | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | unresolved数 |
| filteredReferenceCountLabel | 表示参照数 | label | reference_summary_area | number | N | [[DATA-MW-REFERENCE-EXPLORER-STATE]].filteredReferenceCount | | フィルタ後の参照数 |
| referenceFilterModeSelect | 参照状態フィルタ | select | reference_filter_area | string | N | [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceFilterMode | | all / unresolved / resolved |
| referenceKindFilterSelect | 参照種別フィルタ | select | reference_filter_area | string | N | [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceKindFilter | | wikilink / id / source_link / rule 等 |
| referenceSearchInput | 参照検索 | input | reference_filter_area | string | N | [[DATA-MW-REFERENCE-EXPLORER-STATE]].searchText | | referenceValue / assetId / filePath 検索 |
| referenceList | 参照一覧 | list | reference_list_area | object | Y | [[DATA-MW-REFERENCE-EXPLORER-STATE]].visibleReferences | | 複数のModelReferenceを一覧表示 |
| referenceTextLabel | 参照文字列 | label | reference_row | string | N | [[ENT-MW-MODEL-REFERENCE]].referenceValue | | 記述された参照値 |
| referenceKindLabel | 参照種別 | label | reference_row | string | N | [[ENT-MW-MODEL-REFERENCE]].referenceType | | wikilink / id / source_link 等 |
| referenceResolvedBadge | 解決状態 | label | reference_row | string | N | [[ENT-MW-MODEL-REFERENCE]].resolved | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | resolved / unresolved |
| sourceAssetLabel | 参照元 | label | reference_row | string | N | [[ENT-MW-MODEL-REFERENCE]].sourceAssetId | | 参照元Asset |
| targetAssetLabel | 参照先 | label | reference_row | string | N | [[ENT-MW-MODEL-REFERENCE]].targetAssetId | | 解決済みの場合の参照先 |
| sourceFilePathLabel | 参照元ファイル | label | reference_row | string | N | [[ENT-MW-MODEL-ASSET]].filePath | [[RULE-MW-PATH-SHORTENER]] | sourceAssetId からModelAssetを解決してfilePathを取得する |
| selectedReferenceDetail | 選択参照詳細 | panel | reference_detail_area | object | N | [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedReferenceId | | 選択中参照の詳細 |
| relatedDiagnosticsList | 関連診断一覧 | list | reference_detail_area | object | N | [[DATA-MW-REFERENCE-EXPLORER-STATE]].relatedDiagnostics | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | 未解決参照などの診断 |
| openSourceAssetButton | 参照元を開く | button | reference_action_area | action | N | [[ENT-MW-MODEL-ASSET]].filePath | | sourceAssetId からModelAssetを解決してfilePathを取得する |
| openTargetAssetButton | 参照先を開く | button | reference_action_area | action | N | [[ENT-MW-MODEL-ASSET]].filePath | | targetAssetId からModelAssetを解決してfilePathを取得する |
| openDiagnosticButton | 診断箇所を開く | button | reference_action_area | action | N | [[DATA-MW-CORE-DIAGNOSTIC]] | | 関連診断箇所へ移動 |

## Actions

| id | label | kind | target | event | invoke | transition | rule | notes |
|---|---|---|---|---|---|---|---|---|
| ACT-FILTER-MODE | 参照状態フィルタ変更 | ui_action | referenceFilterModeSelect | change | [[PROC-MW-REFERENCE-EXPLORER-FILTER]] | | | 参照解決状態で絞り込む |
| ACT-FILTER-KIND | 参照種別フィルタ変更 | ui_action | referenceKindFilterSelect | change | [[PROC-MW-REFERENCE-EXPLORER-FILTER]] | | | 参照種別で絞り込む |
| ACT-SEARCH | 参照検索 | ui_action | referenceSearchInput | input | [[PROC-MW-REFERENCE-EXPLORER-FILTER]] | | | 参照文字列やAsset IDで検索 |
| ACT-SELECT | 参照選択 | ui_action | referenceList | select | [[PROC-MW-REFERENCE-SELECT]] | | | 参照詳細を表示 |
| ACT-OPEN-SOURCE | 参照元を開く | ui_action | openSourceAssetButton | click | [[PROC-MW-REFERENCE-FILE-OPEN]] | | [[RULE-MW-PATH-SHORTENER]] | openTargetRole=source で参照元ファイルを開く |
| ACT-OPEN-TARGET | 参照先を開く | ui_action | openTargetAssetButton | click | [[PROC-MW-REFERENCE-FILE-OPEN]] | | [[RULE-MW-PATH-SHORTENER]] | openTargetRole=target で参照先ファイルを開く |
| ACT-OPEN-DIAG | 診断箇所を開く | ui_action | openDiagnosticButton | click | openDiagnosticLocation | | | 関連診断箇所へ移動 |

## Messages

| id | text | severity | timing | notes |
|---|---|---|---|---|
| noReferencesFound | No references found. | info | when_empty | 参照が見つからない場合 |
| unresolvedReferencesFound | Unresolved references found. | warning | when_unresolved_exists | 未解決参照がある場合 |
| referenceExplorerReady | Reference explorer ready. | info | when_ready | 表示可能な状態 |
| openReferenceFailed | Failed to open reference target. | error | on_open_failed | 参照先を開けない場合 |

## Notes
- このScreenはfuture / plannedの参照確認UIであり、Markdownモデルの正本を変更しない。
- [[DATA-MW-REFERENCE-EXPLORER-STATE]] は [[DATA-MW-VAULT-MODEL-INDEX]] から導出される派生状態である。
- 現行実装済みのRelationship View / Impact Summary / Weave MapはViewer内の派生表示であり、この横断Explorerとは別機能である。
- 表示上のパス短縮は [[RULE-MW-PATH-SHORTENER]] に従うが、ファイルジャンプでは完全なfilePathを使用する。
- `filterReferenceExplorer` は [[PROC-MW-REFERENCE-EXPLORER-FILTER]] として詳細化済みである。
- `selectReference` は [[PROC-MW-REFERENCE-SELECT]] として詳細化済みである。
- `openReferenceSource` / `openReferenceTarget` は [[PROC-MW-REFERENCE-FILE-OPEN]] として共通処理化済みである。
- `openDiagnosticLocation` は現時点では論理プロセス名であり、必要に応じて後続で app_process 化する。

## Source Links

| path | notes |
|---|---|
| src/core/vault-index.ts | symbol: VaultIndex; kind: class; 参照情報の索引元 |
| src/core/relation-resolver.ts | symbol: resolveDiagramRelations; kind: function; 参照解決処理 |
| src/main.ts | symbol: openDiagnosticLocation; kind: function; 診断箇所ジャンプ |
| src/views/modeling-preview-view.ts | symbol: renderCurrentState; kind: method; Viewer表示更新の参考 |
