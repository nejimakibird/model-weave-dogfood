---
type: app_process
id: PROC-MW-SOURCE-PATH-OPEN
name: Source Links Explorerソースパスを開く
process_type: ui_action
tags:
  - ModelWeave
  - SourceLinks
  - Explorer
  - Process
---

# Source Links Explorerソースパスを開く

## Summary
Source Links Explorerで選択されたSource Linkのpathをもとに、対応する実装ソースファイルを開く処理。
表示上の短縮パスではなく、[[ENT-MW-SOURCE-LINK]].path の完全なパスを使用する。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| selectedSourceLink | [[ENT-MW-SOURCE-LINK]] | [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].selectedSourceLinkId | Y | 選択中のSource Link |
| sourcePath | string | [[ENT-MW-SOURCE-LINK]].path | Y | 開く対象の実装ソースパス |
| sourceSymbol | string | [[ENT-MW-SOURCE-LINK]].symbol | N | 開いた後に確認したい関数名/型名/クラス名など |
| sourceKind | string | [[ENT-MW-SOURCE-LINK]].kind | N | symbolの種別 |
| pathShortenerRule | [[RULE-MW-PATH-SHORTENER]] | Rule | N | 表示上の短縮パス方針。実際のジャンプには完全pathを使用 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| openedSourcePath | string | External Editor / Obsidian Workspace | 実際に開いた実装ソースパス |
| activeSourceLink | [[ENT-MW-SOURCE-LINK]] | Source Links Explorer | ファイルオープンの起点となったSource Link |
| openResultStatus | string | Source Links Explorer | opened / failed / unsupported 等 |

## Steps

1. [[DATA-MW-SOURCE-LINK-EXPLORER-STATE]].selectedSourceLinkId から選択中の [[ENT-MW-SOURCE-LINK]] を取得する。
2. 選択中Source Linkから完全な path を取得する。
3. 表示上の短縮パスではなく、Source Linkが保持する完全な path を使用する。
4. path が空の場合は openSourcePathFailed メッセージを出して処理を中断する。
5. path がVault内Markdownファイルを指す場合は、Obsidian Workspaceで該当ファイルを開く。
6. path がリポジトリ内ソースファイルを指す場合は、利用可能な方法で外部エディタまたはOSの関連付けに委ねて開く。
7. path がVault外または存在確認できない場合は、openSourcePathUnsupported または openSourcePathFailed として扱う。
8. symbol が指定されている場合は、開いた後に該当symbolを探すための手がかりとして保持する。
9. 開く処理に成功した場合は openResultStatus=opened とする。
10. 開く処理に失敗した場合は openResultStatus=failed とし、利用者に確認可能なメッセージを表示する。

## Messages

| id | text | severity | notes |
|---|---|---|---|
| sourcePathOpened | Source path opened. | info | 実装ソースパスを開いた場合 |
| openSourcePathFailed | Failed to open source path. | error | pathが空、不正、または開けない場合 |
| openSourcePathUnsupported | Source path cannot be opened directly. | warning | Vault外や外部エディタ連携が必要な場合 |
| noSourceLinkSelected | No Source Link selected. | warning | 選択中のSource Linkがない場合 |

## Notes
- 本処理はSource Links Explorerから実装ソースパスを開くUI操作である。
- Markdownモデル本文やfrontmatterは変更しない。
- [[RULE-MW-PATH-SHORTENER]] は表示用であり、実際のファイルオープンには完全なpathを使用する。
- Source Linkの path / symbol / kind / notes は [[ENT-MW-SOURCE-LINK]] の物理名を使用する。
- 実装ソースがObsidian Vault外にある場合、直接ジャンプできない可能性がある。その場合はcopySourcePathや外部エディタでの利用を促す。
- `openSourcePath` は [[SCR-MW-SOURCE-LINK-EXPLORER-VIEW]] から呼び出される論理プロセスであり、本定義はその詳細に相当する。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/main.ts | openDiagnosticLocation | function | ファイルジャンプ処理の参考 |
| src/core/vault-index.ts | VaultIndex | class | Source Links情報の索引元 |
| src/views/modeling-preview-view.ts | renderCurrentState | method | Viewer表示更新の参考 |