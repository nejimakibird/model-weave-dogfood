---

id: REPORT-MW-SOURCE-DRIVEN-DOGFOOD-FINDINGS
name: Source-driven Dogfood Reverse Engineering Findings
tags:
- Dogfood
- ReverseEngineering
- AI
- Quality
- Findings

---

# Source-driven Dogfood Reverse Engineering Findings

## Summary

この文書は、Model Weave の現在のソースコードを起点として、dogfood model を更新・補強したリバースエンジニアリング実験の結果をまとめる。

目的は、既存ソースからどの程度まで Model Weave のモデルを復元・更新できるか、またその結果が設計理解・可視化・品質改善にどの程度役立つかを確認することである。

今回の実験では、AI エージェントにソースコード、既存 dogfood model、format docs を参照させ、DATA / DFD / Process を段階的に更新した。

結果として、ソース起点の dogfood 更新は有効であり、特に以下の価値が確認できた。

* 実装済み機能の構造を、Markdown model と図で可視化できる
* 古くなった dogfood model を、実装に合わせて更新できる
* format docs と parser 実装の不整合を検出できる
* process を Business Flow 化することで、処理分岐を把握しやすくなる
* DATA / DFD を先に整えることで、Process 変換の精度が上がる

## Experiment Goal

この実験の主目的は、単なる dogfood 更新ではない。

Model Weave 自身を対象にして、以下を検証することにある。

1. ソースコードからモデル構造を抽出できるか
2. 実装済み機能と future / planned 機能を区別できるか
3. DATA / DFD / Process のどの形式がリバースエンジニアリングに向いているか
4. AI 生成したモデルが Model Weave 上で実用的に読める図になるか
5. dogfood 更新が、ドキュメントや仕様の品質改善にもつながるか

## Recommended Reverse Engineering Order

今回の実験から、ソース起点の dogfood 生成は次の順序が有効であると分かった。

```text
1. DATA を整える
2. DFD で境界を整理する
3. Process を Business Flow 化する
4. Model Weave Preview で警告・図・不整合を確認する
5. docs / dogfood / 必要に応じて実装の不整合を修正する
```

いきなり Process から始めることも可能だが、Inputs / Outputs / Transitions が抽象的になりやすい。

先に DATA と DFD を整えることで、Process 変換時に参照できるデータ構造と境界が明確になり、Business Flow の品質が上がる。

## DATA Findings

DATA は、ソース起点のリバースエンジニアリングと相性が良い。

理由は、TypeScript の interface / type / runtime structure から比較的安定して抽出できるためである。

今回更新・追加した主な DATA model は以下である。

| model                           | 目的                                                |
| ------------------------------- | ------------------------------------------------- |
| DATA-MW-VAULT-MODEL-INDEX       | ModelingVaultIndex の実装構造に追随                       |
| DATA-MW-CORE-PARSED-MODEL       | BaseFileModel / ParsedFileModel / ParseResult に追随 |
| DATA-MW-CORE-DIAGNOSTIC         | ValidationWarning / current-file diagnostics に追随  |
| DATA-MW-SOURCE-LINK             | 実装済み Source Links の parsed data を表現               |
| DATA-MW-IMPACT-SUMMARY          | ImpactSummary / related Source Links を表現          |
| DATA-MW-RENDERER-GRAPH-MODEL    | ResolvedDiagram の dogfood 表現として再定義                |
| DATA-MW-RENDERER-GRAPH-NODE     | DiagramNode に追随                                   |
| DATA-MW-RENDERER-GRAPH-EDGE     | DiagramEdge に追随                                   |
| DATA-MW-DFD-PARSED-DIAGRAM      | DfdDiagramModel に追随                               |
| DATA-MW-RENDERER-MERMAID-SOURCE | mermaid / mermaid-detail の現在仕様に追随                 |

DATA 更新で重要だったのは、便利な概念を追加しすぎないことである。

たとえば、`ResolvedDiagram` に存在しない `labels` / `shape` / `sourceRefs` のような概念は、主フィールドから外し、必要なら Notes に移す方がよい。

