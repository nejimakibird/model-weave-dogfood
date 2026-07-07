---
id: STATUS-MW-IMPLEMENTATION-COVERAGE
name: Model Weave Dogfood Implementation Coverage
tags:
  - Status
  - Coverage
  - Guide
---

# Model Weave Dogfood Implementation Coverage

## Summary
Dogfoodモデルに含まれる設計資産を、現行Model Weave本体への実装状態に基づいて分類する。
この分類は、AI生成や設計レビュー時に「実装済み」と「将来構想」を混同しないための補助情報である。

この版では Model Weave 0.1.19 の source / docs を基準に、app_process Business Flow、Flow Diagram MVP、diagnostics guidance、Color Scheme preview editing、Domain Sources、color_scheme、Source Links、Impact / Relationship View、Weave Map、Settings、Render Mode の扱いを再整理する。dogfoodがどの本体リリースまで反映済みかは [[STATUS-MW-DOGFOOD-BASELINE]] で管理する。

## Status Categories

| status | meaning | notes |
|---|---|---|
| implemented | 現行Model Weave本体に実装済みとsource-backedに確認できる | 実装済み機能のdogfood精度を優先して高める |
| partial | 一部実装済み。ただしdogfoodモデル全体とは未対応 | 実装差分の確認が必要 |
| planned | 近い将来の拡張として自然だが、現時点では未実装または限定的 | 0.1.17以降のdogfood整備候補 |
| future | 将来構想・dogfood先行設計 | 本体に実装済みと断定しない |
| docs_only | docsやdesign noteに記載があるが、現行src実装としては未確認 | dogfoodへ反映する場合はimplementedではなく調査根拠として扱う |
| note_only | ガイド、README、TODOなど実装対象ではない | 補助情報 |

## Coverage Summary

