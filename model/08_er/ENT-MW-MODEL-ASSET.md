---
type: er_entity
id: ENT-MW-MODEL-ASSET
logical_name: モデル資産
physical_name: mw_model_asset
kind: model_asset
tags:
  - ModelWeave
  - ER
  - Repository
---

# モデル資産 / mw_model_asset

## Overview

Model Weaveが認識した論理的なモデル資産。data_object, rule, mapping, screen などを包含する。

## Columns

| logical_name | physical_name | data_type     | length | scale | not_null | pk  | encrypted | default_value | notes                               |
| ------------ | ------------- | ------------- | -----: | ----: | -------- | --- | --------- | ------------- | ----------------------------------- |
| アセットID       | assetId       | string        |        |       | Y        | Y   | N         |               | frontmatter.id                      |
| ファイルパス       | filePath      | string        |        |       | Y        | N   | N         |               | 元Markdownファイル                       |
| モデル種別        | modelType     | string        |        |       | Y        | N   | N         |               | data_object / rule / mapping 等      |
| 表示名          | name          | string        |        |       | Y        | N   | N         |               | frontmatter.name                    |
| サブ種別         | kind          | string        |        |       | N        | N   | N         |               | screen_type / enum / display_rule 等 |
| パッケージ        | package       | string        |        |       | N        | N   | N         |               | class等の論理階層                         |
| タグ           | tags          | string |        |       | N        | N   | N         |               | 分類用                                 |
| 描画モード        | renderMode    | string        |        |       | N        | N   | N         |               | custom / mermaid / auto             |
| 解析状態         | parseStatus   | string        |        |       | N        | N   | N         |               | parsed / warning / error            |

## Indexes

| index_name | index_type | unique | columns | notes |
|---|---|---|---|---|
| pk_mw_model_asset | PRIMARY | Y | assetId | 主キー |

## Relations

### REL-MODEL-ASSET-TO-MODEL-REFERENCE
- target_table: [[ENT-MW-MODEL-REFERENCE]]
- kind: fk
- cardinality: 1-0..n
- notes: モデル資産から定義された他モデルへの参照

| local_column | target_column | notes |
|---|---|---|
| assetId | sourceAssetId | 参照元アセットIDで対応 |

### REL-MODEL-ASSET-TO-DIAGNOSTIC
- target_table: [[ENT-MW-DIAGNOSTIC]]
- kind: fk
- cardinality: 1-0..n
- notes: モデル資産に関連する診断結果

| local_column | target_column | notes |
|---|---|---|
| assetId | assetId | アセットIDで対応 |

### REL-MODEL-ASSET-TO-SOURCE-LINK
- target_table: [[ENT-MW-SOURCE-LINK]]
- kind: fk
- cardinality: 1-0..n
- notes: モデル資産と実装ソースの対応

| local_column | target_column | notes |
|---|---|---|
| assetId | assetId | アセットIDで対応 |

## Notes

- Markdownファイルが正本であり、ERは理解補助の派生ビューである。
- ModelAssetはMarkdownファイルから解析された論理資産であり、ファイルそのものではない。
- 1つのModelFileから通常は0または1つのModelAssetが導出される。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/models/core/model-asset.ts | ModelAsset | type | モデル資産の内部表現 (未確認) |
