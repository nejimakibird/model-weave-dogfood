---
type: rule
id: RULE-MW-PATH-SHORTENER
name: パス短縮表示ルール
kind: display_rule
tags:
  - Viewer
  - UI
  - Path
  - Rule
---

# パス短縮表示ルール

## Summary

Vault相対パスやソースパスをUI上で短く表示するためのルールを定義する。
本ルールは表示専用であり、実際の参照先パスやリンク先は変更しない。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| filePath | string | Viewer | Y | Vault相対パスまたは表示対象パス |
| viewerState | [[DATA-MW-VIEWER-STATE]] | Viewer | N | currentFilePathLabel の元情報 |
| diagnostic | [[DATA-MW-CORE-DIAGNOSTIC]] | Diagnostics | N | diagnosticSourceFile の元情報 |
| statusBar | [[SCR-MW-VIEWER-STATUS-BAR]] | UI | N | ステータスバー表示 |
| diagnosticsPanel | [[SCR-MW-VIEWER-DIAGNOSTICS-PANEL]] | UI | N | 診断パネル表示 |

## Conditions

- 表示幅に余裕がある場合はVault相対パスをそのまま表示してよい。
- 表示幅が狭い場合は、ファイル名を必ず残す。
- 親ディレクトリを1〜2階層残せる場合は `.../parent/file.md` 形式で表示する。
- さらに短縮が必要な場合は `.../file.md` 形式で表示する。
- 拡張子は原則として残す。
- 実際のリンク先やジャンプ対象として使う完全パスは保持する。
- Source Links のような実装ソースパスでも、表示だけ短縮してよい。
- 短縮表示はMarkdown本文やfrontmatterを書き換えない。

## Parameters

| name | value | notes |
|---|---|---|
| maxDisplayLength | 48 | 目安となる最大表示文字数 |
| preserveFileName | true | ファイル名は必ず残す |
| preserveExtension | true | 拡張子は原則残す |
| ellipsis | ... | 省略記号 |
| parentDepth | 1 | 表示可能なら親ディレクトリを1階層残す |

## Messages

| condition | message | severity | notes |
|---|---|---|---|
| path shortened | Path was shortened for display. | info | 表示上のみ短縮 |
| invalid path | Path cannot be displayed. | warning | パスが空または不正 |

## Notes

- 本ルールはUI表示専用であり、内部的な参照解決やファイルジャンプには完全パスを使用する。
- ステータスバーでは短縮表示、ツールチップやクリック遷移では完全パスを保持する方針が望ましい。
- 診断パネルではファイル名を優先し、必要に応じてセクション名や行番号と組み合わせて表示する。

## Source Links

| path | notes |
|---|---|
| src/views/modeling-preview-view.ts | symbol: renderCurrentState; kind: method; ファイル状態表示の起点 |
| src/main.ts | symbol: openDiagnosticLocation; kind: function; 完全パスを使った診断箇所ジャンプ |
