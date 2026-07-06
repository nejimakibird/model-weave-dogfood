---
type: er_entity
id: ENT-MW-MODEL-REFERENCE
logical_name: モデル間参照
physical_name: mw_model_reference
kind: model_reference
tags:
  - ModelWeave
  - ER
  - Repository
---

# モデル間参照 / mw_model_reference

## Overview

Markdown本文、frontmatter、mapping、screen、source links 等に現れる、他のモデルへの参照。

## Columns

| logical_name | physical_name | data_type | length | scale | not_null | pk | encrypted | default_value | notes |
|---|---|---|---:|---:|---|---|---|---|---|
| 参照ID | referenceId | string | | | Y | Y | N | | 参照単位の識別子 |
| 参照元アセットID | sourceAssetId | string | | | Y | N | N | | 参照元ModelAsset |
| 参照元セクション | sourceSection | string | | | N | N | N | | Fields / Mappings / Notes 等 |
| 参照元項目 | sourceField | string | | | N | N | N | | ref / rule / source_ref 等 |
| 参照値 | referenceValue | string | | | Y | N | N | | 記述された参照値 |
| 参照先アセットID | targetAssetId | string | | | N | N | N | | 解決できた場合 |
| 参照種別 | referenceType | string | | | Y | N | N | | wikilink / id / source_link 等 |
| 解決済み | resolved | boolean | | | Y | N | N | | 参照解決済みか |

## Indexes

| index_name | index_type | unique | columns | notes |
|---|---|---|---|---|
| pk_mw_model_reference | PRIMARY | Y | referenceId | 主キー |

## Relations

### REL-MODEL-REFERENCE-TO-SOURCE-ASSET
- target_table: [[ENT-MW-MODEL-ASSET]]
- kind: fk
- cardinality: n-1
- notes: 参照が記述されたモデル資産

| local_column | target_column | notes |
|---|---|---|
| sourceAssetId | assetId | アセットIDで対応 |

### REL-MODEL-REFERENCE-TO-TARGET-ASSET
- target_table: [[ENT-MW-MODEL-ASSET]]
- kind: fk
- cardinality: n-0..1
- notes: 参照テキストの解決先となるモデル資産

| local_column | target_column | notes |
|---|---|---|
| targetAssetId | assetId | アセットIDで解決 |


## Notes

- Markdownファイルが正本であり、ERは理解補助の派生ビューである。
- 未解決参照はDiagnosticの発生源になり得る。
- frontmatter内Wikilinkはダブルクォート必須とする既存ガイドに従う。

## Source Links

| path | notes |
|---|---|
| src/models/core/reference-resolver.ts | symbol: Reference; kind: type; 参照情報の内部表現 (未確認) |
