---
type: dfd_object
id: DFD-MW-OBJ-RESOLVER
name: Resolver
kind: process
tags:
  - DFD
  - Core
  - Resolver
---

# Resolver

## Summary

Vault Indexを参照して、モデル参照や図面関係を解決する境界。
resolveDiagramRelations は DiagramModel / DfdDiagramModel と ModelingVaultIndex から ResolvedDiagram を生成する。

## Details

| key | value | notes |
|---|---|---|
| owner | Core Resolver | relation-resolver.ts / reference-resolver.ts |
| input | [[DATA-MW-VAULT-MODEL-INDEX]] | indexed models and relations |
| output | [[DATA-MW-RENDERER-GRAPH-MODEL]] | ResolvedDiagram |
| references | resolveReferenceIdentity | model/member参照解決 |
| relations | resolveDiagramRelations | nodes / edges / missingObjects / warnings |
| flow_diagram | resolveDfdDiagramRelations | schema: flow_diagram。Flows endpointはローカルObjects.idで解決 |
| warnings | [[DATA-MW-CORE-DIAGNOSTIC]] | 解決時warning |

## Source Links

| path | notes |
|---|---|
| src/core/relation-resolver.ts | resolveDiagramRelations |
| src/core/relation-resolver.ts | Flow Diagram endpoint / Flows.data Wikilink diagnostics |
| src/core/reference-resolver.ts | reference resolution helpers |
