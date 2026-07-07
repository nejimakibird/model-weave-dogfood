---
id: TASKS-MW-DOGFOOD-COVERAGE
name: Model Weave Dogfood Coverage Tasks
tags:
  - Tasks
  - Coverage
  - Dogfood
  - AI
---

# Model Weave Dogfood Coverage Tasks

## 概要

このノートは、Model Weave dogfood model の機能網羅性点検、不明点管理、段階的反映を継続するための作業台である。

目的は、一度のAI生成で仕様書を完成させることではない。現行の `model-weave/src`、`model-weave/docs`、`model-weave-dogfood/model` の差分を継続的に検出し、根拠があるものから小さくdogfoodへ反映する。

## 運用方針

- source-backed な挙動だけを `implemented` として扱う。
- sourceで一部のみ確認できるものは `partial` とし、足りない確認をタスク化する。
- docsやdogfoodにだけ存在する構想は、`planned`、`future`、`note_only` に逃がす。
- 不明点はすぐ実装済み扱いにせず、このTASKSに残す。
- 生成済みdogfoodモデルを正とは見なさず、src / docs / dogfood の差分を毎回確認する。
- DATAとDFDを先に整え、Process / Business Flow は入力、出力、境界が見えたものから変換する。
- Explorer系やlogical / planned processを、実装済みのViewer機能と混同しない。
- 変更は小さなbatchに分け、Markdown tableのFORMATと日本語表現を壊さない。

## タスク一覧

