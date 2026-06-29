---
type: app_process
id: PROC-MW-SOURCE-LINK-SELECT
name: Source Links Explorer Source Link選択
process_type: ui_action
tags:
  - ModelWeave
  - SourceLinks
  - Explorer
  - Process
---

# Source Links Explorer Source Link選択

## Summary
Source Links Explorer上で選択されたSource Linkをもとに、選択中Source Link ID、対応ModelAsset、関連診断を [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]] へ反映する処理。
Model Weave 0.1.17時点では、Preview内Source LinksのCopy/OpenとRelationship View内Related Source Linksは実装済みだが、Source Links Explorerの選択状態管理はfutureとして扱う。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| selectedSourceLink | [[ENT-MW-SOURCE-LINK]] | [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourceLinkList | Y | ユーザーが選択したSource Link行データ |
| explorerState | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]] | State | Y | 更新対象のSource Links Explorer状態 |
| vaultIndex | [[DATA-MW-VAULT-MODEL-INDEX]] | Index | N | 対応するModelAssetを解決するために参照 |
| diagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | Diagnostics | N | インデックスが保持する全診断のうち、選択リンクに関連するもの |
| severityRule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | Rule | N | 関連診断の重要度表示方針 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| selectedSourceLinkId | string | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].selectedSourceLinkId | 選択されたSource LinkのユニークID |
| selectedAssetId | string | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].selectedAssetId | リンクが設定されているモデル資産ID |
| relatedModelAssets | [[ENT-MW-MODEL-ASSET]] | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].relatedModelAssets | リンクに対応するモデル資産の集合 |
| relatedDiagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].relatedDiagnostics | 選択リンクに紐づく診断情報のリスト |
| explorerStatus | string | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].explorerStatus | UIの全体状態（ready / error 等） |

## Steps

1. [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]].sourceLinkList から選択操作された [[ENT-MW-SOURCE-LINK]] を取得する。
2. 選択されたリンクの `sourceLinkId` を [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].selectedSourceLinkId に反映する。
3. リンク情報の `assetId` を [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].selectedAssetId に反映する。
4. `assetId` をもとに [[DATA-MW-VAULT-MODEL-INDEX]] から対応する [[ENT-MW-MODEL-ASSET]] を解決し、[[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].relatedModelAssets に反映する。
5. Vaultインデックス内の全診断から、対象の `assetId` または `sourceLinkId` に関連する [[DATA-MW-CORE-DIAGNOSTIC]]（リンク切れやパス不正など）を抽出し、[[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].relatedDiagnostics に反映する。
6. 抽出された診断の中に `severity=error` が含まれる場合は `explorerStatus=error`、それ以外は `ready` と判定する。
7. 更新された状態を [[MAP-MW-SOURCE-LINK-EXPLORER-STATE-TO-UI]] を介して詳細ペイン（selectedSourceLinkDetail / relatedModelAssetsList / relatedDiagnosticsList）へ反映する。

## Messages

| id | text | severity | notes |
|---|---|---|---|
| sourceLinkSelected | Source Link selected. | info | 正常なリンクが選択された場合の通知 |
| sourceLinkHasErrors | Selected Source Link has errors. | warning | リンクに関連するエラー診断が存在する場合 |
| noSourceLinkSelected | No Source Link selected. | warning | 無効な選択操作が行われた場合 |

## Notes
- 本処理はfutureのSource Links Explorer向け選択処理であり、Model Weave 0.1.17時点の本体実装済み操作とは扱わない。
- 本処理はUI上の選択状態を管理するものであり、Markdownファイル（正本）の内容やフロントマターを変更することはない。
- `selectedSourceLinkId` 等の ID 群は、エクスプローラー画面内でのみ保持される一時的な状態である。
- 1つの Source Link が複数のアセットに紐づく場合、`relatedModelAssets` にはそれら全てが集約される。
- このプロセスは [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]] の `ACT-SELECT-SOURCE-LINK` アクションの実装詳細に相当する。
- 現行実装済みのSource Links Copy / Open actionsはPreview内Source Links tableの行操作であり、このExplorer選択状態とは別機能である。
- Relationship View / Impact SummaryのRelated Source Linksは現行実装済みの集約表示であり、Explorer専用の選択状態ではない。

## Source Links

| path | notes |
|---|---|
| src/renderers/source-links-renderer.ts | renderSourceLinks function。現行Preview内Source Links表示とCopy/Open |
| src/core/impact-analyzer.ts | collectRelatedSourceLinks function。現行Relationship / Impact Summaryへの関連Source Links集約 |
| src/views/modeling-preview-view.ts | renderCurrentState method。現行Viewer表示であり、独立Source Links Explorer実装ではない |
