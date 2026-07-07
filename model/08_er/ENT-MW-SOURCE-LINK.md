---
type: er_entity
id: ENT-MW-SOURCE-LINK
logical_name: 実装ソース対応
physical_name: mw_source_link
kind: source_link
tags:
  - ModelWeave
  - ER
  - Repository
---

# 実装ソース対応 / mw_source_link

## Overview

Dogfoodモデルと実装ソース（TypeScript等）の対応関係を表す補助情報。

## Columns

| logical_name | physical_name | data_type | length | scale | not_null | pk | encrypted | default_value | notes |
|---|---|---|---:|---:|---|---|---|---|---|
| ソースリンクID | sourceLinkId | string | | | Y | Y | N | | Source Links行の識別子 |
| アセットID | assetId | string | | | Y | N | N | | 対応するModelAsset |
| ソースパス | path | string | | | Y | N | N | | source/model-weave-repo/... |
| シンボル | symbol | string | | | N | N | N | | 関数名 / class名 / type名 |
| 種別 | kind | string | | | N | N | N | | function / class / type / css 等 |
| 備考 | notes | string | | | N | N | N | | 対応内容の説明 |
| 確認済み | verified | boolean | | | N | N | N | | 実装との対応確認済みか |

## Indexes

| index_name | index_type | unique | columns | notes |
|---|---|---|---|---|
| pk_mw_source_link | PRIMARY | Y | sourceLinkId | 主キー |

## Relations

N/A (inbound only)

## Notes

- Markdownファイルが正本であり、ERは理解補助の派生ビューである。
- SourceLinkはDogfoodモデルから実装ソースへ辿るための補助情報である。
- 実装の正本はソースコードであり、SourceLinkは対応関係のメモである。

## Source Links

| path | notes |
|---|---|
| src/models/core/model-asset.ts | symbol: SourceLink; kind: type; ソースリンク情報の内部表現 (未確認) |