| status | target | notes |
|---|---|---|
| implemented | class / er_entity / data_object / dfd_object / dfd_diagram / class_diagram / er_diagram | stable/primary formats。パース、インデックス、Preview、診断の実装を確認済み |
| implemented | app_process / Business Flow | 表形式 `Steps` / `Flows` の解析、Business Flow 表示、暗黙フロー、明示フロー、`Transitions` 表示 |
| implemented | Business Flow direction | toolbar override、frontmatter `flow_direction`、settings `defaultBusinessFlowDirection` による LR / TD 解決 |
| implemented | Step kind拡張 | start / process / decision / input / screen / subflow / end に加え、event / api / batch / message / data / store / wait / error / connector / external の形状と配色対象 |
| implemented | App Process Flow Connect Mode | Preview上でStepを2つ選び、`## Flows` に `from -> to` 行を追加する入力支援 |
| implemented | ref-aware Business Flow step interaction | `Steps`、`Inputs`、`Outputs` から参照先を解決し、hover preview / click target を提供 |
| implemented | domains / domain_diagram / Domain Sources | `type: domains`、`type: domain_diagram`、`## Domain Sources` の解析、統合、診断、Mindmap / Area / Tree 表示 |
| implemented | color_scheme | `type: color_scheme` の解析、settings `defaultColorSchemeRef`、Domain / DFD / Business Flow への配色適用 |
| implemented | Flow Diagram MVP | `type: flow_diagram`、`kind: screen_communication`、Objects / Flows解析、Internal Detail View描画、node / edge hover、`Flows.data` Wikilink診断 |
| implemented | Flow Diagram local endpoint diagnostics | `Flows.from` / `Flows.to` をローカル `Objects.id` として検証し、外部モデル参照とは区別する |
| implemented | DFD / Flow Diagram Flows.data Wikilink diagnostics | `Flows.data` のplain textはラベルとして扱い、Wikilinkだけ参照診断対象にする |
| implemented | Color Scheme preview editing | color_scheme画面で `## Colors` の fill / stroke / text セルをhex colorとして更新する |
| implemented | Applied Color Scheme compact display | Applied Color Scheme sectionを `Target / Kind / Preview / Notes / Source` のcompact tableで表示する |
| implemented | Viewer zoom / pan / fit / View Only | Viewer基本操作。Business Flow / Diagram / Weave Map でも派生的に利用 |
| implemented | diagnostics panel / current file diagnostics | 診断表示、クリックによる該当箇所オープン、UI言語に応じた診断文言 |
| implemented | diagnostics expected header guidance | schema-drivenなsection guidanceにより、table header診断へCopy Expected Headerを表示する |
| implemented | stable lower panel tabs | Review情報をDetails / Relationships / Diagnostics / Source Links / Mermaidの安定タブへまとめる。graph / non-graph review viewとrenderer切替後の表示維持を含む |
| implemented | diagnostics bulk Markdown copy | Diagnostics tabで全診断、errors、warnings、notesをMarkdownとして一括コピーする。[[DATA-MW-DIAGNOSTIC-COPY-ACTION]] に反映済み |
| implemented | diagnostics manual-edit guidance | 診断detailsにreference、expected header、frontmatter example、what happened / cause / manual fixなど手動修復に使う情報を表示する。[[DATA-MW-DIAGNOSTIC-MANUAL-GUIDANCE]] に反映済み |
| implemented | Quick Fix MVP for frontmatter id / name | missing frontmatter id / nameに限定した手動実行の修正支援。[[DATA-MW-DIAGNOSTIC-QUICK-FIX]] と [[PROC-MW-DIAGNOSTIC-QUICK-FIX-APPLY]] に反映済み。missing typeやMarkdown本文全般は自動修正しない |
| implemented | empty Markdown table row handling | 完全に空のtable data rowは編集ノイズとして無視し、部分入力行はrequired-fieldやinvalid-table-row診断対象にする。[[DATA-MW-CORE-MARKDOWN-TABLE]] と [[RULE-MW-PARSER-SECTION-STRUCTURE]] に反映済み |
| implemented | settings tab | per-format render mode、Business Flow direction、Domain view mode、color scheme、viewer preferences、Relationship View、Mermaid debug、UI language、Local source root |
| implemented | Render Mode resolution | toolbar override、frontmatter `render_mode`、settings default、format fallback。`auto` は deprecated frontmatter 値として診断後にfallback |
| implemented | PNG export | 図の派生出力 |
| implemented | Source Links section parsing / preview rendering | `## Source Links` の抽出、Preview内表示、summary chip、UI言語対応 |
| implemented | Source Links Copy / Open actions | Preview内Source Links表のコピー操作とファイルを開く操作。Local source root によるrelative path解決を含む |
| implemented | Related Source Links in relationship / impact summary | Impact Summary / Relationship View 内の関連Source Links集約 |
| implemented | Impact Summary / Relationship View | inbound / outbound / unresolved references、value usage、related Source Links、copy summary、open target |
| implemented | Weave Map | Impact Summary から派生する Weave Map model / Mermaid source / Relationship View 内の表示。新しいMarkdown形式ではない |
| implemented | Vault Index rebuild / relation and member lookups | buildVaultIndex、relation lookup、member lookup、diagnostics / validation 統合 |
| partial | Dogfood app_process coverage | 21件を棚卸しし、主要処理と実装済み入力支援はBusiness Flow化済み。future Explorer系とlogical / planned processは意図的にproseを維持 |
| partial | Viewer / Review Panel dogfood model | View Only、Focus mode、Review Summary、Diagnostics、Relationships / Impact、Source Links、Weave Map、Details、Mermaid Source、Applied Color Scheme、PNG exportは一部反映済み。mode別の下部Panel構成は追加確認が必要 |
| partial | Command registration dogfood coverage | main.tsにはRebuild index、Preview open、pane指定Preview open、template insertion、ER relation block、field completion、PNG exportのcommand登録がある。command palette入口は実装済みだが、dogfoodでは既存Preview / PNG / template処理への接続として扱い、専用モデル化はCOV-010A1で判断する |
| partial | v0.1.18 dogfood catch-up | Lower panel tabs、diagnostics bulk copy、manual guidance、Quick Fix MVP、empty table row handlingはsource-backedに確認できる。詳細dogfood反映はCOV-014A〜COV-014Eで追跡する |
| partial | Domain resolver / object context helper coverage | Domain Sources解決とDomain diagnosticsは実装済み。Domain診断のUI guidanceは既存Diagnostics rule / mappingへ最小反映済み。object context、subgraph、internal edge adaptersはClass / ER object preview用の派生描画補助として既存Renderer / Graph modelへ最小反映済み。内部helperの全量モデル化はしない |
| partial | DFD Domain Sources diagnostics | DFD描画側のDomain Sources解決は実装済みだが、Vault検証側ではObjects.domainがDomain Sources経由で解決できる場合にもローカルDomains不在警告が出る可能性がある。dogfood側で重複Domainsを追加せず、本体診断仕様の改善候補として扱う |
| partial | NotebookLM用バンドル | 本体source/docs内に専用実装は確認できない。dogfood運用上の派生成果物として扱う |
| planned | Weave Map dogfood data/process/screen refinement | 本体実装はあるが、dogfood側の専用モデル整備は今後の候補 |
| implemented | Connect Flow dogfood process/screen refinement | app_process Business Flow限定の `## Flows` 更新支援として、操作プロセス、状態データ、接続ルール、Viewer画面参照を整理済み |
| implemented | Step kind dogfood data/rule/mapping refinement | [[DATA-MW-APP-PROCESS-STEP]] / [[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]] / [[MAP-MW-APP-PROCESS-STEP-KIND-TO-MERMAID]] でBusiness Flow Step kindのshape / color境界を整理済み |
| future | Model Index View | dogfood先行設計。独立Viewとしての本体実装は確認しない |
| future | Reference Explorer | dogfood先行設計。独立Explorer画面としての本体実装は確認しない |
| future | Source Links Explorer | dogfood先行設計。Source Links section support と混同しない |
| future | Source Links Explorer indexing / filtering / selection | Explorer画面としての一覧・検索・選択はdogfood先行設計 |
| future | Flow Diagram Surface / Communication / projection views | v0.1.19 Flow Diagram MVPの範囲外。foldingやtransition coverage生成もfuture |
| docs_only | Commands docs stale render-mode wording | `docs/commands.md` には単一Default render modeとAutoが残るが、現行srcは形式別render mode設定とlegacy fallbackで扱う |
| docs_only | Weave Map design note future items | `docs/design/weave-map-mvp.md` のList / Map切替、node clicks、filtering、Custom Renderer等は現行dogfoodではfutureとして扱う |
| note_only | model/README.md / 99_notes guides | 補助ドキュメント |

