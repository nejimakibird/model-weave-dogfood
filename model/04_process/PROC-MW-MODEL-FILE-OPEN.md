---
type: app_process
id: PROC-MW-MODEL-FILE-OPEN
name: モデルファイルを開く
process_type: ui_action
tags:
  - ModelWeave
  - Vault
  - Index
  - Process
---

# モデルファイルを開く

## Summary

Model Index Viewで選択されたModelAssetから完全なfilePathを取得し、Obsidian上で該当Markdownファイルを開く処理。
表示上の短縮パスではなく、内部保持されたVault相対パスを使用する。
Model Weave 0.1.17時点では、診断やRelationship Viewからのファイルジャンプは実装済みだが、独立したModel Index View起点のopen処理はfuture / plannedとして扱う。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| selectedModelAsset | [[ENT-MW-MODEL-ASSET]] | [[SCR-MW-MODEL-INDEX-VIEW]].modelAssetList | Y | ユーザーが選択したモデル資産 |
| selectedFilePath | string | [[ENT-MW-MODEL-ASSET]].filePath | Y | 開く対象のVault相対パス |
| pathShortenerRule | [[RULE-MW-PATH-SHORTENER]] | Rule | N | 表示上の短縮パス方針。実際のジャンプには完全パスを使用 |
| vaultModelIndex | [[DATA-MW-VAULT-MODEL-INDEX]] | Index | N | 選択対象の元インデックス |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| openedFile | [[ENT-MW-MODEL-FILE]] | Obsidian Workspace | 開かれたMarkdownファイル |
| activeModelAsset | [[ENT-MW-MODEL-ASSET]] | Viewer / Editor | アクティブになったモデル資産 |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | Model Index | 開始 | start | selectedModelAsset | | | | [[SCR-MW-MODEL-INDEX-VIEW]] | future / planned のModel Index View起点 |
| readAsset | Model Index | 選択ModelAsset取得 | screen | selectedModelAsset | activeModelAsset | | | [[SCR-MW-MODEL-INDEX-VIEW]] | modelAssetListの選択行を読む |
| readPath | Vault | filePath取得 | data | activeModelAsset | selectedFilePath | [[RULE-MW-PATH-SHORTENER]] | | | 表示用短縮パスではなく内部保持pathを使う |
| validatePath | Vault | filePath確認 | decision | selectedFilePath | | | | | 空またはVault内に存在しない場合は失敗 |
| showFailure | Model Index | 失敗通知 | error | selectedFilePath | | | | [[SCR-MW-MODEL-INDEX-VIEW]] | openModelFileFailedを表示する |
| openFile | Obsidian | Markdownファイルを開く | external | selectedFilePath | openedFile | | | | Obsidian WorkspaceまたはEditor APIで開く |
| updateActive | Viewer | アクティブ状態更新 | process | openedFile | activeModelAsset | | | | 必要に応じてアクティブファイルとして扱う |
| refreshPreview | Viewer | Preview更新 | screen | activeModelAsset | | | | | Model Weave対象ならViewerや診断表示を更新する |
| end | Model Index | 終了 | end | | | | | | 処理終了 |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | readAsset | | 選択取得 | |
| readAsset | readPath | | path取得 | |
| readPath | validatePath | | 検証 | |
| validatePath | showFailure | invalid path | 開けない | |
| showFailure | end | | 中断 | |
| validatePath | openFile | valid path | 開く | |
| openFile | updateActive | | 状態更新 | |
| updateActive | refreshPreview | target is Model Weave file | Preview更新 | |
| updateActive | end | target is not Model Weave file | Preview更新なし | |
| refreshPreview | end | | 完了 | |

## Messages

| id | text | severity | notes |
|---|---|---|---|
| modelFileOpened | Model file opened. | info | ファイルを開いた場合 |
| openModelFileFailed | Failed to open model file. | error | filePathが不正またはVault内に存在しない場合 |
| noModelAssetSelected | No model asset selected. | warning | 選択対象がない場合 |

## Notes

- 本処理はfuture / plannedのModel Index Viewから対象Markdownファイルを開くUI操作である。
- Markdownモデル本文やfrontmatterは変更しない。
- [[RULE-MW-PATH-SHORTENER]] は表示用であり、実際のファイルオープンには完全なfilePathを使用する。
- openModelFile は [[SCR-MW-MODEL-INDEX-VIEW]] から呼び出される論理プロセスであり、本定義はその詳細に相当する。
- 診断パネルのジャンプ処理 openDiagnosticLocation と概念的に近いが、診断行ではなくModelAsset選択を起点とする。
- 現行実装済みの openReferencedFile / openDiagnosticLocation とは別に、Model Index View専用の選択状態は本体srcで確認できない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/main.ts | openDiagnosticLocation | function | ファイルジャンプ処理の参考 |
| src/views/modeling-preview-view.ts | renderCurrentState | method | 開いたファイルのViewer表示更新 |
| src/core/vault-index.ts | VaultIndex | class | ModelAssetとfilePathの参照元 |