## DFD Findings

DFD は、ソース上の module / responsibility boundary を整理するのに有効だった。

特に Level 0 DFD を更新することで、Model Weave の中核構造を次のように表現できるようになった。

| boundary        | 役割                                                         |
| --------------- | ---------------------------------------------------------- |
| Parser          | Markdown model を parsed model に変換                          |
| Vault Index     | parsed model / relation / warnings を保持                     |
| Resolver        | diagram reference を解決し ResolvedDiagram を生成                 |
| Renderer        | resolved data を preview / Mermaid / Business Flow 表示へ変換    |
| Source Links    | Source Links section の解析・表示・path resolution                |
| Impact Analyzer | relationship / impact summary を生成                          |
| Preview View    | 表示状態を受け取り preview を描画                                      |
| Settings        | viewer preferences / render defaults / localSourceRoot を提供 |

DFD により、単なる `Parser → Renderer` ではなく、実装上の重要な境界が見えるようになった。

特に `Vault Index` と `Resolver` を分離したことで、後続の Process model が書きやすくなった。

## Process / Business Flow Findings

Process の Business Flow 化は、今回の実験で最も視覚的な効果が大きかった。

特に以下の process は、ソース起点で生成しても十分に読みやすい図になった。

| process                                  | 可視化できた内容                                                                |
| ---------------------------------------- | ----------------------------------------------------------------------- |
| PROC-MW-SOURCE-LINKS-SECTION-SUPPORT     | Source Links の parse / resolve / render / copy / open / impact 連携       |
| PROC-MW-PARSER-PARSE-VAULT-FILE          | shallow / full parse、schema detection、parser dispatch、markdown fallback |
| PROC-MW-SETTINGS-SAVE                    | settings normalize / save / preview refresh                             |
| PROC-MW-VAULT-INDEX-REBUILD              | index rebuild、lookup build、validation                                   |
| PROC-MW-RENDERER-GENERATE-MERMAID-SOURCE | Mermaid source generation                                               |
| PROC-MW-RENDERER-BUILD-GRAPH-MODEL       | Class / ER / DFD の resolver 分岐                                          |
| PROC-MW-IMPACT-SUMMARY-BUILD             | inbound / outbound / value usage / Source Links summary                 |
| PROC-MW-RESOLVER-RESOLVE-DIAGRAM         | ResolvedDiagram 生成                                                      |
| PROC-MW-PREVIEW-RENDER-MODEL             | preview render pipeline                                                 |
| PROC-MW-DIAGNOSTICS-COLLECT-FOR-PREVIEW  | parser / validator / resolver / preview diagnostics の集約                 |

Business Flow は処理が大きくなると面積も大きくなるが、AI による初期可視化としては十分実用的だった。

人間が最終版の図を手作業で描く前に、処理構造を把握する第一段階として価値が高い。

## Documentation Quality Findings

今回の実験では、dogfood 更新がドキュメント品質改善にもつながった。

特に DFD diagram の `## Flows` schema で、format docs と parser 実装の不整合を発見した。

| target                | schema                                          |
| --------------------- | ----------------------------------------------- |
| old docs              | `from / to / label / data / notes`              |
| parser implementation | `id / from / to / data / notes`                 |
| DfdFlowModel          | `id`, `from`, `to`, `data`, `notes`, `rowIndex` |

実際の parser は `id / from / to / data / notes` を期待していたため、format docs 側を修正した。

この結果、dogfood は単なる利用例ではなく、以下の役割を持つことが確認できた。

* 実装とドキュメントの差分検出
* format docs の検証
* parser / renderer の挙動確認
* AI 生成モデルの妥当性確認
* 仕様の古さの発見

## Table Safety Findings

AI 生成モデルでは、Markdown table cell の安全性が重要である。

今回、`SourceLink[]` や `ImpactSourceLink[]` のような TypeScript 風 array notation が、見た目上は自然でも Model Weave の診断で table column error を誘発するケースがあった。

そのため、dogfood 生成時には次のルールを強制する。