## Release Reflection 0.1.14-0.1.17

| release | implementation focus | dogfood reflection status | notes |
|---|---|---|---|
| 0.1.14 | Weave Map / Impact Summary / Relationship View | implemented | 現行sourceでは Impact Summary、Relationship View、Weave Map、related Source Links、copy summary、open target を確認済み。dogfoodでは DATA / PROCESS / SCREEN / coverage notes に反映済み |
| 0.1.15 | Obsidian compatibility / lint / quality cleanup | note_only | 本体機能として新しいdogfood形式を追加する対象ではない。必要な場合のみ運用ノートで扱う |
| 0.1.16 | Viewer / Preview usability, Review Panel, Source Links summary, diagnostics, hover / click navigation | implemented / partial | Review Panel、Diagnostics、Relationships / Impact、Source Links、Weave Map は実装済みとして整理済み。Viewer細部の画面モデルは追加精査余地あり |
| 0.1.17 | Business Flow direction, Step kind extension, Flow Connect Mode, ref-aware Business Flow step navigation | implemented | [[PROC-MW-BUSINESS-FLOW-CONNECT-FLOW]]、[[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]]、[[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]]、[[MAP-MW-APP-PROCESS-STEP-KIND-TO-MERMAID]] に反映済み |
| 0.1.18 | Lower panel tabs, diagnostics bulk copy, manual guidance, Quick Fix MVP, empty table row handling | implemented / catch-up | source実装は確認済み。dogfood詳細反映は [[STATUS-MW-DOGFOOD-BASELINE]] と COV-014A〜COV-014Eで追跡する |
| 0.1.19 | Flow Diagram MVP, diagnostics guidance, Color Scheme preview editing, Applied Color Scheme compact display | implemented / future | Flow Diagram MVPは [[DATA-MW-FLOW-DIAGRAM-PARSED-DIAGRAM]]、[[PROC-MW-FLOW-DIAGRAM-PARSE]]、[[PROC-MW-FLOW-DIAGRAM-RENDER]] に反映済み。MVP外のprojection / folding / coverage生成はfuture |

## App Process Inventory Notes

| area | status | notes |
|---|---|---|
| table-based Business Flow processes | implemented | 既存の主要処理は `Steps.domain`、Domain Sources、現行 `Flows` ヘッダーへ更新済み |
| future Explorer / Model Index processes | future | Source Links Explorer、Reference Explorer、Model Index View は実装済みの Source Links section support や Relationship View と混同しない |
| logical / planned processes | partial / planned | reset defaults や Explorer由来のopen/select系は、source-backedな実装境界が弱い場合はproseを維持する |
| prose-only process conversion | deferred | 今回は新規Business Flow変換を追加しない。source-backedな分岐とUI実装が明確なものだけ後続で変換する |

## Continuous Coverage Management

機能網羅性点検は一度で完了するものではない。今後は [[TASKS-MW-DOGFOOD-COVERAGE]] を起点に、src / docs / dogfood の差分、不明点、判断、次アクションを分けて継続管理する。

| area | status | notes |
|---|---|---|
| coverage task board | note_only | [[TASKS-MW-DOGFOOD-COVERAGE]] で継続的な点検タスクを管理する |
| unknowns | partial / planned | source-backedと断定できないものはTASKSに戻し、即時implemented化しない |
| phased reflection | note_only | 小さなbatchでDATA / DFD / PROCESS / SCREEN / RULE / MAPPINGへ反映する運用方針 |
| Redmine dogfood method | note_only | 根拠、不明点、判断、次アクションを分ける進め方をModel Weave dogfoodにも適用する |

