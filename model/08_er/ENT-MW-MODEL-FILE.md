---
type: er_entity
id: ENT-MW-MODEL-FILE
logical_name: モデルファイル
physical_name: mw_model_file
tags:
  - ModelWeave
  - ER
  - Repository
---

# モデルファイル / mw_model_file

## Overview

Vault内のMarkdownファイルそのものを表す。Model Weave管理対象外のMarkdownも含み得る。

## Columns

| logical_name | physical_name | data_type | length | scale | not_null | pk | encrypted | default_value | notes |
|---|---|---|---:|---:|---|---|---|---|---|
| ファイルパス | filePath | string | | | Y | Y | N | | Vault相対パス |
| ファイル基本名 | basename | string | | | Y | N | N | | 拡張子を除いたファイル名 |
| 拡張子 | extension | string | | | N | N | N | | 通常は md |
| 内容ハッシュ | contentHash | string | | | N | N | N | | 変更検知用 |
| 更新時刻 | modifiedAt | datetime | | | N | N | N | | Vault上の更新時刻 |
| frontmatter有無 | hasFrontmatter | boolean | | | Y | N | N | | YAML frontmatterを持つか |
| 検出種別 | detectedType | string | | | N | N | N | | detectFileTypeの結果 |
| 解析状態 | parseStatus | string | | | N | N | N | | parsed / error / ignored 等 |

## Indexes

| index_name | index_type | unique | columns | notes |
|---|---|---|---|---|
| pk_mw_model_file | PRIMARY | Y | filePath | 主キー |

## Relations

### REL-MODEL-FILE-TO-MODEL-ASSET
- target_table: [[ENT-MW-MODEL-ASSET]]
- kind: fk
- cardinality: 1-0..1
- notes: Markdownファイルから認識されたモデル資産

| local_column | target_column | notes |
|---|---|---|
| filePath | filePath | ファイルパスで対応 |

### REL-MODEL-FILE-TO-DIAGNOSTIC
- target_table: [[ENT-MW-DIAGNOSTIC]]
- kind: fk
- cardinality: 1-0..n
- notes: ファイルから検出された診断結果

| local_column | target_column | notes |
|---|---|---|
| filePath | filePath | 発生ファイルパスで対応 |

## Notes

- Markdownファイルが正本であり、ERは理解補助の派生ビューである。
- ファイル自体はObsidian Vault上のMarkdownであり、Model Weave独自DBに保存されるものではない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/models/core/file-entry.ts | FileEntry | type | ファイル情報の内部表現 (未確認) |