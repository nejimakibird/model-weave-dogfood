---
type: app_process
id: PROC-MW-SOURCE-LINKS-SECTION-SUPPORT
name: Source Linksセクションサポート
process_type: preview_support
kind: renderer
tags:
  - ModelWeave
  - SourceLinks
  - Process
---

# Source Linksセクションサポート

## Summary

Model Weave preview に実装済みの `## Source Links` セクション表示をサポートする。
この処理はモデルMarkdownから Source Links を解析し、preview に表示し、`localSourceRoot` によって相対パスを解決し、preview内の Copy Path / Open 操作を提供し、relationship / impact summary に関連 Source Links を含める。

この処理は Source Links Explorer をモデル化しない。Explorer画面、フィルタ、選択、索引化、Explorer専用のcopy/open workflow は future / planned であり、本処理の対象外である。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| sourceLinksSection | Markdown section lines | Model file `## Source Links` | N | モデル本文から解析されるテーブル行または単純な行エントリ |
| localSourceRoot | string | Plugin settings / viewer preferences | N | Source Link のpathが相対パスの場合に使用する |
| parsedModel | Parsed model | Preview / relationship analysis | Y | 解析済み Source Links を持つ可能性があるモデル |
| vaultIndex | Modeling vault index | Relationship / impact analysis | N | 関連 Source Links の収集時に使用する |

## Outputs

| id                 | data             | target                        | notes                                                         |
| ------------------ | ---------------- | ----------------------------- | ------------------------------------------------------------- |
| sourceLinks        | SourceLink       | Parsed model state            | path と任意の label / notes を持つ解析済み Source Links                  |
| previewSection     | HTMLElement      | Modeling preview              | path status、resolved path、notes、actions を持つ Source Links テーブル |
| copiedPath         | string           | Clipboard                     | Copy Path 操作でコピーされた resolved path                             |
| openRequest        | resolved path    | OS/default app                | open可能な resolved path に対する Open 操作要求                          |
| relatedSourceLinks | ImpactSourceLink | Relationship / impact summary | 現在のモデルおよび関連する inbound / outbound モデルから集約された Source Links      |

## Steps

