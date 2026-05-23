---
type: app_process
id: PROC-MW-PARSER-PARSE-VAULT-FILE
name: Vaultファイル解析
process_type: batch
kind: parser
tags:
  - Parser
  - Core
---

# Vaultファイル解析

## Summary

Obsidian Vault内の物理的なMarkdownファイルを読み込み、Model Weaveの各種モデル種別を判定した上で、構造化された論理モデルへとパースします。

## Triggers

| id | kind | source | event | notes |
|---|---|---|---|---|
| T1 | system | Vault Indexer | ファイル走査時 | |
| T2 | user | Obsidian | ファイル修正時 | |

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| source | [[DATA-MW-CORE-VAULT-FILE]] | Obsidian | Y | Vaultファイル情報 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| result | [[DATA-MW-CORE-PARSED-MODEL]] | Vault Index | 解析済みモデル |
| diag | [[DATA-MW-CORE-DIAGNOSTIC]] | Viewer | 診断情報 |

## Transitions

| id | event | to | condition | notes |
|---|---|---|---|---|
| TR1 | 解析成功 | PROC-MW-RENDERER-BUILD-GRAPH-MODEL | type が diagram 系の場合 | インデックス登録後に構築へ |
| TR2 | 解析失敗 | - | | 診断情報を更新して終了 |

## Steps

1. **Frontmatter解析**: `CLS-MW-PARSER-FRONTMATTER-PARSER.parseFrontmatter` を呼び出し、YAMLメタデータと本文を分離する。
2. **モデル種別判定**: `CLS-MW-PARSER-MODEL-TYPE-DETECTOR.detectFileType` を用いて、`type` フィールドからアセット種別を特定する。
3. **セクション分割**: 本文を `extractMarkdownSections` でヘッディング単位に分割し、文字列キーごとに複数の文字列値を保持するマップ構造を作る。
4. **具象パース**: 特定された種別に応じたパーサ（`CLS-MW-PARSER-CLASS-PARSER` 等）を呼び出し、`DATA-MW-CORE-MARKDOWN-TABLE` 等の構造データを抽出する。
5. **バリデーション**: `RULE-MW-PARSER-MARKDOWN-TABLE-SAFE-CELL` 等の運用ルールに基づき、整合性をチェックする。
6. **結果集約**: パース結果と発生した `DATA-MW-CORE-DIAGNOSTIC` を一つの `DATA-MW-CORE-PARSED-MODEL` にまとめて返す。

## Errors

- **E-PRS-001**: フロントマターの構文エラー（閉じ忘れ等）
- **E-PRS-002**: 未知の `type` 指定

## Notes

- パース処理は、物理ファイルへの依存を最小限にするため、パス情報ではなくテキスト内容に基づいて行われます。
- Markdown安全記法のため、複雑な型表現は山括弧表記ではなく自然文で説明します。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/vault-index.ts | parseVaultFile | function | ファイル種別に応じたパース処理の振り分け |
| src/core/vault-index.ts | indexSingleFile | function | 個別ファイルの解析とインデックス登録の制御 |
| src/parsers/frontmatter-parser.ts | parseFrontmatter | function | YAMLフロントマターの抽出 |
| src/core/vault-index.ts | indexSingleFile | function | 個別ファイルの解析とインデックス登録の制御 |
