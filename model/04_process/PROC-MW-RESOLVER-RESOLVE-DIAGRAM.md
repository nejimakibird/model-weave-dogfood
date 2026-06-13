---
type: app_process
id: PROC-MW-RESOLVER-RESOLVE-DIAGRAM
name: Diagram参照解決
process_type: resolver
tags:
  - ModelWeave
  - Resolver
  - Renderer
  - Process
  - BusinessFlow
---

# Diagram参照解決

## Summary

parsed diagram と Vaultモデルインデックスから、rendererが利用する `ResolvedDiagram` を生成する処理。
現行実装では `resolveDiagramRelations` が diagram kind に応じて class / ER / DFD の解決経路を切り替え、nodes、edges、missingObjects、warnings を返す。

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Model Weave architecture domains |

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| parsedDiagram | [[DATA-MW-CORE-PARSED-MODEL]] | Parser | Y | class / ER / DFD diagram model |
| vaultIndex | [[DATA-MW-VAULT-MODEL-INDEX]] | [[DFD-MW-OBJ-VAULT-INDEX]] | Y | object / entity / DFD object / relation lookupに使う |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| resolvedDiagram | [[DATA-MW-RENDERER-GRAPH-MODEL]] | [[DFD-MW-OBJ-RESOLVER]] | sourceの`ResolvedDiagram`に対応する |
| graphNodes | [[DATA-MW-RENDERER-GRAPH-NODE]] | Renderer | 解決済みdiagram nodes |
| graphEdges | [[DATA-MW-RENDERER-GRAPH-EDGE]] | Renderer | 解決済みdiagram edges |
| warnings | [[DATA-MW-CORE-DIAGNOSTIC]] | Preview diagnostics | missing / duplicate / unresolved など |

## Steps

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | relation_resolution | Diagram解決を開始する | start | parsedDiagram |  |  |  |  | `resolveDiagramRelations` を呼び出す |
| detectKind | relation_resolution | diagram kindを判定する | decision | parsedDiagram |  |  |  |  | `er` と `dfd` は専用経路へ分岐する |
| resolveClassNodes | relation_resolution | class diagram nodesを解決する | process | parsedDiagram, vaultIndex | graphNodes |  |  |  | object refsをObjectModelへ解決する |
| resolveClassEdges | relation_resolution | class diagram edgesを解決する | process | graphNodes, vaultIndex | graphEdges |  |  |  | 明示edgesまたはobject relationsを使う |
| resolveErNodes | relation_resolution | ER diagram nodesを解決する | process | parsedDiagram, vaultIndex | graphNodes |  |  |  | object refsをErEntityへ解決する |
| resolveErEdges | relation_resolution | ER diagram edgesを解決する | process | graphNodes, vaultIndex | graphEdges |  |  |  | ER outbound relationsを使う |
| resolveDfdObjects | relation_resolution | DFD objectsを解決する | process | parsedDiagram, vaultIndex | graphNodes |  |  |  | Objects rowsをDFD objectへ解決する |
| resolveDfdFlows | relation_resolution | DFD flowsを解決する | process | graphNodes, vaultIndex | graphEdges |  |  |  | flow endpointとdataを解決する |
| collectWarnings | relation_resolution | missingObjectsとwarningsを集約する | process | graphNodes, graphEdges | warnings |  |  |  | unresolved referenceやshape warningを含む |
| buildResolved | relation_resolution | ResolvedDiagramを組み立てる | process | graphNodes, graphEdges, warnings | resolvedDiagram |  |  |  | diagram / nodes / edges / missingObjects / warningsを返す |
| end | renderer_area | Diagram解決を終了する | end | resolvedDiagram |  |  |  |  | renderer入力として使われる |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | detectKind |  | kind判定 | diagram kindで分岐する |
| detectKind | resolveErNodes | `diagram.kind === "er"` | ER | ER専用経路 |
| detectKind | resolveDfdObjects | `diagram.kind === "dfd"` | DFD | DFD専用経路 |
| detectKind | resolveClassNodes |  | class | それ以外はclass/component/flow系diagramとして扱う |
| resolveClassNodes | resolveClassEdges |  | edges | class relationsを解決する |
| resolveClassEdges | collectWarnings |  | warnings | warningを集約する |
| resolveErNodes | resolveErEdges |  | edges | ER relationsを解決する |
| resolveErEdges | collectWarnings |  | warnings | warningを集約する |
| resolveDfdObjects | resolveDfdFlows |  | flows | DFD flowをedge化する |
| resolveDfdFlows | collectWarnings |  | warnings | warningを集約する |
| collectWarnings | buildResolved |  | build | ResolvedDiagramを作る |
| buildResolved | end |  | 完了 | rendererへ渡す |

## Notes

- `ResolvedDiagram` は renderer用の実行時データであり、Mermaid sourceそのものではない。
- class diagramでは明示edgesが空の場合、Objects由来のclass relationsを自動収集する経路がある。
- DFDでは flow endpointがObjectsに存在しない場合や不適切なshape同士のflowでwarningを生成する。
- この処理はExplorer機能やファイルオープン操作を扱わない。

## Source Links

| path | notes |
|---|---|
| src/core/relation-resolver.ts | Steps: start, detectKind, resolveClassNodes, resolveClassEdges, resolveErNodes, resolveErEdges. resolveDiagramRelations がdiagram kindごとに解決する |
| src/core/relation-resolver.ts | Steps: resolveDfdObjects, resolveDfdFlows, collectWarnings, buildResolved. resolveDfdDiagramRelations がDFD object / flowを解決する |
| src/core/reference-resolver.ts | Steps: resolveClassNodes, resolveErNodes, resolveDfdObjects, resolveDfdFlows. resolveReferenceIdentity がendpointや参照の同一性を解決する |
| src/types/models.ts | Steps: buildResolved, end. ResolvedDiagram / DiagramNode / DiagramEdge がresolver出力構造を定義する |
