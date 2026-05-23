---
type: class
id: CLS-MW-RENDERER-GRAPH-MODEL-BUILDER
name: GraphModelBuilder
kind: class
package: model_weave.renderer
tags:
  - Renderer
---

# GraphModelBuilder

## Summary

解析済みの論理データ（ParsedModel）とシステム全体のインデックス（ModelingVaultIndex）を入力とし、ノード間の参照を解決した抽象グラフモデル（ResolvedDiagram / GraphModel）を構築します。

## Attributes

| name | type | visibility | static | notes |
|---|---|---|---|---|

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| resolveDiagramRelations | diagram: DiagramModel, index: ModelingVaultIndex | ResolvedDiagram | public | Y | ダイアグラム種別に応じたリレーション解決 |
| resolveDfdDiagramRelations | diagram: DfdDiagramModel, index: ModelingVaultIndex | ResolvedDiagram | private | Y | DFD専用のフロー解決 |
| resolveErDiagramRelations | diagram: DiagramModel, index: ModelingVaultIndex | ResolvedDiagram | private | Y | ER専用のエンティティ解決 |
| resolveDfdDiagramObjects | diagram: DfdDiagramModel, index: ModelingVaultIndex | any | private | Y | ノード定義の解決 |

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/relation-resolver.ts | resolveDiagramRelations | function | 共通リレーション解決口 |
| src/core/relation-resolver.ts | resolveDfdDiagramRelations | function | DFDフロー解決の実装 |