## Implemented First Policy

- 今後のdogfood整備では、implemented / partial のモデルを優先してソース実装と照合する。
- future のモデルは、将来機能の設計資産として扱い、現行実装済みと断定しない。
- Source Links Explorer / Reference Explorer / Model Index View は future として扱う。
- `## Source Links` セクションの解析、Preview内表示、Copy Path / Open、Relationship summary内のRelated Source Links集約は現行実装済みとして扱う。詳細は [[PROC-MW-SOURCE-LINKS-SECTION-SUPPORT]] を参照する。
- Source Links Explorerとしての一覧画面、Explorer専用のフィルタ、選択状態管理、Explorer索引は future として扱う。
- Source Linksのrelative path解決には現行実装済み設定 `localSourceRoot` を使用する。`enableSourceLinks` は現行ModelWeaveSettingsには存在しないため実装済み設定とは扱わない。
- app_process Business Flow は実装済みとして扱う。0.1.17時点では `flow_direction`、`defaultBusinessFlowDirection`、Step kind拡張、Flow Connect Mode、ref-aware hover / click target も実装済みである。Flow Connect Modeは [[PROC-MW-BUSINESS-FLOW-CONNECT-FLOW]] と [[DATA-MW-BUSINESS-FLOW-INTERACTION-STATE]]、Step kind拡張は [[RULE-MW-BUSINESS-FLOW-STEP-KIND-RENDERING]] を参照する。
- Flow Diagram MVPは実装済みとして扱う。ただし `dfd_diagram` とは別形式であり、`type: flow_diagram` のInternal Detail Viewだけをimplementedとする。Surface View、Communication View、folding、projection、transition coverage生成はfutureである。
- Color Scheme preview editingは実装済みとして扱うが、`## Colors` の fill / stroke / text セル更新に限定する。Diagnostics Quick Fixや汎用Markdown自動修正とは混同しない。
- v0.1.18 Quick Fix MVPは実装済みとして扱うが、missing frontmatter id / nameに限定する。[[DATA-MW-DIAGNOSTIC-QUICK-FIX]] と [[PROC-MW-DIAGNOSTIC-QUICK-FIX-APPLY]] を参照する。missing type、invalid table header、任意のMarkdown本文修正は自動修正しない。
- v0.1.18 diagnostics bulk Markdown copyは実装済みとして扱うが、ClipboardへMarkdown文字列を渡すcopy actionであり、Quick FixやMarkdown本文修正ではない。notesはinfo severityのUI分類である。
- v0.1.18 diagnostic manual-edit guidanceは実装済みとして扱うが、診断detailsに手動修復案を表示するUI支援であり、Quick FixやMarkdown本文自動修正ではない。
- v0.1.18 Markdown table row handlingは実装済みとして扱うが、完全空データ行だけをediting noiseとして除外する。部分入力行、列数不一致、ヘッダー不一致の診断を緩めるものではない。
- v0.1.18 catch-upはCOV-014で追跡する。baselineとrelease反映状況は [[STATUS-MW-DOGFOOD-BASELINE]] を参照する。
- Weave Map は Relationship View の派生表示として実装済みである。ただし `type: weave_map` のような新しい正本Markdown形式、専用パーサー、Source Links Explorer連携、フィルタ、強い整合性判定は future / out of scope として扱う。
- docs/designや古いcommands docsの記述は、srcで確認できるまで implemented 判定に使わない。特に単一Default render mode、Auto、Weave MapのList / Map切替やnode clicksはdocs_onlyまたはfutureとして扱う。
- NotebookLM用バンドルは本体実装済み機能として扱わない。必要な場合は、dogfood運用の派生ドキュメント生成手順として別管理する。
- 判断に迷う差分は、実装済みと断定せず [[TASKS-MW-DOGFOOD-COVERAGE]] に調査タスクとして残す。

## TODO

- 0.1.17でimplementedに寄せた領域について、未反映の細部を個別dogfoodモデルで確認する。
- 必要に応じて、各モデルファイルのfrontmatterに maturity を追加する。ただしFORMATや既存frontmatterへの影響を確認してから別タスクで行う。
- Explorer系モデルはSummary / Notes上でfuture明示済み。frontmatterでのmaturity付与は別タスク判断とする。
- `08_er/` と `08_message/` の番号衝突を別タスクで整理する。
- Viewer細部、Review Panel、Applied Color Scheme表示、NotebookLM用派生バンドルの運用境界を追加精査する。
- [[TASKS-MW-DOGFOOD-COVERAGE]] の高優先度タスクから、source-backedな小batchを順に切り出す。
