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
DATA-MW-VAULT-MODEL-INDEX が保持するモデル間参照と診断情報を、future / plannedのReference Explorerで表示・検索しやすい DATA-MW-REFERENCE-EXPLORER-STATE へ変換するマッピングを定義する。
Model Weave 0.1.17時点では、Relationship View / Impact Summary / Weave Mapは実装済みだが、独立Reference Explorer状態への変換は本体srcで確認できない。

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
| [[DATA-MW-VAULT-MODEL-INDEX]] | [[DATA-MW-REFERENCE-EXPLORER-STATE]].sourceIndexId | 表示用IDを派生 | | N | 実装IndexにindexId fieldはない |
| [[DATA-MW-VAULT-MODEL-INDEX]].relationsById | [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceCount | relation件数を集計 | | N | 参照総数の表示用派生値 |
| [[DATA-MW-VAULT-MODEL-INDEX]].warningsByFilePath | [[DATA-MW-REFERENCE-EXPLORER-STATE]].unresolvedReferenceCount | unresolved-reference件数を集計 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 未解決参照数の表示用派生値 |
| [[DATA-MW-VAULT-MODEL-INDEX]].relationsById | [[DATA-MW-REFERENCE-EXPLORER-STATE]].visibleReferences | 初期表示では全relationを表示対象にする | | Y | Explorerはfuture/planned。現行Indexのrelation lookupを元にする |
| [[DATA-MW-VAULT-MODEL-INDEX]].warningsByFilePath | [[DATA-MW-REFERENCE-EXPLORER-STATE]].relatedDiagnostics | 参照に関連する診断だけを抽出 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | 未解決参照など |
| [[ENT-MW-MODEL-REFERENCE]].referenceId | [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedReferenceId | 初期状態では空または未選択 | | N | ユーザー選択後に設定 |
| [[ENT-MW-MODEL-REFERENCE]].sourceAssetId | [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedSourceAssetId | 選択参照から導出 | | N | 選択時に設定 |
| [[ENT-MW-MODEL-REFERENCE]].targetAssetId | [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedTargetAssetId | 選択参照から導出 | | N | 解決済みの場合 |
| | [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceFilterMode | 初期値 all | | N | all / unresolved / resolved |
| | [[DATA-MW-REFERENCE-EXPLORER-STATE]].referenceKindFilter | 初期値 all | | N | 参照種別フィルタ |
| | [[DATA-MW-REFERENCE-EXPLORER-STATE]].searchText | 初期値は空文字 | | N | 検索文字列 |
| [[DATA-MW-REFERENCE-EXPLORER-STATE]].visibleReferences | [[DATA-MW-REFERENCE-EXPLORER-STATE]].filteredReferenceCount | visibleReferencesの件数を計算 | | N | 初期表示件数 |
| [[DATA-MW-REFERENCE-EXPLORER-STATE]].visibleReferences | [[DATA-MW-REFERENCE-EXPLORER-STATE]].explorerStatus | 件数や診断状態から決定 | [[RULE-MW-RENDERER-DIAGNOSTICS-SEVERITY-MAP]] | N | ready / empty / error |

## Rules
- 初期表示では DATA-MW-VAULT-MODEL-INDEX.relationsById を visibleReferences の主な入力として扱う。
- unresolvedReferenceCount は warningsByFilePath 内の unresolved-reference 診断から派生計算する。
- relatedDiagnostics は warningsByFilePath から未解決参照や参照解決に関係するDiagnosticを抽出する。
- referenceFilterMode / referenceKindFilter / searchText は初期状態として all / all / 空文字にする。
- selectedReferenceId / selectedSourceAssetId / selectedTargetAssetId は初期状態では未選択として扱う。
- filteredReferenceCount は visibleReferences の件数から計算する。
- explorerStatus は visibleReferences が空なら empty、error診断があれば error、それ以外は ready とする。
- 本mappingは派生状態の生成であり、Markdown本文やfrontmatterは変更しない。

## Notes
- DATA-MW-REFERENCE-EXPLORER-STATE は DATA-MW-VAULT-MODEL-INDEX から導出される表示用状態である。
- Reference Explorer はfuture/plannedであり、このmappingは現行Index構造から導出可能な表示状態を示す。
- 現行実装済みのImpact Summary / Relationship View / Weave Map向け集約とは別の、将来の横断Explorer用mappingとして扱う。
- 選択参照やフィルタ条件はReference Explorerの一時状態であり、Markdownモデルには保存しない。
- ファイルパスの表示短縮はUIマッピング側で [[RULE-MW-PATH-SHORTENER]] に委ねる。
- 参照元/参照先ファイルパスは [[ENT-MW-MODEL-REFERENCE]] ではなく、sourceAssetId / targetAssetId から [[ENT-MW-MODEL-ASSET]] を解決して取得する。

## Source Links

| path | notes |
|---|---|
| src/core/vault-index.ts | symbol: VaultIndex; kind: class; 参照情報の索引元 |
| src/core/relation-resolver.ts | symbol: resolveDiagramRelations; kind: function; 参照解決処理 |
| src/core/current-file-diagnostics.ts | symbol: buildCurrentObjectDiagnostics; kind: function; 参照関連診断の生成 |