| risky                | safe                        |
| -------------------- | --------------------------- |
| `SourceLink[]`       | `SourceLink list`           |
| `ImpactSourceLink[]` | `ImpactSourceLink list`     |
| `Array<string>`      | `string list`               |
| `string \| null`     | `string` + required / notes |
| `[[target\|label]]`  | `[[target]]`                |

Markdown table cells should be short, parser-safe, and avoid TypeScript-specific syntax.

詳細な説明や条件分岐は table cell に詰め込まず、`## Notes` や別の Rule / Process model に分離する。

## AI Agent Instruction Findings

AI エージェントに dogfood を更新させる場合、次の指示が有効だった。

```text
Do not assume table schemas from memory.
Read the current format document and parser implementation before editing.
If docs and source disagree, report the inconsistency before making broad changes.
```

また、次のルールも毎回入れるべきである。

```text
Keep dogfood prose in Japanese.
Keep source symbols, file paths, model IDs, and code identifiers in English.
Keep Markdown table cells parser-safe.
Do not use TypeScript-style array notation such as Foo[] or Array<Foo> in table cells.
Use Foo list or list of Foo instead.
Do not use Wikilink aliases inside tables.
Do not model future/planned behavior as implemented.
```

これにより、AI が古い docs に従って不正な model を生成したり、future/planned の構想を実装済みとして扱うリスクを下げられる。

## Implemented vs Future Separation

今回の実験では、実装済みと future / planned を分離することが重要だった。

たとえば Source Links では、以下のように整理した。

| area                                   | status           |
| -------------------------------------- | ---------------- |
| `## Source Links` parsing              | implemented      |
| preview Source Links rendering         | implemented      |
| Copy Path / Open resolved path         | implemented      |
| related Source Links in impact summary | implemented      |
| Source Links Explorer                  | future / planned |
| Explorer filtering / selection         | future / planned |

この分離により、実装済み process と future concept が混ざらず、dogfood の信頼性が上がった。

## Key Lessons

今回の主な学びは以下である。

1. Source-driven dogfood generation is practical.
2. DATA and DFD should be refreshed before large Process conversion.
3. Business Flow is useful as a first-pass visualization of source behavior.
4. Dogfood can reveal docs / parser inconsistencies.
5. AI-generated dogfood needs strict table-safety rules.
6. Implemented behavior and future/planned models must be separated.
7. Large process diagrams are acceptable as generated understanding maps, but may later need subflow splitting.
8. Dogfood can work as both documentation and quality feedback loop.

## Recommended Workflow

今後の Model Weave dogfood 更新では、次の流れを推奨する。

```text
1. Source scan
2. DATA refresh
3. DFD boundary refresh
4. Process Business Flow conversion
5. Preview validation
6. Docs/source inconsistency report
7. Minimal fixes
8. Commit by coherent batch
```

この順序で進めると、AI によるモデル更新の精度が上がり、Process 図も読みやすくなる。

## Next Candidates

次に進める場合の候補は以下である。

| candidate                                   | reason                             |
| ------------------------------------------- | ---------------------------------- |
| format-specific diagnostics child processes | current-file diagnostics の詳細化      |
| file-type-specific preview child processes  | Preview pipeline の分割               |
| class / ER / DFD resolver child processes   | Resolver process の分割               |
| render mode detail DFD                      | Settings / Renderer / Preview の詳細化 |
| Explorer future/planned cleanup             | future model の明示的整理                |

ただし、現時点では中核 DATA / DFD / Process はかなり揃っているため、まずは preview 確認と差分整理を優先する。

## Conclusion

今回の source-driven dogfood reverse engineering 実験により、Model Weave は既存ソースコードの理解・可視化・ドキュメント検証に有効であることが確認できた。

特に、DATA / DFD / Process を段階的に生成・更新することで、コードの構造と処理フローを人間が理解しやすい形に変換できる。

また、dogfood を実際に Model Weave preview に通すことで、format docs と parser 実装のズレや、AI 生成時の記述リスクも発見できた。

これは、Model Weave が単なる図生成ツールではなく、AI と人間が協調して設計資産を更新・検証するための中間表現として機能することを示している。
