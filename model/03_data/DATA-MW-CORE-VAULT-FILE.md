---
type: data_object
id: DATA-MW-CORE-VAULT-FILE
name: Vaultファイル情報
tags:
  - Core
  - Data
---

# Vaultファイル情報

## Summary

Obsidian Vault内に存在する物理ファイルの実体と、OS/プラグインが管理するメタデータを保持するデータオブジェクトです。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| filePath | ファイルパス | string | | Y | | | Vaultルートからの相対パス |
| basename | ファイル基本名 | string | | Y | | | 拡張子を除いた名前 |
| extension | 拡張子 | string | | Y | | | 通常は md |
| content | テキスト内容 | string | | Y | | | Markdown全文 |
| mtime | 更新時刻 | number | | Y | | | エポックミリ秒 |
| sourcePluginContext | プラグイン文脈 | Record<string, any> | | N | | | キャッシュステータス等 |

## Notes

- インデックス構築プロセス（Vault Indexer）への主要な入力データとなります。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/vault-index.ts | indexSingleFile | function | 解析関数への入力オブジェクト構造に対応 |