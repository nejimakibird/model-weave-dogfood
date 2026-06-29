---
type: app_process
id: PROC-MW-REFERENCE-FILE-OPEN
name: Reference Explorer参照ファイルを開く
process_type: ui_action
tags:
  - ModelWeave
  - Reference
  - Explorer
  - Process
---

# Reference Explorer参照ファイルを開く

## Summary
Reference Explorerで選択された参照について、参照元または参照先のModelAssetを解決し、対応するMarkdownファイルをObsidian上で開く処理。
表示上の短縮パスではなく、[[ENT-MW-MODEL-ASSET]].filePath の完全なVault相対パスを使用する。
Model Weave 0.1.17時点では、Relationship View内の参照ファイルオープンは実装済みだが、独立したReference Explorer起点のopen処理はfuture / plannedとして扱う。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| selectedReference | [[ENT-MW-MODEL-REFERENCE]] | [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedReferenceId | Y | 選択中の参照 |
| openTargetRole | string | [[SCR-MW-REFERENCE-EXPLORER-VIEW]] | Y | source / target のどちらを開くか |
| sourceAsset | [[ENT-MW-MODEL-ASSET]] | Vault Index | N | sourceAssetId から解決される参照元Asset |
| targetAsset | [[ENT-MW-MODEL-ASSET]] | Vault Index | N | targetAssetId から解決される参照先Asset |
| pathShortenerRule | [[RULE-MW-PATH-SHORTENER]] | Rule | N | 表示上の短縮パス方針。実際のジャンプには完全パスを使用 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| openedFile | [[ENT-MW-MODEL-ASSET]] | Obsidian Workspace | 開かれた参照元または参照先のModelAsset |
| openedFilePath | string | Obsidian Workspace | 実際に開いたVault相対パス |
| activeReference | [[ENT-MW-MODEL-REFERENCE]] | Reference Explorer | ファイルオープンの起点となった参照 |

## Steps

1. [[DATA-MW-REFERENCE-EXPLORER-STATE]].selectedReferenceId から選択中の [[ENT-MW-MODEL-REFERENCE]] を取得する。
2. `openTargetRole` が `source` の場合、`selectedReference.sourceAssetId` を使用する。
3. `openTargetRole` が `target` の場合、`selectedReference.targetAssetId` を使用する。
4. `target` を開く場合に `targetAssetId` が空または未解決であれば、`unresolvedReferenceTarget` メッセージを出して処理を中断する。
5. 対象Asset IDから [[ENT-MW-MODEL-ASSET]] を解決する。
6. 解決したModelAssetから完全な `filePath` を取得する。
7. 表示上の短縮パスではなく、完全な `filePath` を使用する。
8. `filePath` が空またはVault内に存在しない場合は、`openReferenceFileFailed` メッセージを出して処理を中断する。
9. Obsidian Workspace または Editor APIを使って該当Markdownファイルを開く。
10. 必要に応じて開いたファイルをアクティブファイルとして扱い、Viewer表示や診断表示を更新する。

## Messages

| id | text | severity | notes |
|---|---|---|---|
| referenceFileOpened | Reference file opened. | info | 参照元または参照先ファイルを開いた場合 |
| unresolvedReferenceTarget | Reference target is unresolved. | warning | 未解決参照のtargetを開こうとした場合 |
| openReferenceFileFailed | Failed to open reference file. | error | filePathが不正またはVault内に存在しない場合 |
| noReferenceSelected | No reference selected. | warning | 選択中の参照がない場合 |

## Notes
- 本処理はfuture / plannedのReference Explorerから参照元または参照先のMarkdownファイルを開くUI操作である。
- Markdownモデル本文やfrontmatterは変更しない。
- `openTargetRole` により `source` / `target` のどちらを開くかを切り替える。
- 参照元/参照先の `filePath` は [[ENT-MW-MODEL-REFERENCE]] ではなく、`sourceAssetId` / `targetAssetId` から [[ENT-MW-MODEL-ASSET]] を解決して取得する。
- [[RULE-MW-PATH-SHORTENER]] は表示用であり、実際のファイルオープンには完全なfilePathを使用する。
- `openReferenceSource` / `openReferenceTarget` は [[SCR-MW-REFERENCE-EXPLORER-VIEW]] から呼び出される論理プロセスであり、本定義はその共通処理に相当する。
- 現行実装済みのRelationship View / Impact Summaryからの open referenced file とは別に、Reference Explorer専用の選択状態は本体srcで確認できない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/main.ts | openDiagnosticLocation | function | ファイルジャンプ処理の参考 |
| src/core/vault-index.ts | VaultIndex | class | ModelAssetとfilePathの参照元 |
| src/views/modeling-preview-view.ts | renderCurrentState | method | 開いたファイルのViewer表示更新 |
