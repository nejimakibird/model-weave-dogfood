---
type: app_process
id: PROC-MW-IMPACT-SUMMARY-BUILD
name: Impact Summary生成
process_type: analysis
tags:
  - ModelWeave
  - Impact
  - Relationship
  - Process
  - BusinessFlow
---

# Impact Summary生成

## Summary

現在の Parsed model と Vaultモデルインデックスから、参照元、参照先、未解決参照、CodeSet値利用、関連 Source Links を集約して `DATA-MW-IMPACT-SUMMARY` を生成する処理。
現行実装では `buildImpactSummary` が実行時に生成し、Relationship ViewとWeave Mapの入力になる。copy操作では `formatImpactSummaryAsMarkdown` がMarkdown文字列へ整形する。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Impact Summary生成に関わる実装領域 |

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| parsedModel | [[DATA-MW-CORE-PARSED-MODEL]] | Preview context | Y | summary対象のモデル |
| vaultIndex | [[DATA-MW-VAULT-MODEL-INDEX]] | [[DFD-MW-OBJ-VAULT-INDEX]] | Y | inbound参照と関連モデルの探索に使う |
| sourceLinks | [[DATA-MW-SOURCE-LINK]] | Parsed models | N | self / inbound / outbound 由来のSource Links |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| impactSummary | [[DATA-MW-IMPACT-SUMMARY]] | [[DFD-MW-OBJ-IMPACT-ANALYZER]] | 参照関係と関連Source Linksの集約結果 |
| weaveMapInput | [[DATA-MW-IMPACT-SUMMARY]] | Relationship View | Weave Map生成に渡される同じ派生summary |
| formattedMarkdown | string | Clipboard | copy時に生成されるMarkdown文字列 |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | impact_analysis | Impact Summary生成を開始する | start | parsedModel |  |  |  |  | `buildImpactSummary` を対象モデルで開始する |
| collectOutbound | impact_analysis | outbound参照を収集する | process | parsedModel | outbound references |  |  |  | モデル種別ごとの参照フィールドを走査する |
| resolveOutbound | relation_resolution | outbound参照を解決する | process | outbound references, vaultIndex | resolved outbound |  |  |  | `resolveReferenceIdentity` とmember解決を使う |
| splitUnresolved | impact_analysis | 未解決outboundを分離する | process | outbound references | unresolved outbound |  |  |  | 解決できない外部モデル参照を保持する |
| scanInbound | impact_analysis | inbound参照を探索する | process | vaultIndex | inbound references |  |  |  | markdownと自分自身を除くモデルを走査する |
| groupRelationships | impact_analysis | 参照関係をグループ化する | process | resolved outbound, inbound references | relationship groups |  |  |  | outbound / inbound をモデル単位でまとめる |
| codesetCheck | impact_analysis | codeset値利用か判定する | decision | parsedModel | value usages |  |  |  | codesetのみmember利用を集約する |
| collectValueUsage | impact_analysis | codeset値利用を集約する | process | inbound references | value usages |  |  |  | qualified member参照をmemberごとにまとめる |
| skipValueUsage | impact_analysis | 値利用を空にする | process | parsedModel | value usages |  |  |  | codeset以外は空のlistにする |
| collectSourceLinks | impact_analysis | 関連Source Linksを集約する | process | parsedModel, resolved outbound, inbound references | related Source Links |  |  |  | self / outbound / inbound のSource Linksを集約する |
| buildSummary | impact_analysis | ImpactSummaryを組み立てる | process | relationship groups, value usages, related Source Links | impactSummary |  |  |  | model identity と未解決参照も含める |
| provideWeaveMapInput | preview_pipeline | Weave Map入力として渡す | process | impactSummary | weaveMapInput |  |  |  | Relationship View内の派生視覚化へ渡す |
| formatMarkdown | impact_analysis | Markdown出力を整形する | process | impactSummary | formattedMarkdown |  |  |  | copy操作時のみ使用される派生出力 |
| end | preview_pipeline | Impact Summary生成を終了する | end | impactSummary |  |  |  |  | previewのRelationship sectionへ渡される |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | collectOutbound |  | 開始 | 対象モデルから参照を集める |
| collectOutbound | resolveOutbound |  | 解決 | outbound参照をindexで解決する |
| resolveOutbound | splitUnresolved |  | 未解決分離 | unresolvedOutboundを保持する |
| splitUnresolved | scanInbound |  | inbound探索 | Vault内モデルを走査する |
| scanInbound | groupRelationships |  | 集約 | inboundとoutboundを関係グループにする |
| groupRelationships | codesetCheck |  | 値利用判定 | codesetだけ追加集約する |
| codesetCheck | collectValueUsage | `model.fileType === "codeset"` | codeset | member利用を集約する |
| codesetCheck | skipValueUsage |  | codeset以外 | value usagesは空になる |
| collectValueUsage | collectSourceLinks |  | Source Links | 関連Source Linksへ進む |
| skipValueUsage | collectSourceLinks |  | Source Links | 関連Source Linksへ進む |
| collectSourceLinks | buildSummary |  | summary作成 | ImpactSummaryを返す |
| buildSummary | provideWeaveMapInput |  | weave map | Relationship View内のWeave Map入力にする |
| provideWeaveMapInput | formatMarkdown |  | copy用整形 | copy操作時にMarkdown化できる |
| provideWeaveMapInput | end |  | preview出力 | preview表示へ渡す |
| formatMarkdown | end |  | copy出力 | clipboardへ渡せる文字列 |

## Notes

- `ImpactSummary` は保存データではなく、現在の Parsed model と `ModelingVaultIndex` から生成される派生結果である。
- `buildImpactSummary` は `model.fileType === "relations"` や markdown のpreview対象外判定とは別に、受け取ったモデルに対して集約を行う。
- outboundRelationships は解決済みoutbound参照だけをモデル単位にまとめ、unresolvedOutbound は解決できない外部モデル参照を別に保持する。
- codesetの場合だけ valueUsages を集約し、それ以外のmodel typeでは空のlistを返す。
- `collectRelatedSourceLinks` は self / outbound / inbound のSource Linksを重複集約する。
- Weave MapはこのImpactSummaryから後段で生成されるRelationship View内の派生表示であり、専用Markdown形式ではない。
- Source Links Explorer の状態やフィルタはこの処理の対象外である。
- `Steps.domain` は [[DOMAINS-MW-ARCHITECTURE]] のDomain idを参照する。旧lane名はBusiness Flowの配置ラベルではなく、Domain Sources解決後のgroupとして扱う。

## Source Links

| path | notes |
|---|---|
| src/core/impact-analyzer.ts | symbol: buildImpactSummary; kind: function; ImpactSummaryを生成する |
| src/core/impact-analyzer.ts | symbol: collectRelatedSourceLinks; kind: function; 関連Source Linksを集約する |
| src/core/impact-analyzer.ts | symbol: formatImpactSummaryAsMarkdown; kind: function; copy用Markdownを生成する |
| src/core/weave-map.ts | symbol: buildWeaveMapModel; kind: function; ImpactSummaryからWeave Map派生モデルを生成する |
| src/core/reference-resolver.ts | symbol: resolveReferenceIdentity; kind: function; 参照先モデルを解決する |
| src/main.ts | symbol: buildImpactPreviewProps; kind: method; previewへsummaryとcopy/open handlerを渡す |