| id | area | status | priority | question | source evidence | dogfood target | finding | decision | next action | notes |
|---|---|---|---|---|---|---|---|---|---|---|
| COV-001 | 全体網羅性 | 要追加調査 | high | src / docs / dogfood の主要機能差分はどこに残っているか | model-weave/src, model-weave/docs | model-weave-dogfood/model | 0.1.17対応で主要線引きは整理済み | 継続タスク化 | 機能別に小さな調査batchを作る | 人間側で範囲を狭く固定しすぎない |
| COV-002 | Viewer / Review Panel | 部分反映済み | high | Viewer細部とReview Panelのscreen / data / mappingは実装に追随しているか | modeling-preview-view.ts, zoom-toolbar.ts, mermaid-shared.ts, v0.1.16 release docs | SCR-MW-VIEWER-MAIN-FRAME, DATA-MW-VIEWER-STATE, MAP-MW-VIEWER-STATE-TO-MAIN-FRAME-UI | Review Summary、下部Review Panel slots、Source Links、Diagnostics、Relationships / Impact、Weave Map、Details、Mermaid Source、Applied Color Scheme、View Only、Focus mode、PNG exportをsource-backedで確認 | implemented部分を最小反映し、Viewer細部全体はpartial維持 | COV-002Aでmode別Review Panel差分と実機表示を追加確認する | Source Links ExplorerやReference Explorerとは混同しない |
| COV-002A | Viewer / Review Panel mode別差分 | 反映済み | medium | object / summary / domains / diagramごとの下部Review Panel構成差はdogfoodに十分表現できているか | modeling-preview-view.ts | 09_screen, 03_data, 06_mapping | object / dfd-object / domains / domain-diagramはbottomPane、diagramはlower slots、color_scheme / relationsは標準Review Panelなし | source-backedなmode別表示表と派生状態を最小反映 | COV-002Bで実機Preview表示を確認する | 大きなscreen redesignは避けた |
| COV-002B | Viewer / Review Panel実機確認 | 確認済み | low | mode別lower paneが実機Previewでdogfood記述どおり表示されるか | Model Weave preview, representative dogfood files | 09_screen, 99_notes | 開発時Preview確認済みとして扱う | COV-002Aの追加調査をクローズ | 今後の表示差分は別タスクで扱う | 表示差分が再発した場合はCOV-002Aの補正ではなく新規タスクにする |
| COV-003 | Diagnostics | 部分反映済み | high | DiagnosticsのDATA / RULE / SCREEN / PROCESSは現在の診断収集と表示に合っているか | current-file-diagnostics.ts, validator.ts, warnings.ts, modeling-preview-view.ts, main.ts | DATA-MW-CORE-DIAGNOSTIC, diagnostics process, diagnostics screen, diagnostics mapping, severity rule | ValidationWarning構造、info / warning / error、current-file診断、Vault検証、Preview表示、Open Location、copy guidanceを確認 | source-backedな表示とコピー支援を反映し、汎用Markdown自動修正はfuture維持 | COV-003Aで診断コード別guidanceの網羅性を追加調査する | Parser, resolver, validator, preview diagnosticsを分ける。missing frontmatter id / name限定Quick Fix MVPはCOV-014Dで追跡 |
| COV-003A | Diagnostics guidance matrix | 部分反映済み | medium | 診断コード別の表示詳細やcopy guidanceをdogfoodにどこまで持たせるか | warnings.ts, current-file-diagnostics.ts, validator.ts, parser files, modeling-preview-view.ts | RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION, MAP-MW-RENDERER-DIAGNOSTIC-TO-UI, SCR-MW-VIEWER-DIAGNOSTICS-PANEL | guidanceはcode単独ではなくmessage / contextから汎用導出される。reference、expected header、frontmatter example、render_mode detailsを確認 | 高価値なUI guidanceだけrule化し、全code詳細モデル化は避ける | COV-003Bでcode別message / context生成元を必要に応じて棚卸しする | 汎用Quick FixやMarkdown本文自動修正はfuture。frontmatter id / name限定Quick Fix MVPはCOV-014Dで扱う |
| COV-003B | Diagnostics code source inventory | 要追加調査 | low | VALIDATION_WARNING_CODESごとの生成元message / contextを全量管理する必要があるか | parser files, validator.ts, relation-resolver.ts, render-mode.ts | 05_rule, 99_notes | 有効code一覧は確認済みだが、生成元ごとのmessage variationは多い | deferred | 実害が出たcodeから追加でsource-backedに整理する | 全code詳細表を無理に作らない |
| COV-004 | PNG Export | 反映済み | medium | PNG Export processを専用app_process化すべきか | modeling-preview-view.ts, png-export.ts, zoom-toolbar.ts, renderer周辺 | PROC-MW-VIEWER-EXPORT-PNG, SCR-MW-VIEWER-MAIN-FRAME, DATA-MW-VIEWER-STATE, MAP-MW-VIEWER-STATE-TO-MAIN-FRAME-UI | export button、Export and Open、export surface、offscreen描画、exports folder保存、desktop open、Weave Map exportをsource-backedで確認 | 専用processを追加し、Viewer StateとUI mappingは出力可否と操作境界だけ反映 | COV-004Aで実機PNGの見た目確認を必要に応じて行う | Markdown正本ではなく派生出力。toolbar / Review Panel / Diagnostics / lower paneは対象外 |
| COV-004A | PNG Export実機確認 | 要追加調査 | low | 実機PreviewでPNGにColor Schemeと図本体だけが反映されるか | Model Weave preview, representative dogfood files | 04_process, 09_screen | source上は描画済みsurfaceの色とboundsを使うことを確認 | manual verificationへ分離 | DFD、Business Flow、Weave Mapで代表出力を確認する | source-backed model更新はCOV-004で完了 |
| COV-005 | Applied Color Scheme | 反映済み | high | Applied Color Scheme sectionの表示とdogfoodのscreen / mappingは整合しているか | modeling-preview-view.ts, applied-color-scheme-renderer.ts, color-scheme.ts, weave-map-mermaid.ts, FORMAT-color_scheme.md | SCR-MW-VIEWER-MAIN-FRAME, DATA-MW-VIEWER-STATE, PROC-MW-PREVIEW-RENDER-MODEL, PROC-MW-VIEWER-EXPORT-PNG, RULE-MW-VIEWER-GLOBAL-SETTINGS-RESOLUTION, MAP-MW-VIEWER-STATE-TO-MAIN-FRAME-UI, COLOR-MW-DOGFOOD | Applied Color Scheme sectionはcolorSchemeとview targetがある場合に表示され、configured / built-in行を区別する。未設定や未解決はbuilt-in fallback。Weave Mapはtarget=weave_map行だけでlayer既定色を上書きする | source-backedな表示条件、fallback、target別表示、PNG Export境界を最小反映 | COV-005Aで実機PreviewのApplied Color Scheme表示を必要に応じて確認する | Color Scheme適用はMarkdown正本を書き換えない。Applied Color Scheme section自体はPNG対象外 |
| COV-005A | Applied Color Scheme実機確認 | 要追加調査 | low | Applied Color Scheme sectionがmode別に想定どおり表示されるか | Model Weave preview, representative dogfood files | 09_screen, 03_data, 10_color_scheme | source上は表示条件とtarget別行生成を確認 | manual verificationへ分離 | domains、DFD、Business Flow、Relationship / Weave Mapで代表表示を確認する | source-backed model更新はCOV-005で完了 |
| COV-006 | app_process変換候補 | 反映済み | medium | prose / 簡易型app_processのうちBusiness Flow化すべきものはどれか | FORMAT-app_process.md, app-process-parser.ts, app-process-business-flow.ts, app-process-flow-editor.ts, app-process-step-interaction-target.ts, modeling-preview-view.ts | 04_process | 22件を棚卸し。主要source-backed処理はすでに表形式Business Flow化済み。残るprose中心のopen / reset / Explorer系はfuture、planned、logical、またはUI構想寄りであり、今回追加変換しない判断 | 追加Business Flow化なし。既存表形式の維持とfuture/prose維持を明確化 | COV-006Aで必要になった場合だけ個別processを再評価する | Explorer / Model Index系futureはprose維持。Flowsは内部Steps.id、Transitionsは外部出口として扱う |
| COV-006A | app_process個別再評価 | 要追加調査 | low | proseのまま残したopen / reset系を将来source-backedに表形式化する必要があるか | future UI implementation, settings implementation | 04_process | 現時点では実装済みscreenや専用handlerが薄いものを無理に変換しない | deferred | 実装が追加された時点で個別にBusiness Flow化を判断する | MODEL-FILE-OPEN、REFERENCE-FILE-OPEN、SETTINGS-RESETなど |
| COV-007 | Explorer / Model Index future横展開 | 反映済み | high | Explorer / Model Index系DATA / SCREEN / MAPPINGにもfuture明示が一貫しているか | modeling-preview-view.ts, source-links-renderer.ts, impact-analyzer.ts, vault-index.ts, dogfood Explorer files | 03_data, 06_mapping, 09_screen, 04_process | srcには独立したSource Links Explorer / Reference Explorer / Model Index Viewを確認できない。内部Vault Index、Source Links section support、Relationship View / Impact Summary / Weave Mapは実装済み | Explorer / Model Index系はfuture / planned維持。実装済みViewer機能とは分離 | 実装が追加された時点でDATA / SCREEN / MAPPINGをimplementedへ再判定する | Source Links section support、Relationship View、Weave Map、Vault Index rebuildと混同しない |
| COV-008 | NotebookLM派生バンドル | 要追加調査 | medium | NotebookLM用バンドルはdogfood運用でどこまで管理するか | docs, dogfood notes | 99_notes, derived outputs | 本体機能ではない | note_only | 運用手順と出力物の置き場所を整理する | Model Weave formatとして扱わない |
| COV-009 | docsのみ仕様 | 部分反映済み | medium | docsにあるがsrcで確認できない仕様はどれか | commands.md, V0.8-rendering-policy.md, weave-map-mvp.md, main.ts, model-weave-settings.ts | STATUS, TASKS, relevant model | docs/commands.mdには単一Default render modeとAutoが残るが、srcは形式別render mode設定とlegacy fallback。weave-map-mvp.mdにはList / Map切替、node clicks、filtering、Custom Renderer等のfuture項目が残る | docs_only / futureとしてSTATUSに記録し、dogfoodの実装済み分類へは取り込まない | COV-009AでFORMAT docsとparser / renderer差分を必要に応じて個別確認する | docsを無条件に実装済み根拠にしない |
| COV-009A | FORMAT docs差分確認 | 要追加調査 | low | FORMAT docsにあるがparser / rendererで未確認の列や挙動は残っているか | docs/formats, docs/ja/formats, src/parsers, src/renderers | FORMAT関連dogfood, STATUS | COV-009ではcommandsとdesign noteの高リスク箇所を優先確認 | deferred | format別に小さく確認する | 大規模なdocs同期やparser変更はしない |
| COV-010 | srcのみ仕様 | 部分反映済み | high | srcにあるがdocs/dogfoodが薄い仕様はどれか | main.ts, command registration, domain resolver files, relation-resolver.ts, current-file-diagnostics.ts, png-export.ts | 99_notes, relevant model folders | src scanでCommand registration、domain resolver / diagnostics / tree、object context / subgraph / internal edge、relation resolver diagnostic edge cases、legacy / special preview viewsをdogfood薄め候補として確認。Flow Connect、Color Scheme、PNG Export、Source Links、settingsは既存dogfoodで概ね反映済み | 今回は新規モデルを増やさず、価値が高い候補を派生タスクへ分離 | COV-010AでCommand registration、COV-010BでDomain resolver / object context、COV-003Bでdiagnostic edge casesを扱う | 内部helper名をそのままモデル化しない |
| COV-010A | Command registration coverage | 部分反映済み | medium | main.tsのコマンド登録とdogfood/docsのコマンド説明は整合しているか | main.ts addCommand, docs/commands.md, modeling-preview-view.ts, png-export.ts | 09_screen, 04_process, 99_notes | srcではRebuild index、Open preview、Open preview in main pane、Open preview in new pane、各template insertion、ER relation block、Complete current field、Export current diagram as PNGを確認。docs/commands.mdは主要commandを載せるがpane指定Previewは薄く、Export and Openはtoolbar actionでcommand登録ではない | command palette入口はimplemented。ただし専用SCREEN / PROCESSは増やさず、既存Preview / PNG / template / completion処理へ接続する表現で十分 | COV-010A1でcommand reference専用noteが必要か判断する | command registration自体の内部処理はモデル化しない。Viewer toolbar actionとcommand palette入口を混同しない |
| COV-010A1 | Command reference dogfood note | 要追加調査 | low | command palette入口をdogfood内に専用noteとして持つ必要があるか | main.ts addCommand, docs/commands.md | 99_notes, optional screen/process refs | COV-010Aではsource-backed command一覧を確認したが、既存processへの参照で概ね足りる | deferred | docs/commands.mdのstale settings説明と合わせて、必要なら小さなcommand reference noteを作る | docs修正ではなくdogfood運用上の導線として判断する |
| COV-010B | Domain resolver and object context internals | 部分反映済み | medium | Domain Sources、domain diagnostics、object context / subgraph / internal edge helpersをdogfoodでどこまで表現すべきか | domain-diagram-resolver.ts, app-process-domain-resolver.ts, domain-diagnostics.ts, object-context-resolver.ts, object-subgraph-builder.ts, internal-edge-adapters.ts | 00_domains, 01_dfd, 03_data, 04_process, 99_notes | Domain Sources解決、source summary、conflict、warningは表示 / 診断用派生結果。object contextはClass / ER object preview用にoutgoing / incoming関係を集約し、subgraphは派生ResolvedDiagramとしてRendererへ渡される。internal edge adaptersはrelations / diagram edge / ER relation blockを表示用edgeへ寄せる補助 | user-visibleな描画境界だけ既存モデルへ最小反映し、内部helper全量DATA化はしない | COV-010B1でDomain diagnostics codeとUI guidanceを必要時に確認する | Markdown正本のDomain Sourcesと派生resolved domain情報を混同しない |
| COV-010B1 | Domain diagnostics guidance | 反映済み | low | Domain Sources / local Domainsのdiagnosticsを診断guidanceへ追加すべきか | domain-diagnostics.ts, domain-diagram-resolver.ts, app-process-domain-resolver.ts, modeling-preview-view.ts | RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION, MAP-MW-RENDERER-DIAGNOSTIC-TO-UI, SCR-MW-VIEWER-DIAGNOSTICS-PANEL | Domain Sources.ref、Domain Sources、Steps.domain、local Domain override、Domain conflictは既存診断カードのmetadata / details / copy actionsに乗る | Domain専用UIやQuick Fixは作らず、既存guidance ruleへ代表ケースだけ反映 | message別の全量棚卸しが必要になればCOV-003Bで扱う | Domain resolver内部helperの全量モデル化はしない |
| COV-010B2 | DFD Domain Sources object-domain diagnostics | 要追加調査 | medium | DFD Objects.domain が Domain Sources 経由で解決できる場合にローカル Domains 不在警告を出さないようにできるか | validator.ts, relation-resolver.ts, domain-diagnostics.ts | model-weave source issue候補, 99_notes | Preview解決側はDomain Sourcesを統合するが、Vault検証側のDFD object domain診断はローカルDomains不在を警告する経路が残る | dogfood DFDへ重複ローカルDomainsを追加して回避しない。本体診断仕様の改善候補として残す | 本体側で検証診断がDomain Sourcesの解決済みDomainsを参照するか検討する | DFD-MW-CORE-L0のDomain Sources共通利用設計は維持する |
| COV-010C | Relation resolver diagnostics edge cases | 要追加調査 | low | class relation incompatible targetやauto-collected relation diagnosticsをdogfood diagnostics rulesに追加すべきか | relation-resolver.ts, current-file-diagnostics.ts | diagnostics rule / mapping | COV-003 / COV-003Aで一部対応済みだがedge caseは薄い | COV-003Bへ統合 | code / message生成元棚卸しで扱う | Quick Fixとは混同しない |
| COV-011 | overclaim確認 | 部分反映済み | high | 既存dogfoodモデルが実装済みと過大表現している箇所はないか | model-weave/src, dogfood model | all model folders | 横断検索でExplorer系、Quick Fix、自動修正、weave_map、severity note、Markdown正本と派生表示、保存設定とViewer一時状態、Flow Connect Mode対象範囲を点検。大きなoverclaimは見つからず、STATUS / READMEの分類表現だけ補正 | implemented定義をsource-backedへ締め、Explorer / Model Indexはfuture維持、NotebookLMはnote_only候補維持 | COV-009 / COV-010でdocsのみ仕様とsrcのみ仕様を機能別に洗う | generated modelを正と見なさない。COV-011は定期点検として必要に応じて再実施 |
| COV-012 | 日本語 / FORMAT逸脱 | 部分反映済み | medium | 既存dogfoodに不自然な日本語やFORMAT逸脱は残っていないか | FORMAT docs, dogfood model scan | 04_process, 99_notes | 横断検索で不自然な和英混在動詞、TypeScript風型表記、Wikilink alias、frontmatter未クォートWikilink、app_process Steps / Flows headerを確認。実モデルで明確な修正対象はSource Links processのopen表現のみ。型表記やaliasの検出は安全ガイド内の悪い例が中心 | 明らかな表現問題だけ最小修正し、ガイド内のRisky例はfalse positiveとして維持 | COV-012Aでtable列数の機械検証を必要に応じて別途行う | raw pipe、TypeScript風型、Wikilink aliasは例示と実モデルを分けて判断する |
| COV-012A | Markdown table mechanical validation | 反映済み | low | 全Markdown tableの列数を機械的に検証する必要があるか | dogfood model scan | all model folders | コードブロック外のMarkdown tableを検証し、ヘッダー列数と各行列数の不一致は0件。app_process Inputs / Outputs / Steps / Flowsヘッダー逸脱も0件 | 明らかな列崩れ修正は不要。false positiveの悪い例は維持 | 今後table warningが出た場合だけFORMAT別に再検証する | ガイド内の悪い例を誤修正しない |
| COV-013 | v0.1.19 release alignment | 反映済み | high | v0.1.19のFlow Diagram MVP、diagnostics guidance、Color Scheme編集をdogfoodへ反映できているか | v0.1.19 release notes, FORMAT-flow_diagram, dfd-diagram-parser.ts, relation-resolver.ts, dfd-mermaid.ts, diagnostic-section-guidance.ts, color-scheme-table-editor.ts, applied-color-scheme-renderer.ts | 03_data, 04_process, 05_rule, 06_mapping, 09_screen, 99_notes | Flow Diagram MVPはtype: flow_diagram、kind: screen_communication、Objects / Flows表、ローカルendpoint、Flows.data Wikilink診断、Internal Detail Viewとして実装済み。Color Scheme preview editingとApplied Color Scheme compact tableも実装済み | Flow Diagram DATA / parse / render processを追加し、既存Parser / Resolver / Renderer / Diagnostics / Viewerへ最小反映 | COV-013Aで代表Flow Diagramサンプルやdogfood実例が必要か判断する | Surface View、Communication View、folding、projection、transition coverage生成はfuture維持 |
| COV-013A | Flow Diagram sample and model coverage | 要追加調査 | medium | dogfood内にFlow Diagramの代表モデルやサンプルを追加する必要があるか | FORMAT-flow_diagram, parser / renderer implementation | optional model files | COV-013では形式データと処理境界のみ反映 | deferred | 代表的なscreen communication例を追加するか別batchで判断する | MVP外のprojectionや自動生成は追加しない |
| COV-013B | Source Links header diagnostics | 反映済み | high | schema-driven expected header diagnosticsで表面化した旧Source Linksヘッダーを正規化できているか | current-file-diagnostics.ts, diagnostic-section-guidance.ts, dogfood model scan | model-weave-dogfood/model | 旧 `path / symbol / kind / notes` 形式が多数残っていた。現行FORMATは `path / notes` のみ | Dogfood側を正規化し、symbol / kindはnotesへ畳み込む。本体parser / diagnosticsは緩めない | 今後Source Links invalid-table-columnが出たファイルだけ再検索する | Source Links Explorerとは無関係。section supportの現行FORMATに合わせる |
| COV-014 | dogfood baseline and v0.1.18 catch-up | 反映済み | high | dogfoodがどの本体releaseまで反映済みか明示し、v0.1.18要素の取りこぼしを追跡できているか | v0.1.18 release notes, modeling-preview-view.ts, markdown-table.ts | STATUS-MW-DOGFOOD-BASELINE, STATUS, TASKS | v0.1.19 alignment中にv0.1.18のlower panel tabs、diagnostics bulk copy、manual guidance、Quick Fix MVP、empty table row handlingの追跡が独立していないことを確認 | baseline noteを追加し、v0.1.18 catch-upをCOV-014A〜Eへ分離する | COV-014Aから小さく反映する | implementedはsrc確認済みの範囲だけ。docsやrelease notesだけではimplemented化しない |
| COV-014A | v0.1.18 lower panel tabs | 要追加調査 | medium | lower panel tabsのstable behaviorをscreen / data / mappingへどこまで反映するか | modeling-preview-view.ts | SCR-MW-VIEWER-MAIN-FRAME, DATA-MW-VIEWER-STATE, mapping | `.model-weave-lower-tabs` とactive tab維持はsource-backedに確認できる | pending | graph / non-graph review viewとrenderer switching境界を最小反映する | COV-002Aのmode別lower pane整理と重複させない |
| COV-014B | v0.1.18 diagnostics copy actions | 要追加調査 | medium | all diagnostics / errors / warnings / notesのbulk Markdown copyをDiagnostics screen / mappingへ反映するか | modeling-preview-view.ts, i18n messages | SCR-MW-VIEWER-DIAGNOSTICS-PANEL, MAP-MW-RENDERER-DIAGNOSTIC-TO-UI | `diagnostics.copyAll`、`copyErrors`、`copyWarnings`、`copyNotes` はsource-backedに確認できる | pending | 既存copy guidanceとbulk actionを混同しない形で補強する | AI/chatレビュー向けのコピー支援として扱う |
| COV-014C | v0.1.18 manual-edit guidance | 要追加調査 | medium | diagnostic detailsのmanual repair guidanceをRULE / SCREENへどう整理するか | modeling-preview-view.ts, diagnostic-section-guidance.ts, i18n messages | RULE-MW-DIAGNOSTIC-GUIDANCE-DERIVATION, SCR-MW-VIEWER-DIAGNOSTICS-PANEL | reference、expected header、frontmatter exampleなどのdetailsはsource-backed | pending | Quick Fixではなく手動修復guidanceとして追記する | 自動修正とcopy guidanceを分ける |
| COV-014D | v0.1.18 Quick Fix MVP | 反映済み | high | missing frontmatter id / name限定のQuick Fix MVPをdogfoodへどう反映するか | modeling-preview-view.ts | DATA-MW-DIAGNOSTIC-QUICK-FIX, PROC-MW-DIAGNOSTIC-QUICK-FIX-APPLY, diagnostics screen, diagnostics rule, mapping, STATUS | Quick Fix actionはmissingFieldがtypeの場合は出ず、id / nameはbasenameから安全に値を作ってfrontmatterへ追加する | missing id / name限定MVPとしてDATA / PROCESS / SCREEN / RULE / MAPPINGへ反映 | COV-014BまたはCOV-014Cへ進む | missing type、invalid table header、任意Markdown本文の自動修正ではない |
| COV-014E | v0.1.18 Markdown table rows | 要追加調査 | medium | fully empty data row ignoreとpartially filled row validationをDATA / RULE / parser notesへ反映するか | markdown-table.ts, FORMAT-common-sections.md | DATA-MW-CORE-MARKDOWN-TABLE, parser rule notes | `isEmptyMarkdownTableDataRow` により全セル空のdata rowだけ無視する | pending | Markdown table DATAまたはparser guidanceへ最小反映する | 部分入力行はrequired-field diagnostics対象のまま |

