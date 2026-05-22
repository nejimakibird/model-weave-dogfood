---
type: mapping
id: MAP-MW-VAULT-INDEX-TO-REFERENCE-EXPLORER-STATE
name: Vault Index to Reference Explorer State Mapping
kind: data_to_data
source: "[[DATA-MW-VAULT-MODEL-INDEX]]"
target: "[[DATA-MW-REFERENCE-EXPLORER-STATE]]"
tags:
  - ModelWeave
  - Reference
  - Explorer
  - Mapping
---

# Vault Index to Reference Explorer State Mapping

## Summary
DATA-MW-VAULT-MODEL-INDEX が保持するモデル間参照と診断情報を、Reference Explorerで表示・検索しやすい DATA-MW-REFERENCE-EXPLORER-STATE へ変換するマッピングを定義する。

## Scope

| role | ref | notes |
|---|---|---|
| source | [[DATA-MW-VAULT-MODEL-INDEX]] | Vault内モデル資産の派生インデックス |
| source | [[ENT-MW-MODEL-REFERENCE]] | モデル間参照 |
| source | [[ENT-MW-MODEL-ASSET]] | 参照元/参照先アセット |
| source | [[DATA-MW-CORE-DIAGNOSTIC]] | 参照に関連する診断 |
| target | [[DATA-MW-REFERENCE-EXPLORER-STATE]] | Reference Explorer表示状態 |
| rule | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | 診断severity分類 |

## Mappings

| source_ref | target_ref | transform | rule | required | notes |
|---|---|---|---|---|---|
| [[DATA-MW-VAULT-MODEL-INDEX]].indexId | [[DATA-MW-REFERENCE-EXPLORER-STATE]].sourceIndexId | そのまま保持 | | N | 元インデックスID |
| [[DATA-MW-VAULT-MODEL-INDEX]].referenceCount | [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceCount | 件数をそのまま反映 | | N | 参照総数 |
| [[DATA-MW-VAULT-MODEL-INDEX]].unresolvedReferenceCount | [[DATA-MW-REFERENCE-EXPLORER-STATE]].unresolvedReferenceCount | 件数をそのまま反映 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 未解決参照数 |
| [[DATA-MW-VAULT-MODEL-INDEX]].modelReferences | [[DATA-MW-REFERENCE-EXPLORER-STATE]].visibleReferences | 初期表示では全参照を表示対象にする | | Y | 複数のModelReferenceを表示用に保持 |
| [[DATA-MW-VAULT-MODEL-INDEX]].diagnostics | [[DATA-MW-REFERENCE-EXPLORER-STATE]].relatedDiagnostics | 参照に関連する診断だけを抽出 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 未解決参照など |
| [[ENT-MW-MODEL-REFERENCE]].referenceId | [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedReferenceId | 初期状態では空または未選択 | | N | ユーザー選択後に設定 |
| [[ENT-MW-MODEL-REFERENCE]].sourceAssetId | [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedSourceAssetId | 選択参照から導出 | | N | 選択時に設定 |
| [[ENT-MW-MODEL-REFERENCE]].targetAssetId | [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedTargetAssetId | 選択参照から導出 | | N | 解決済みの場合 |
| | [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceFilterMode | 初期値 all | | N | all / unresolved / resolved |
| | [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceKindFilter | 初期値 all | | N | 参照種別フィルタ |
| | [[DATA-MW-REFERENCE-EXPLORER-STATE]].searchText | 初期値は空文字 | | N | 検索文字列 |
| [[DATA-MW-REFERENCE-EXPLORER-STATE]].visibleReferences | [[DATA-MW-REFERENCE-EXPLORER-STATE]].filteredReferenceCount | visibleReferencesの件数を計算 | | N | 初期表示件数 |
| [[DATA-MW-REFERENCE-EXPLORER-STATE]].visibleReferences | [[DATA-MW-REFERENCE-EXPLORER-STATE]].explorerStatus | 件数や診断状態から決定 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | ready / empty / error |

## Rules
- 初期表示では DATA-MW-VAULT-MODEL-INDEX.modelReferences を visibleReferences に反映する。
- unresolvedReferenceCount は resolved=false の参照数と一致させる。
- relatedDiagnostics は未解決参照や参照解決に関係するDiagnosticを抽出する。
- referenceFilterMode / referenceKindFilter / searchText は初期状態として all / all / 空文字にする。
- selectedReferenceId / selectedSourceAssetId / selectedTargetAssetId は初期状態では未選択として扱う。
- filteredReferenceCount は visibleReferences の件数から計算する。
- explorerStatus は visibleReferences が空なら empty、error診断があれば error、それ以外は ready とする。
- 本mappingは派生状態の生成であり、Markdown本文やfrontmatterは変更しない。

## Notes
- DATA-MW-REFERENCE-EXPLORER-STATE は DATA-MW-VAULT-MODEL-INDEX から導出される表示用状態である。
- 選択参照やフィルタ条件はReference Explorerの一時状態であり、Markdownモデルには保存しない。
- ファイルパスの表示短縮はUIマッピング側で [[RULE-MW-PATH-SHORTENER]] に委ねる。
- 参照元/参照先ファイルパスは [[ENT-MW-MODEL-REFERENCE]] ではなく、sourceAssetId / targetAssetId から [[ENT-MW-MODEL-ASSET]] を解決して取得する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/core/vault-index.ts | VaultIndex | class | 参照情報の索引元 |
| source/model-weave-repo/src/core/relation-resolver.ts | resolveDiagramRelations | function | 参照解決処理 |
| source/model-weave-repo/src/core/current-file-diagnostics.ts | buildCurrentObjectDiagnostics | function | 参照関連診断の生成 |