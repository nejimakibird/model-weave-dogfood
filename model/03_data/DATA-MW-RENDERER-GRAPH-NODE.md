---
type: data_object
id: DATA-MW-RENDERER-GRAPH-NODE
name: 抽象グラフノード
kind: model
data_format: object
tags:
  - Renderer
  - Data
---

# 抽象グラフノード

## Summary

描画ライブラリに依存しない、解決済み図面における単一の頂点（ノード）を表します。
現行ソースでは DiagramNode に対応し、resolveDiagramRelations 後は解決済み object を持つ場合があります。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| id | ノードID | string | | Y | id | | ノード識別子 |
| ref | 元参照 | string | | N | ref | | diagram側のobject ref |
| label | 表示ラベル | string | | N | label | | 表示名 |
| kind | 種別 | string | | N | kind | | ObjectKindまたはDfdDiagramObjectKind |
| metadata | メタデータ | object | | N | metadata | | DFD行情報などの追加情報 |
| object | 解決済みモデル | object | | N | object | [[DATA-MW-CORE-PARSED-MODEL]] | ObjectModel / ErEntity / DfdObjectModel |

## Notes

- `DATA-MW-RENDERER-GRAPH-MODEL` の構成要素です。
- sourceRef は現行 DiagramNode の直接フィールドではなく、元参照は ref として保持される。
- object は ResolvedDiagram.nodes で付与される解決済みモデルであり、基礎の DiagramNode interface では任意拡張として扱われる。
- metadata は任意のRecord構造で、DFDのrowIndexやlocal状態などを保持する場合がある。
- Markdownテーブル安全性のため、任意性やRecord構造は required / notes で表現します。

## Source Links

| path | notes |
|---|---|
| src/types/models.ts | symbol: DiagramNode; kind: interface; グラフ構造における頂点情報 |
| src/types/models.ts | symbol: ResolvedDiagram; kind: interface; object付きnodeの出力形 |
| src/core/relation-resolver.ts | symbol: resolveDiagramRelations; kind: function; node解決 |
