---
type: class
id: CLS-MW-RENDERER-DIAGNOSTICS-COLLECTOR
name: DiagnosticsCollector
kind: class
package: model_weave.renderer
tags:
  - Renderer
  - Diagnostic
---

# DiagnosticsCollector

## Summary

モデルの解析中や、グラフの構築・解決中に発生した各種警告（未解決参照、不正な種別など）を収集し、ビューアー向けの診断リスト（DATA-MW-CORE-DIAGNOSTIC）として統合します。

## Attributes

| name | type | visibility | static | notes |
|---|---|---|---|---|

## Methods

| name | parameters | returns | visibility | static | notes |
|---|---|---|---|---|---|
| buildCurrentDiagramDiagnostics | diagram: ResolvedDiagram, warnings: object | object | public | Y | ダイアグラム用診断情報の収集。warningsは論理的には複数要素。DATA-MW-CORE-DIAGNOSTICを複数要素として返す |
| buildCurrentObjectDiagnostics | model: ParsedFileModel, index: ModelingVaultIndex, context: any, warnings: object | object | public | Y | 単体オブジェクト用診断情報の収集。warningsは論理的には複数要素。DATA-MW-CORE-DIAGNOSTICを複数要素として返す |

## Source Links

| path | notes |
|---|---|
| src/core/current-file-diagnostics.ts | symbol: buildCurrentDiagramDiagnostics; kind: function; 診断情報の統合実装 |
