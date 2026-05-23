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

1. [[SCR-MW-MODEL-INDEX-VIEW]].modelAssetList から選択中のModelAssetを取得する。
2. 選択中のModelAssetから完全な filePath を取得する。
3. 表示上の短縮パスではなく、内部保持された filePath を使用する。
4. filePath が空またはVault内に存在しない場合は、openModelFileFailed メッセージを出して処理を中断する。
5. Obsidian Workspace または Editor APIを使って該当Markdownファイルを開く。
6. 必要に応じて開いたファイルをアクティブファイルとして扱い、Viewer状態を更新する。
7. 開いたファイルがModel Weave対象であれば、Viewerプレビューや診断表示の再描画を行う。

## Messages

| id | text | severity | notes |
|---|---|---|---|
| modelFileOpened | Model file opened. | info | ファイルを開いた場合 |
| openModelFileFailed | Failed to open model file. | error | filePathが不正またはVault内に存在しない場合 |
| noModelAssetSelected | No model asset selected. | warning | 選択対象がない場合 |

## Notes

- 本処理はModel Index Viewから対象Markdownファイルを開くUI操作である。
- Markdownモデル本文やfrontmatterは変更しない。
- [[RULE-MW-PATH-SHORTENER]] は表示用であり、実際のファイルオープンには完全なfilePathを使用する。
- openModelFile は [[SCR-MW-MODEL-INDEX-VIEW]] から呼び出される論理プロセスであり、本定義はその詳細に相当する。
- 診断パネルのジャンプ処理 openDiagnosticLocation と概念的に近いが、診断行ではなくModelAsset選択を起点とする。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/main.ts | openDiagnosticLocation | function | ファイルジャンプ処理の参考 |
| src/views/modeling-preview-view.ts | renderCurrentState | method | 開いたファイルのViewer表示更新 |
| src/core/vault-index.ts | VaultIndex | class | ModelAssetとfilePathの参照元 |