---
type: app_process
id: PROC-MW-SOURCE-PATH-COPY
name: Source Linksソースパスをコピー
process_type: ui_action
tags:
  - ModelWeave
  - SourceLinks
  - Process
---

# Source Linksソースパスをコピー

## Summary
preview の `Source Links` セクションに表示された Source Link の resolved path を、外部エディタや検索で利用できるようにクリップボードへコピーする処理。
現行実装では Source Links Explorer ではなく、preview 内の Copy Path button から実行される。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Preview内Source Links copy操作に関わる実装領域 |

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| sourceLink | [[DATA-MW-SOURCE-LINK]] | Preview Source Links table | Y | pathが空でないSource Link |
| localSourceRoot | string | [[DATA-MW-PLUGIN-SETTINGS]].localSourceRoot | N | relative pathの解決に使用する |
| resolvedPath | string | `resolveSourceLinkPath` | Y | Copy Path buttonがコピーするpath |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| copiedPath | string | Clipboard | クリップボードへ書き込まれたresolved path |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | viewer_ui | Copy Path操作を開始する | start | sourceLink |  |  |  |  | Source Links table の行操作から開始する |
| receive | viewer_ui | Source Link行を受け取る | input | sourceLink | sourceLink.path |  |  |  | `renderSourceLinks` はpathが空でない行だけを描画する |
| resolve | renderer_area | pathをresolved pathへ解決する | process | sourceLink.path, localSourceRoot | resolvedPath |  |  |  | relative pathでは `localSourceRoot` が使用される場合がある |
| copy | viewer_ui | resolved pathをコピーする | screen | resolvedPath | copiedPath |  |  |  | `navigator.clipboard?.writeText` に渡す |
| end | viewer_ui | コピー処理を終了する | end | copiedPath |  |  |  |  | 現行実装では完了通知は出さない |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | receive |  | 開始 | preview行操作 |
| receive | resolve |  | pathあり | 有効なSource Link行だけが対象 |
| resolve | copy |  | resolved path | 解決後のpathをコピー対象にする |
| copy | end |  | 終了 | clipboard書き込み要求後に終了する |

## Messages

| id | text | severity | notes |
|---|---|---|---|
| none |  | info | 現行実装ではCopy Path成功/失敗のNoticeは明示されない |

## Notes
- 本処理は現行実装済みの preview Source Links table から実装ソースパスをコピーするUI操作である。
- Markdownモデル本文やfrontmatterは変更しない。
- コピー対象は `sourceLink.path` そのものではなく、Source Links renderer が解決した `resolvedPath` である。
- `navigator.clipboard?.writeText` は失敗通知を明示的には出さない。
- Source Links Explorer からのcopy workflowは future / planned であり、この処理では実装済みとして扱わない。
- `Steps.domain` は [[DOMAINS-MW-ARCHITECTURE]] のDomain idを参照する。Source Links Explorerのfuture workflowとは分けて扱う。

## Source Links

| path | notes |
|---|---|
| src/renderers/source-links-renderer.ts | symbol: renderSourceLinks; kind: function; Copy Path buttonを生成し、resolved pathをclipboardへ渡す |
| src/renderers/source-links-renderer.ts | symbol: resolveSourceLinkPath; kind: function; Source Link pathをresolved pathへ解決する |
| src/views/modeling-preview-view.ts | symbol: renderSourceLinks; kind: usage; previewへSource Links sectionを追加する |
