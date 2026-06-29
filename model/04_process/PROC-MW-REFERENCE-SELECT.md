---
type: app_process
id: PROC-MW-REFERENCE-SELECT
name: Reference Explorer参照選択
process_type: ui_action
tags:
  - ModelWeave
  - Reference
  - Explorer
  - Process
---

# Reference Explorer参照選択

## Summary
Reference Explorer上で選択されたModelReferenceをもとに、選択中参照ID、参照元Asset、参照先Asset、関連診断を [[DATA-MW-REFERENCE-EXPLORER-STATE]] へ反映する処理。
Model Weave 0.1.17時点では、Relationship View / Impact Summary内の参照表示やopen targetは実装済みだが、独立したReference Explorerの選択状態管理はfutureとして扱う。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| selectedReference | [[ENT-MW-MODEL-REFERENCE]] | [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceList | Y | ユーザーが選択した参照行データ |
| explorerState | [[DATA-MW-REFERENCE-EXPLORER-STATE]] | State | Y | 更新対象の表示状態オブジェクト |
| relatedAssets | [[ENT-MW-MODEL-ASSET]] | Vault Index | N | アセットIDから表示名やパスを解決するために参照 |
| diagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | Diagnostics | N | インデックスが保持する全診断のうち、選択参照に関連するもの |
| severityRule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | Rule | N | 関連診断の重要度表示方針 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| selectedReferenceId | string | [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedReferenceId | 選択された参照のユニークID |
| selectedSourceAssetId | string | [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedSourceAssetId | 参照元のモデル資産ID |
| selectedTargetAssetId | string | [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedTargetAssetId | 参照先のモデル資産ID。未解決時は空 |
| relatedDiagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | [[DATA-MW-REFERENCE-EXPLORER-STATE]].relatedDiagnostics | 選択参照に紐づく診断情報のリスト |
| explorerStatus | string | [[DATA-MW-REFERENCE-EXPLORER-STATE]].explorerStatus | UIの全体状態（ready / error 等） |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | Reference Explorer | 開始 | start | selectedReference | | | | [[SCR-MW-REFERENCE-EXPLORER-VIEW]] | futureのReference Explorer向け |
| waitSelection | Reference Explorer | 参照選択を受け取る | wait | selectedReference | | | | [[SCR-MW-REFERENCE-EXPLORER-VIEW]] | referenceListの選択操作 |
| storeReferenceId | Reference Explorer | selectedReferenceId更新 | store | selectedReference | selectedReferenceId | | | | referenceIdを状態へ反映する |
| storeSourceAsset | Reference Explorer | sourceAssetId更新 | store | selectedReference | selectedSourceAssetId | | | | 参照元Asset IDを反映する |
| hasTargetAsset | Reference Explorer | targetAssetId確認 | decision | selectedReference | | | | | 参照先が存在するか判定する |
| storeTargetAsset | Reference Explorer | targetAssetId更新 | store | selectedReference | selectedTargetAssetId | | | | 参照先Asset IDを反映する |
| markUnresolved | Reference Explorer | 未解決参照として扱う | process | selectedReference | selectedTargetAssetId | | | | selectedTargetAssetIdは未設定にする |
| collectDiagnostics | Reference Explorer | 関連診断抽出 | process | selectedReferenceId | relatedDiagnostics | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | | | referenceIdに関連する診断を抽出する |
| decideStatus | Reference Explorer | 状態判定 | decision | relatedDiagnostics | explorerStatus | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | | | errorがあればexplorerStatusをerrorにする |
| updateUi | Reference Explorer | 詳細ペインへ反映 | screen | explorerState | explorerStatus | | | [[SCR-MW-REFERENCE-EXPLORER-VIEW]] | [[MAP-MW-REFERENCE-EXPLORER-STATE-TO-UI]] で反映する |
| end | Reference Explorer | 終了 | end | | | | | | 処理終了 |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | waitSelection | | 選択待ち | |
| waitSelection | storeReferenceId | | 参照ID反映 | |
| storeReferenceId | storeSourceAsset | | source反映 | |
| storeSourceAsset | hasTargetAsset | | target確認 | |
| hasTargetAsset | storeTargetAsset | targetAssetId exists | 解決済み | |
| hasTargetAsset | markUnresolved | targetAssetId missing | 未解決 | |
| storeTargetAsset | collectDiagnostics | | 診断抽出 | |
| markUnresolved | collectDiagnostics | | 診断抽出 | |
| collectDiagnostics | decideStatus | | 状態判定 | |
| decideStatus | updateUi | | UI反映 | |
| updateUi | end | | 完了 | |

## Messages

| id | text | severity | notes |
|---|---|---|---|
| referenceSelected | Reference selected. | info | 正常な参照が選択された場合の通知 |
| unresolvedReferenceSelected | Unresolved reference selected. | warning | 未解決の参照（ターゲット不在）が選択された場合 |
| noReferenceSelected | No reference selected. | warning | 無効な選択操作が行われた場合 |

## Notes
- 本処理はfutureのReference Explorer向け選択処理であり、Model Weave 0.1.17時点の本体実装済み操作とは扱わない。
- 本処理はUI上の選択状態を管理するものであり、Markdownファイル（正本）の内容やフロントマターを変更することはない。
- `selectedReferenceId` 等の ID 群は、エクスプローラー画面が閉じられるまで保持される一時的な状態である。
- 参照先の詳細情報（ファイルパス等）は [[ENT-MW-MODEL-REFERENCE]] 自体には持たせず、`targetAssetId` から [[ENT-MW-MODEL-ASSET]] を介して動的に解決する。
- このプロセスは [[SCR-MW-REFERENCE-EXPLORER-VIEW]] の `ACT-SELECT` アクションの実装詳細に相当する。
- 現行Relationship Viewのopen targetやWeave Map表示はViewer内機能であり、このExplorer選択状態とは別機能である。
- ref-aware Business Flow step hover / click targetはBusiness Flow Stepの参照確認支援であり、Reference Explorerの行選択ではない。

## Source Links

| path | notes |
|---|---|
| src/core/vault-index.ts | VaultIndex class。future Reference Explorerが参照し得る内部索引 |
| src/core/impact-analyzer.ts | buildImpactSummary function。現行Relationship View向けの参照集約 |
| src/views/modeling-preview-view.ts | renderImpactSummarySection method。現行Viewer内Relationship View表示であり、独立Reference Explorer実装ではない |
