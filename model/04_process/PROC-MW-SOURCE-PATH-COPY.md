---
type: app_process
id: PROC-MW-SOURCE-PATH-COPY
name: Source Links Explorerソースパスをコピー
process_type: ui_action
tags:
  - ModelWeave
  - SourceLinks
  - Explorer
  - Process
---

# Source Links Explorerソースパスをコピー

## Summary
Source Links Explorerで選択されたSource Linkのpath / symbol / kindを、外部エディタや検索で利用できるようにクリップボードへコピーする処理。
表示上の短縮パスではなく、[[ENT-MW-SOURCE-LINK]].path の完全なパスを使用する。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| selectedSourceLink | [[ENT-MW-SOURCE-LINK]] | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].selectedSourceLinkId | Y | 選択中のSource Link |
| sourcePath | string | [[ENT-MW-SOURCE-LINK]].path | Y | コピー対象の実装ソースパス |
| sourceSymbol | string | [[ENT-MW-SOURCE-LINK]].symbol | N | コピー対象に含めるsymbol |
| sourceKind | string | [[ENT-MW-SOURCE-LINK]].kind | N | symbolの種別 |
| pathShortenerRule | [[RULE-MW-PATH-SHORTENER]] | Rule | N | 表示上の短縮パス方針。コピーには完全pathを使用 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| copiedSourcePath | string | Clipboard | コピーされた完全path |
| copiedSourceSymbol | string | Clipboard | 必要に応じてコピーされたsymbol |
| copyResultStatus | string | Source Links Explorer | copied / failed 等 |

## Steps

1. [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].selectedSourceLinkId から選択中の [[ENT-MW-SOURCE-LINK]] を取得する。
2. 選択中Source Linkから完全な path を取得する。
3. 表示上の短縮パスではなく、Source Linkが保持する完全な path を使用する。
4. path が空の場合は copySourcePathFailed メッセージを出して処理を中断する。
5. symbol が存在する場合は、path と symbol を併せてコピー対象に含めてよい。
6. kind が存在する場合は、symbolの補足情報としてコピー対象に含めてよい。
7. クリップボードへ path、必要に応じて symbol / kind をコピーする。
8. コピーに成功した場合は copyResultStatus=copied とする。
9. コピーに失敗した場合は copyResultStatus=failed とし、利用者に確認可能なメッセージを表示する。

## Messages

| id | text | severity | notes |
|---|---|---|---|
| sourcePathCopied | Source path copied. | info | 実装ソースパスをコピーした場合 |
| copySourcePathFailed | Failed to copy source path. | error | pathが空、不正、またはコピーできない場合 |
| noSourceLinkSelected | No Source Link selected. | warning | 選択中のSource Linkがない場合 |

## Notes
- 本処理はSource Links Explorerから実装ソースパスをコピーするUI操作である。
- Markdownモデル本文やfrontmatterは変更しない。
- [[RULE-MW-PATH-SHORTENER]] は表示用であり、コピーには完全なpathを使用する。
- Source Linkの path / symbol / kind / notes は [[ENT-MW-SOURCE-LINK]] の物理名を使用する。
- openSourcePathで直接開けない場合でも、copySourcePathにより外部エディタやOS検索へ渡せるようにする。
- copySourcePath は [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]] から呼び出される論理プロセスであり、本定義はその詳細に相当する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/vault-index.ts | VaultIndex | class | Source Links情報の索引元 |
| src/views/modeling-preview-view.ts | renderCurrentState | method | Viewer表示更新の参考 |