## 分類メモ

| classification | meaning | handling |
|---|---|---|
| 調査済み | source / docs / dogfood の根拠を一度確認した | 判断根拠と残リスクをnotesに残す |
| 反映済み | dogfood modelへ小さく反映した | 反映先とsource evidenceを記録する |
| 要追加調査 | 根拠不足または範囲が広い | すぐimplemented扱いにせず、次batch候補にする |
| future保持 | dogfood先行設計または未実装 | 実装済み機能との違いをSummary / Notesに明記する |
| docs_only | docsやdesign noteに記述があるがsrc実装としては未確認 | implementedにせず、STATUSやTASKSで根拠と残リスクを残す |

## 今回までに得られたナレッジ

- Source-driven dogfood更新では、DATA、DFD、Processの順で整えるとBusiness Flow化の精度が上がる。
- Format docsとparser実装はずれることがあるため、docsだけを根拠に表ヘッダーを決めない。
- COV-009では、docs/commands.mdの単一Default render modeとAuto説明、weave-map-mvp.mdのList / Map切替やnode clicksなどをdocs_only / future候補として確認した。dogfood側は現行srcに合わせた形式別render modeとWeave Map派生表示の線引きを維持する。
- app_process Business Flowの `Flows.from` / `Flows.to` はローカル `Steps.id` を参照する。外部processやscreenへの出口は `Transitions`、子process参照は `Steps.invoke` で扱う。
- Flow Connect Modeとref-aware hover / click targetは app_process / Business Flow 限定である。DFD、Class、ER、Mermaid全般の汎用編集機能として扱わない。
- Source Links section supportは実装済みである。Source Links Explorerはfutureとして分ける。
- Reference Explorer / Model Index Viewも0.1.17時点では独立UIとしてsrc実装を確認できないためfutureとして分ける。内部Vault Index、Relationship View、Impact Summary、Weave Mapとは混同しない。
- Weave MapはRelationship View内の派生視覚化であり、専用Markdown形式ではない。
- Impact Summaryは保存データではなく、Parsed modelとVault Indexから生成される派生結果である。
- Color Scheme適用は描画結果の見た目であり、Markdown正本を書き換えない。
- NotebookLM用バンドルは本体機能ではなく、dogfood運用上の派生成果物として扱う。
- COV-011の横断点検では、implementedは「実装に近い」ではなく、srcで確認できるものだけに限定する方針へ締め直した。
- COV-010では、srcにあるhelperやcommand registrationをすべてdogfoodモデル化するのではなく、利用者理解、保守判断、変更影響の把握に効く境界だけを候補化する方針を確認した。Command registration、Domain resolver / object context、Relation resolver diagnostics edge casesは派生タスクへ分離する。
- COV-010Aでは、Obsidian command palette入口として、index rebuild、preview open、pane指定preview open、template insertion、ER relation block、field completion、PNG exportを確認した。Export and Open PNGはcommand登録ではなくViewer toolbar actionであるため、command coverageとは分ける。
- COV-010Bでは、Domain SourcesはMarkdown正本の参照情報、sourceSummaries / conflicts / warnings は表示と診断用の派生結果、object context / subgraph / internal edge adapters はClass / ER object preview用の派生描画補助として分けて扱う方針を確認した。
- COV-010B1では、Domain Sources / Domain Diagram / app_process `Steps.domain` の診断は、専用UIではなく既存の診断カードとcopy guidanceへ載ることを確認した。Domain定義やMarkdown本文を自動修正するQuick Fixとは扱わない。
- COV-010B2では、DFD `Objects.domain` が `## Domain Sources` 経由で解決できる場合は、dogfood側でローカル `## Domains` を重複追加して警告を消すのではなく、本体側のDFD domain診断仕様候補として扱う。
- Viewerのlower pane構成はmodeごとに異なる。object / dfd-object / domains / domain-diagramはsplit shellのbottomPane、diagramはlower slots、color_scheme / relationsは標準Review Panelなしとして扱う。
- Diagnosticsは `ValidationWarning` を基礎にした派生結果であり、severityの有効値は info / warning / error である。Viewerでは info をNotesとして表示する。
- Diagnostics PanelのOpen Locationとcopy guidanceは実装済みである。v0.1.18ではbulk Markdown copyとmissing frontmatter id / name限定Quick Fix MVPもsource-backedに確認できるが、Markdown本文全般の自動修正ではない。
- Diagnosticsのcopy guidanceは、diagnostic code単独ではなくmessageとcontextのキーから導出される。reference、expected header、frontmatter exampleはこの汎用導出に乗る。
- v0.1.18 Quick Fix MVPは、missing frontmatter id / nameに限定された手動実行の修正支援である。missing typeは自動修正しない。既存frontmatter値は上書きしない。id / nameは安全な場合のみファイルbasenameから導出する。
- v0.1.18 Markdown table handlingでは、全セルが空のdata rowだけを編集ノイズとして無視する。部分入力行は引き続きrequired-field diagnostics対象にする。
- PNG ExportはViewer actionとして実装済みであり、描画済み図のexport surfaceからPNGを派生出力する。toolbar、Review Panel、Diagnostics、lower paneは出力対象ではない。
- Applied Color Scheme sectionはViewer下部の確認表示であり、resolvedColorSchemeとview targetから表示行を作る。PNGにはsectionではなく配色反映済み図本体だけが含まれる。
- app_processはproseとtable-based Business Flowを併用できる。source-backedな順序、分岐、入出力、外部process連携が明確なものだけ表形式へ変換し、future / planned / logical寄りのprocessはproseで残す。
- COV-012では、ガイドやレポートに含まれる `Array<T>`、`Foo[]`、Wikilink alias は「避けるべき例」として残し、実モデルの表セルに残る危険表記とは分けて扱う方針を確認した。
- COV-012Aでは、コードブロック外のMarkdown table列数とapp_process主要ヘッダーを機械的に確認し、列数崩れは検出されなかった。今後は実際のViewer warningが出たFORMATだけ小さく再検証する。
- COV-013では、v0.1.19のFlow Diagram MVPを `dfd_diagram` とは別形式として整理した。`type: flow_diagram` は `schema: flow_diagram` として処理され、MVPはInternal Detail Viewのみを描画する。
- Flow Diagramの `Flows.from` / `Flows.to` はローカル `Objects.id` を参照する。外部モデル参照やWikilinkではない。
- Flow DiagramとDFDの `Flows.data` は、plain textならedge labelとして扱い、Wikilinkなど参照形式だけ未解決診断の対象になる。
- v0.1.19のColor Scheme preview editingは `## Colors` の fill / stroke / text セル更新に限定された実装済み操作であり、Diagnostics Quick Fixや汎用Markdown自動修正ではない。
- Applied Color Scheme sectionはcompact tableとして `Target / Kind / Preview / Notes / Source` を表示する。表示section自体はPNG export対象ではない。
- COV-013Bでは、dogfood内の旧Source Links表 `path / symbol / kind / notes` を現行FORMATの `path / notes` へ正規化した。symbol / kindは必要な場合だけnotesへ畳み込み、本体parserやdiagnosticsを旧形式許容へ戻さない。

