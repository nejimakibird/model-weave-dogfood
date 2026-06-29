---
type: data_object
id: DATA-MW-IMPACT-SUMMARY
name: Impact Summary
kind: result
data_format: object
tags:
  - ModelWeave
  - Impact
  - Relationship
  - Data
---

# Impact Summary

## Summary

現在表示中または解析対象のモデルに対して、参照関係、未解決参照、CodeSet値利用、関連 Source Links を集約した実行時の結果データ。
buildImpactSummary が Vaultモデルインデックスを参照して生成し、Relationship View、Weave Map、Copy Impact Summary の入力として使われる。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| modelPath | 対象モデルpath | string | | Y | modelPath | [[DATA-MW-CORE-PARSED-MODEL]] | ImpactSummary.modelPath |
| modelId | 対象モデルID | string | | N | modelId | | ImpactSummary.modelId |
| modelType | 対象モデル種別 | string | | Y | modelType | [[CODE-MW-CORE-MODEL-TYPE]] | ParsedFileModel.fileType |
| modelLabel | 対象モデル表示名 | string | | Y | modelLabel | | ImpactSummary.modelLabel |
| outboundRelationships | 参照先関係 | ImpactRelationship list | | Y | outboundRelationships | | 解決済みoutbound参照のグループ |
| inboundRelationships | 被参照関係 | ImpactRelationship list | | Y | inboundRelationships | | 対象モデルを参照するモデルのグループ |
| valueUsages | 値利用 | ImpactValueUsage list | | Y | valueUsages | | codesetの場合にmember利用を集約 |
| unresolvedOutbound | 未解決参照 | ImpactReference list | | Y | unresolvedOutbound | | 解決できないoutbound参照 |
| relatedSourceLinks | 関連Source Links | ImpactSourceLink list | | Y | relatedSourceLinks | [[DATA-MW-SOURCE-LINK]] | self / inbound / outbound由来のSource Links |
| formattedMarkdown | Markdown出力 | string | | N | output | | formatImpactSummaryAsMarkdown の派生出力 |
| weaveMapInput | Weave Map入力 | ImpactSummary | | N | buildWeaveMapModel.summary | | Relationship View内のWeave Map派生表示に使われる入力 |

## Notes

- ImpactSummary は保存用のExplorer stateではなく、現在の ParsedFileModel と ModelingVaultIndex から生成される派生結果である。
- ImpactSummary はMarkdown正本ではなく、Preview更新時のRelationship View表示用に作られる実行時データである。
- outboundRelationships は、対象モデルから参照される解決済みモデルを groupOutboundRelationships でまとめる。
- inboundRelationships は、Vault内の他モデルから対象モデルへの参照を groupInboundRelationships でまとめる。
- unresolvedOutbound は、外部モデル参照として扱われるが解決できなかった参照を保持する。
- valueUsages は対象が codeset の場合だけ、qualified member参照をmember単位に集約する。
- relatedSourceLinks は collectRelatedSourceLinks が self / inbound / outbound の Source Links を集約し、重複をまとめた結果である。
- ImpactSourceLink の notes は複数noteを持つため、Markdownテーブル内では list として表す。
- Weave Mapは ImpactSummary から buildWeaveMapModel で生成されるRelationship View内の派生視覚化であり、専用Markdown形式ではない。
- formatImpactSummaryAsMarkdown は Used by、References、Value usage、Unresolved、Source links をMarkdown文字列に整形する。
- Source Links Explorer の選択状態やフィルタ状態は本データオブジェクトの範囲外である。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/types/models.ts | ImpactSummary | interface | impact summaryの本体 |
| src/types/models.ts | ImpactReference | interface | 参照1件の詳細 |
| src/types/models.ts | ImpactRelationship | interface | 参照関係のグループ |
| src/types/models.ts | ImpactValueUsage | interface | codeset member利用 |
| src/types/models.ts | ImpactSourceLink | interface | 関連Source Links |
| src/core/impact-analyzer.ts | buildImpactSummary | function | summary生成 |
| src/core/impact-analyzer.ts | collectRelatedSourceLinks | function | 関連Source Links集約 |
| src/core/impact-analyzer.ts | formatImpactSummaryAsMarkdown | function | Markdown整形 |
| src/core/weave-map.ts | buildWeaveMapModel | function | ImpactSummaryからWeave Map派生モデルを生成 |
