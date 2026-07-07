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

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | Reference Explorer | 開始 | start | selectedReference | | | | [[SCR-MW-REFERENCE-EXPLORER-VIEW]] | future / plannedのReference Explorer起点 |
| readReference | Reference Explorer | 選択参照取得 | screen | selectedReference | activeReference | | | [[SCR-MW-REFERENCE-EXPLORER-VIEW]] | selectedReferenceIdから取得する |
| decideRole | Reference Explorer | 開く側を判定 | decision | openTargetRole | | | | | source / targetを判定する |
| useSourceAsset | Reference Explorer | 参照元Assetを選ぶ | data | activeReference | sourceAsset | | | | sourceAssetIdを使う |
| useTargetAsset | Reference Explorer | 参照先Assetを選ぶ | data | activeReference | targetAsset | | | | targetAssetIdを使う |
| validateTarget | Reference Explorer | 参照先解決確認 | decision | targetAsset | | | | | targetが空または未解決なら失敗 |
| showUnresolved | Reference Explorer | 未解決通知 | error | targetAsset | | | | [[SCR-MW-REFERENCE-EXPLORER-VIEW]] | unresolvedReferenceTargetを表示する |
| resolveAsset | Vault | Asset解決 | data | sourceAsset, targetAsset | openedFile | | | | 対象Asset IDからModelAssetを解決する |
| readPath | Vault | filePath取得 | data | openedFile | openedFilePath | [[RULE-MW-PATH-SHORTENER]] | | | 完全なVault相対pathを使う |
| validatePath | Vault | filePath確認 | decision | openedFilePath | | | | | 空またはVault内に存在しない場合は失敗 |
| showOpenFailed | Reference Explorer | open失敗通知 | error | openedFilePath | | | | [[SCR-MW-REFERENCE-EXPLORER-VIEW]] | openReferenceFileFailedを表示する |
| openFile | Obsidian | Markdownファイルを開く | external | openedFilePath | openedFile | | | | Obsidian WorkspaceまたはEditor APIで開く |
| updateViewer | Viewer | Viewer表示更新 | screen | openedFile | | | | | 必要に応じてViewerや診断表示を更新する |
| end | Reference Explorer | 終了 | end | | | | | | 処理終了 |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | readReference | | 選択取得 | |
| readReference | decideRole | | 開く側判定 | |
| decideRole | useSourceAsset | openTargetRole is source | source | |
| decideRole | useTargetAsset | openTargetRole is target | target | |
| useTargetAsset | validateTarget | | target確認 | |
| validateTarget | showUnresolved | target unresolved | 未解決 | |
| showUnresolved | end | | 中断 | |
| validateTarget | resolveAsset | target resolved | 解決済み | |
| useSourceAsset | resolveAsset | | source解決 | |
| resolveAsset | readPath | | path取得 | |
| readPath | validatePath | | path確認 | |
| validatePath | showOpenFailed | invalid path | 開けない | |
| showOpenFailed | end | | 中断 | |
| validatePath | openFile | valid path | 開く | |
| openFile | updateViewer | | Viewer更新 | |
| updateViewer | end | | 完了 | |

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

| path | notes |
|---|---|
| src/main.ts | symbol: openDiagnosticLocation; kind: function; ファイルジャンプ処理の参考 |
| src/core/vault-index.ts | symbol: VaultIndex; kind: class; ModelAssetとfilePathの参照元 |
| src/views/modeling-preview-view.ts | symbol: renderCurrentState; kind: method; 開いたファイルのViewer表示更新 |
