---
type: app_process
id: PROC-MW-SOURCE-PATH-OPEN
name: Source Linksソースパスを開く
process_type: ui_action
tags:
  - ModelWeave
  - SourceLinks
  - Process
---

# Source Linksソースパスを開く

## Summary
preview の `Source Links` セクションに表示された Source Link の resolved path を、OS/default app へ渡して開く処理。
現行実装では Source Links Explorer ではなく、preview 内の Open button から実行される。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| sourceLink | [[DATA-MW-SOURCE-LINK]] | Preview Source Links table | Y | pathが空でないSource Link |
| localSourceRoot | string | [[DATA-MW-PLUGIN-SETTINGS]].localSourceRoot | N | relative pathの解決に使用する |
| resolvedPath | string | `resolveSourceLinkPath` | Y | Open buttonがOSへ渡すpath |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| openRequest | string | OS/default app | `electron.shell.openPath` に渡されたresolved path |
| openNotice | string | Obsidian Notice | open不可または失敗時の通知 |

## Steps

| id | lane | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | Preview | Open操作を開始する | start | sourceLink |  |  |  |  | Source Links table の行操作から開始する |
| receive | Preview | Source Link行を受け取る | input | sourceLink | sourceLink.path |  |  |  | `renderSourceLinks` はpathが空でない行だけを描画する |
| resolve | Renderer | pathをresolved pathへ解決する | process | sourceLink.path, localSourceRoot | resolvedPath |  |  |  | relative pathでは `localSourceRoot` が使用される場合がある |
| status | Renderer | path statusを判定する | decision | resolvedPath | source link status |  |  |  | file URI はopen不可として扱う |
| disabled | Preview | Open buttonを無効化する | end | source link status |  |  |  |  | `status.openable === false` の場合 |
| requestOpen | Preview | OS openを要求する | screen | resolvedPath | openRequest |  |  |  | `electron.shell.openPath` を呼び出す |
| available | Runtime | OS open APIが利用可能か判定する | decision | openRequest |  |  |  |  | `electron.shell.openPath` が関数か確認する |
| unavailable | Runtime | open不可を通知する | end | openRequest | openNotice |  |  |  | OS open APIがない場合 |
| handleResult | Runtime | open結果を確認する | decision | openRequest | openNotice |  |  |  | `openPath` の戻り値が空なら成功扱い |
| failed | Runtime | open失敗を通知する | end | openNotice |  |  |  |  | 戻り値または例外messageをNotice表示する |
| end | Runtime | open処理を終了する | end | openRequest |  |  |  |  | 正常終了時はNoticeを出さない |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | receive |  | 開始 | preview行操作 |
| receive | resolve |  | pathあり | 有効なSource Link行だけが対象 |
| resolve | status |  | status判定 | open可否を判定する |
| status | disabled | `status.openable === false` | open不可 | Open buttonはdisabledになる |
| status | requestOpen | `status.openable === true` | Open | resolved pathをOSへ渡す |
| requestOpen | available |  | API確認 | Electron shellのopenPathを確認する |
| available | unavailable | `typeof electron.shell?.openPath !== "function"` | APIなし | Noticeを表示して終了する |
| available | handleResult |  | APIあり | openPathを呼び出す |
| handleResult | failed | `result` | 失敗 | openPathがmessageを返した場合 |
| handleResult | end |  | 成功 | openPathが空文字を返した場合 |

## Messages

| id | text | severity | notes |
|---|---|---|---|
| osOpenUnavailable | Could not open Source Link: OS open is not available. | warning | `electron.shell.openPath` が利用できない場合 |
| sourcePathOpenFailed | Could not open Source Link: <message> | warning | `openPath` の戻り値または例外messageを表示する場合 |

## Notes
- 本処理は現行実装済みの preview Source Links table から実装ソースパスを開くUI操作である。
- Markdownモデル本文やfrontmatterは変更しない。
- Open対象は `sourceLink.path` そのものではなく、Source Links renderer が解決した `resolvedPath` である。
- file URI は表示されるがopen不可として扱われる。
- missing pathやsource root外pathでも、statusに応じてopen可能な場合がある。
- Source Links Explorer からのopen workflowは future / planned であり、この処理では実装済みとして扱わない。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/renderers/source-links-renderer.ts | renderSourceLinks | function | Open buttonを生成し、openable状態を反映する |
| src/renderers/source-links-renderer.ts | resolveSourceLinkPath | function | Source Link pathをresolved pathへ解決する |
| src/renderers/source-links-renderer.ts | openResolvedSourcePath | function | Electron shellへresolved pathのopenを要求する |
| src/views/modeling-preview-view.ts | renderSourceLinks | usage | previewへSource Links sectionを追加する |