| id | lane | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | Preview | Source Linksサポート開始 | start | parsedModel |  |  |  |  | Preview描画またはrelationship summary処理を開始する |
| receive | Preview | モデルファイル入力を受け取る | input | parsedModel | sourceLinksSection |  |  |  | モデル状態は `## Source Links` セクションを含む場合がある |
| parse | Parser | Source Linksセクションを解析する | process | sourceLinksSection | sourceLinks |  |  |  | テーブル行または単純な行エントリを受け付ける |
| hasLinks | Preview | 有効な Source Links があるか判定する | decision | sourceLinks |  |  |  |  | pathが空でない場合のみpreviewセクションを生成する |
| normalize | Parser | path、label、notesを正規化する | process | sourceLinks | sourceLinks |  |  |  | `path`、`source`、`source_path`、`file` などのpath headerをサポートする |
| resolve | Renderer | Source Link pathを解決する | process | sourceLinks, localSourceRoot | resolved path |  |  |  | file URI、absolute、UNC、relative pathを分類する |
| relativePath | Renderer | source root付き相対パスか判定する | decision | resolved path, localSourceRoot |  |  |  |  | 対応するabsolute rootの場合、relative pathは `localSourceRoot` を使用する |
| useLocalRoot | Renderer | 相対パスをlocalSourceRootへ結合する | process | localSourceRoot, source link path | resolved path |  |  |  | WindowsまたはPOSIX rootに対応するpath APIを使用する |
| keepResolvedPath | Renderer | 解決済みpathを維持する | process | source link path | resolved path |  |  |  | absolute pathや未対応rootでは `localSourceRoot` を使用しない |
| fileUri | Renderer | file URIが未対応か判定する | decision | resolved path |  |  |  |  | file URI pathはこのrendererではopenできない |
| markUnsupported | Renderer | pathを未対応として扱う | process | resolved path | source link status |  |  |  | unsupported file URI status と action note を付与する |
| renderPreview | Preview | Source Links preview tableを描画する | screen | source link status | previewSection |  |  |  | Path、Status、Resolved Path、Notes、Action列を表示する |
| copyPath | Preview | resolved pathをコピーする | screen | resolved path | copiedPath |  |  |  | Copy Path は resolved path をクリップボードへ書き込む |
| openable | Preview | resolved pathをopenできるか判定する | decision | source link status |  |  |  |  | open不可の場合、Open buttonをdisabledにする |
| openResolved | Preview | resolved pathをopenする | screen | resolved path | openRequest |  |  |  | Electron shell 経由でOS/default appへ要求する |
| collectRelated | Impact | 関連 Source Links を収集する | process | parsedModel, vaultIndex | relatedSourceLinks |  |  |  | self、resolved outbound target models、inbound source models を含める |
| end | Preview | preview / impact outputで終了する | end | previewSection, relatedSourceLinks |  |  |  |  | Source Links Explorer は対象外のままとする |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| hasLinks | collectRelated | `validSourceLinks.length === 0` | 有効なリンクなし | Rendererはpreview Source Linksセクションを返さない |
| hasLinks | normalize |  | 有効なリンクあり | previewセクション描画を続行する |
| relativePath | useLocalRoot | `linkPath.kind === "relative" && classified.kind !== "relative" && classified.kind !== "fileUri"` | localSourceRootを使用 | relative pathをlocalSourceRootへ結合する |
| relativePath | keepResolvedPath |  | pathを維持 | absolute path、file URI、empty root、未対応rootではlocalSourceRootを使用しない |
| useLocalRoot | fileUri |  | サポート確認 | status解決を続行する |
| keepResolvedPath | fileUri |  | サポート確認 | status解決を続行する |
| fileUri | markUnsupported | `resolved.kind === "fileUri"` | 未対応URI | file URI pathは表示されるがopen不可 |
| fileUri | renderPreview |  | open可能なpath種別 | preview描画を続行する |
| markUnsupported | renderPreview |  | statusを描画 | 未対応statusをテーブルへ描画する |
| openable | openResolved | `status.openable === true` | Open | OS/default appでopenする |
| openable | collectRelated | `status.openable === false` | openをスキップ | relationship / impact collectionへ進む |
| openResolved | collectRelated |  | 続行 | relationship / impact collectionへ進む |

## Notes

- Source Linksセクションサポートは、previewおよびrelationship / impact summaryの挙動として実装済みである。
- `localSourceRoot` はrelative Source Link pathを解決するための実装済み設定である。
- File URI pathは直接open動作では未対応として扱う。
- missing pathであっても、rendererの挙動に従ってstatus付きで表示され、copy/open可能な場合がある。
- Source Links Explorer はこの処理では実装済みとして扱わない。
- この処理はscreen、Explorer state、Explorer filtering、Explorer selection behaviorを定義しない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/parsers/source-links-parser.ts | parseSourceLinks | function | Parses `## Source Links` table and line entries |
| src/renderers/source-links-renderer.ts | renderSourceLinks | function | Renders preview table, path status, Copy Path, and Open actions |
| src/renderers/source-links-renderer.ts | resolveSourceLinkPath | function | Resolves relative Source Link paths using `localSourceRoot` |
| src/core/impact-analyzer.ts | collectRelatedSourceLinks | function | Collects Source Links for relationship / impact summary |
| src/core/impact-analyzer.ts | formatImpactSummaryAsMarkdown | function | Emits Related Source Links in impact summary Markdown |
| src/views/modeling-preview-view.ts | renderSourceLinks | usage | Adds Source Links sections to preview output |
| src/settings/model-weave-settings.ts | ModelWeaveSettings.localSourceRoot | setting | Stores the local source root used for relative path resolution |
