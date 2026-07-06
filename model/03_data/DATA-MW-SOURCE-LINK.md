---
type: data_object
id: DATA-MW-SOURCE-LINK
name: Source Link
kind: source_link
data_format: object
tags:
  - ModelWeave
  - SourceLinks
  - Data
---

# Source Link

## Summary

`## Source Links` セクションから解析される、ソースファイル参照の1件分のデータ。
ParsedFileModel.sourceLinks に保持され、Preview内の Source Links 表示や Relationship / impact summary の関連Source Links集約に使われる。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| path | ソースパス | string | | Y | path | | SourceLink.path |
| label | 表示ラベル | string | | N | label | | SourceLink.label |
| notes | 補足説明 | string | | N | notes | | SourceLink.notes |

## Notes

- parseSourceLinks は table形式、Markdown link形式、bullet / line形式から SourceLink を生成する。
- parsed SourceLink 自体は path / label / notes のみを保持する。
- resolvedPath や openable status は renderSourceLinks 側の表示状態であり、SourceLink の保存データではない。
- relative path の解決には renderSourceLinks / resolveSourceLinkPath が localSourceRoot を使う。
- Source Links Explorer state は本データオブジェクトの範囲外である。

## Source Links

| path | notes |
|---|---|
| src/types/models.ts | symbol: SourceLink; kind: interface; parsed Source Link data |
| src/parsers/source-links-parser.ts | symbol: parseSourceLinks; kind: function; Source Links section解析 |
| src/renderers/source-links-renderer.ts | symbol: renderSourceLinks; kind: function; Preview表示とCopy Path / Open |
| src/renderers/source-links-renderer.ts | symbol: resolveSourceLinkPath; kind: function; relative path解決 |
| src/settings/model-weave-settings.ts | symbol: localSourceRoot; kind: field; relative path解決の設定値 |