## Redmine dogfood方式から持ち込む考え方

- AIに一気に仕様書を完成させようとしない。
- 根拠、不明点、判断、次アクションを分ける。
- source-backedなものだけをimplementedとして扱う。
- 推測や将来構想はplanned / future / note_onlyに逃がす。
- 生成済みdogfoodモデルを正と見なさず、src / docsとの差分を継続確認する。
- 仕様書不備のある業務システムでは、修正履歴や設計書がきれいに揃っているとは限らない前提で進める。
- 調査範囲を人間側で狭く固定しすぎず、AIによる差分検出を活用する。
- 変更は段階的に行い、無根拠な一括変換は避ける。

## 次回以降の進め方

1. TASKSの高優先度から1つだけ選ぶ。
2. source / docs / dogfood を読み、差分と不明点を分ける。
3. implementedと断定できるものだけ小さなdogfood edit batchにする。
4. 判断に迷うものはTASKSへ戻し、future / planned / partialを維持する。
5. 変更後はSTATUSとREADME / GUIDEの線引きが古くならないか確認する。

## Source Links

| path | notes |
|---|---|
| model-weave/docs/releases/v0.1.16.md | Viewer / Review Panel / Source Links / Weave Map / Diagnostics の0.1.16整理 |
| model-weave/docs/releases/v0.1.17.md | Business Flow direction、Step kind、Flow Connect Mode、ref-aware interaction の0.1.17整理 |
| model-weave/docs/releases/v0.1.18.md | Lower panel tabs、diagnostics bulk copy、Quick Fix MVP、empty table row handling の0.1.18整理 |
| model-weave/src/parsers/markdown-table.ts | empty table data row handling の実装参照 |
| model-weave/docs/design/weave-map-mvp.md | Weave MapをRelationship Viewの派生表示として扱う設計根拠 |
| model-weave/src/views/modeling-preview-view.ts | Viewer、Relationship View、Weave Map、Applied Color Scheme、Flow Connect Modeの実装参照 |
| model-weave/src/core/impact-analyzer.ts | Impact Summaryとrelated Source Linksの実装参照 |
