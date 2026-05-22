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

1. [[SCR-MW-REFERENCE-EXPLORER-VIEW]].referenceList から選択操作された [[ENT-MW-MODEL-REFERENCE]] を取得する。
2. 選択された参照の `referenceId` を [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedReferenceId に反映する。
3. `sourceAssetId` を [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedSourceAssetId に反映する。
4. `targetAssetId` が存在する場合、[[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedTargetAssetId に反映する。
5. `targetAssetId` が空または解決不能な場合は未解決参照として扱い、`selectedTargetAssetId` は未設定（null/undefined）にする。
6. Vaultインデックス内の全診断から、対象の `referenceId` に関連する [[DATA-MW-CORE-DIAGNOSTIC]] を抽出し、[[DATA-MW-REFERENCE-EXPLORER-STATE]].relatedDiagnostics に反映する。
7. 抽出された診断の中に `severity=error` が含まれる場合は `explorerStatus=error`、それ以外は `ready` と判定する。
8. 更新された状態を [[MAP-MW-REFERENCE-EXPLORER-STATE-TO-UI]] を介して詳細ペイン（selectedReferenceDetail / relatedDiagnosticsList）へ反映する。

## Messages

| id | text | severity | notes |
|---|---|---|---|
| referenceSelected | Reference selected. | info | 正常な参照が選択された場合の通知 |
| unresolvedReferenceSelected | Unresolved reference selected. | warning | 未解決の参照（ターゲット不在）が選択された場合 |
| noReferenceSelected | No reference selected. | warning | 無効な選択操作が行われた場合 |

## Notes
- 本処理はUI上の選択状態を管理するものであり、Markdownファイル（正本）の内容やフロントマターを変更することはない。
- `selectedReferenceId` 等の ID 群は、エクスプローラー画面が閉じられるまで保持される一時的な状態である。
- 参照先の詳細情報（ファイルパス等）は [[ENT-MW-MODEL-REFERENCE]] 自体には持たせず、`targetAssetId` から [[ENT-MW-MODEL-ASSET]] を介して動的に解決する。
- このプロセスは [[SCR-MW-REFERENCE-EXPLORER-VIEW]] の `ACT-SELECT` アクションの実装詳細に相当する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/core/vault-index.ts | VaultIndex | class | 参照および診断情報の検索元 |
| source/model-weave-repo/src/core/relation-resolver.ts | resolveDiagramRelations | function | 参照解決状態の判定ロジック |
| source/model-weave-repo/src/views/modeling-preview-view.ts | renderCurrentState | method | 状態変更をUIへ通知する仕組みの参考 |