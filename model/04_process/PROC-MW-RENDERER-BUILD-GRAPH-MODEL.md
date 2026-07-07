---
type: app_process
id: PROC-MW-RENDERER-BUILD-GRAPH-MODEL
name: 抽象グラフモデル構築
process_type: transform
kind: renderer
tags:
  - Renderer
---

# 抽象グラフモデル構築

## Summary

解析済みdiagramモデルとVault Indexから、描画に使う解決済みの node / edge 情報を構築します。
現行実装では resolveDiagramRelations が diagram.kind に応じて Class / ER / DFD の参照解決を行い、ResolvedDiagram を返します。
Class / ER object previewでは、object contextから関連objectのsubgraphを派生ResolvedDiagramとして構築します。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Renderer graph model構築に関わる実装領域 |

## Triggers

| id | kind | source | event | notes |
|---|---|---|---|---|
| T1 | system | Viewer | プレビュー表示要求時 | |

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| model | [[DATA-MW-CORE-PARSED-MODEL]] | Parser | Y | diagram系の解析済みモデル |
| index | Vault Index | Cache | Y | 参照解決に使う |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| graph | [[DATA-MW-RENDERER-GRAPH-MODEL]] | Renderer | 解決済みnode / edge情報 |
| diagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | Viewer | 参照解決warning |

## Transitions

| id | event | to | condition | notes |
|---|---|---|---|---|
| TR1 | 構築完了 | [[PROC-MW-RENDERER-GENERATE-MERMAID-SOURCE]] | render_mode が mermaid の場合 | |
| TR2 | 構築完了 | - | render_mode が custom の場合 | カスタム描画へ移行 |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | renderer_area | Graph構築開始 | start | model |  |  |  |  | diagramモデルを受け取る |
| chooseKind | relation_resolution | diagram kindを判定する | decision | model |  |  |  |  | er / dfd / class相当で分岐する |
| resolveClassNodes | relation_resolution | Class nodeを解決する | process | model | graph |  |  |  | objectRefs を object model に解決する |
| resolveErNodes | relation_resolution | ER nodeを解決する | process | model | graph |  |  |  | objectRefs を er_entity に解決する |
| resolveDfdNodes | relation_resolution | DFD nodeを解決する | process | model | graph |  |  |  | Objects行またはlegacy refを解決する |
| resolveClassEdges | relation_resolution | Class edgeを解決する | process | graph | graph |  |  |  | explicit relationまたは関係lookupを使う |
| resolveErEdges | relation_resolution | ER edgeを解決する | process | graph | graph |  |  |  | outboundRelations をedge化する |
| resolveDfdEdges | relation_resolution | DFD flowを解決する | process | graph | graph |  |  |  | Flows のfrom / toをnodeに解決する |
| collectWarnings | diagnostics | warningを集約する | process | graph | diagnostics |  |  |  | missing / duplicate / shape warning |
| end | renderer_area | Graph構築完了 | end | graph |  |  |  |  | Rendererへ渡す |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| chooseKind | resolveErNodes | `diagram.kind === "er"` | ER | ER diagram path |
| chooseKind | resolveDfdNodes | `diagram.kind === "dfd"` | DFD | DFD diagram path |
| chooseKind | resolveClassNodes |  | Class | default path |
| resolveClassNodes | resolveClassEdges |  | edges | Class edge解決へ進む |
| resolveErNodes | resolveErEdges |  | edges | ER edge解決へ進む |
| resolveDfdNodes | resolveDfdEdges |  | flows | DFD flow解決へ進む |
| resolveClassEdges | collectWarnings |  | warnings | warning集約へ進む |
| resolveErEdges | collectWarnings |  | warnings | warning集約へ進む |
| resolveDfdEdges | collectWarnings |  | warnings | warning集約へ進む |

## Errors

- **E-BGD-001**: unresolved-reference warning
- **E-BGD-002**: invalid-structure warning

## Notes

- 特定の描画エンジン（Mermaid等）に変換する前の中間表現を生成するのが本プロセスの責務です。
- Class path は objectRefs を解決し、explicit edges があればそれを優先する。
- Class path で explicit edges がない場合、関係lookupやobject内relationsからedgeを集約する。
- ER path は presentEntities の outboundRelations からedgeを作る。
- DFD path は Objects / Flows を解決し、endpoint欠落や不自然なflow shapeをwarningにする。
- Object preview path は resolveObjectContext で outgoing / incoming 関係を集約し、buildObjectSubgraphScene でPreview用subgraphへ変換する。
- internal edge adapters は relations file、class relation、ER relation block、diagram edgeを表示用edgeへ寄せる補助であり、Markdown正本の編集対象ではない。
- `Steps.domain` は [[DOMAINS-MW-ARCHITECTURE]] のDomain idを参照する。DFDのFlows endpointとapp_process Business FlowのFlows endpointは別フォーマットとして扱う。

## Source Links

| path | notes |
|---|---|
| src/core/relation-resolver.ts | symbol: resolveDiagramRelations; kind: function; 図面種別（ER/DFD等）に応じたノード・エッジの解決 |
| src/core/relation-resolver.ts | symbol: resolveErDiagramRelations; kind: function; ER diagramのnode / edge解決 |
| src/core/relation-resolver.ts | symbol: resolveDfdDiagramRelations; kind: function; DFD diagramのnode / flow解決 |
| src/core/reference-resolver.ts | symbol: resolveObjectModelReference; kind: function; Class参照解決 |
| src/core/reference-resolver.ts | symbol: resolveErEntityReference; kind: function; ER参照解決 |
| src/core/reference-resolver.ts | symbol: resolveDfdObjectReference; kind: function; DFD参照解決 |
| src/core/object-context-resolver.ts | symbol: resolveObjectContext; kind: function; object preview用の関連object contextを構築 |
| src/core/object-subgraph-builder.ts | symbol: buildObjectSubgraphScene; kind: function; object contextから派生ResolvedDiagramを構築 |
| src/core/internal-edge-adapters.ts | symbol: toClassRelationEdge; kind: function; relations由来の関係をClass edgeへ変換 |
