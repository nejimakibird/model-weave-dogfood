---
type: app_process
id: PROC-MW-PREVIEW-RENDER-MODEL
name: Previewモデル描画
process_type: preview
tags:
  - ModelWeave
  - Preview
  - Renderer
  - Process
  - BusinessFlow
---

# Previewモデル描画

## Summary

現在のモデルファイルを解析済みモデルとして受け取り、render mode、diagnostics、relationship summary、renderer入力を組み立てて Modeling Preview View に表示する処理。
現行実装では `main.ts` 側が `view.updateContent` に状態を渡し、`ModelingPreviewView.renderCurrentState` がmode別の描画関数へdispatchする。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| parsedModel | [[DATA-MW-CORE-PARSED-MODEL]] | Parser / Vault Index | Y | 現在のファイルの解析結果 |
| viewerState | [[DATA-MW-VIEWER-STATE]] | [[DFD-MW-OBJ-PREVIEW-VIEW]] | Y | viewer preferencesとviewport state |
| vaultIndex | [[DATA-MW-VAULT-MODEL-INDEX]] | [[DFD-MW-OBJ-VAULT-INDEX]] | N | diagnostics / relations / impact summaryに使う |
| rendererSelection | [[DATA-MW-RENDERER-RENDER-MODE-INPUT]] | Render mode rule | N | toolbar / frontmatter / settingsから決まる |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| previewState | object | [[DFD-MW-OBJ-PREVIEW-VIEW]] | `updateContent` に渡されるmode別状態 |
| diagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | Preview diagnostics | parser warningと検証結果 |
| resolvedDiagram | [[DATA-MW-RENDERER-GRAPH-MODEL]] | [[DFD-MW-OBJ-RENDERER]] | diagram系renderer入力 |
| mermaidSource | [[DATA-MW-RENDERER-MERMAID-SOURCE]] | Renderer source panel | Mermaid render mode時の派生出力 |
| impactSummary | [[DATA-MW-IMPACT-SUMMARY]] | Preview relationship section | relationship view有効時に渡される |

## Steps

| id | lane | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | Preview Controller | Preview描画を開始する | start | parsedModel |  |  |  |  | active fileの解析結果から開始する |
| resolveRenderMode | Preview Controller | render modeを解決する | process | parsedModel, rendererSelection | rendererSelection | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] |  |  | toolbar / frontmatter / settings / fallbackを反映する |
| prepareImpact | Impact Analyzer | impact summaryを準備する | process | parsedModel, vaultIndex | impactSummary |  | [[PROC-MW-IMPACT-SUMMARY-BUILD]] |  | relationship viewが有効な場合に生成する |
| detectPreviewMode | Preview Controller | preview modeを判定する | decision | parsedModel | previewState |  |  |  | fileTypeごとのmodeへ分岐する |
| resolveDiagram | Resolver | diagram relationsを解決する | subflow | parsedModel, vaultIndex | resolvedDiagram |  | [[PROC-MW-RESOLVER-RESOLVE-DIAGRAM]] |  | diagram / dfd-diagramで使用する |
| buildDiagnostics | Preview Controller | diagnosticsを組み立てる | process | parsedModel, resolvedDiagram | diagnostics |  |  |  | parser warningsとcurrent file diagnosticsをまとめる |
| updateDiagram | Preview View | diagram preview stateを渡す | screen | resolvedDiagram, diagnostics | previewState |  |  |  | `mode: "diagram"` または `dfd-object` |
| updateSummary | Preview View | summary preview stateを渡す | screen | parsedModel, diagnostics | previewState |  |  |  | data_object / app_process / screenなど |
| updateEmpty | Preview View | empty preview stateを渡す | screen | parsedModel | previewState |  |  |  | unsupportedやmarkdown fallback |
| renderState | Preview View | renderCurrentStateで描画する | screen | previewState | preview DOM |  |  |  | mode別のrender関数へdispatchする |
| renderDiagram | Renderer | diagram rendererを実行する | subflow | resolvedDiagram | mermaidSource |  |  |  | `renderDiagramModel` がkind別rendererへdispatchする |
| renderSections | Preview View | diagnosticsと補助sectionを描画する | screen | diagnostics, impactSummary | preview DOM |  |  |  | Source Linksやrelationship sectionを含む場合がある |
| end | Preview View | Preview描画を終了する | end | preview DOM |  |  |  |  | viewport / scroll stateはview側で保持される |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | resolveRenderMode |  | render mode | 表示方式を決める |
| resolveRenderMode | prepareImpact |  | impact | relationship propsを準備する |
| prepareImpact | detectPreviewMode |  | mode判定 | fileTypeごとに分岐する |
| detectPreviewMode | resolveDiagram | diagram preview state | diagram系 | diagram relationsまたはDFD sceneを解決する |
| detectPreviewMode | updateSummary |  | summary | supported summary model |
| detectPreviewMode | updateEmpty |  | fallback | unsupported / markdown |
| resolveDiagram | buildDiagnostics |  | diagnostics | resolved warningsも含める |
| buildDiagnostics | updateDiagram |  | diagram state | preview stateへ渡す |
| updateDiagram | renderState |  | render | viewで描画する |
| updateSummary | renderState |  | render | viewで描画する |
| updateEmpty | renderState |  | render | empty stateを描画する |
| renderState | renderDiagram | `previewState.mode === "diagram"` | diagram renderer | diagram系のみrendererを呼ぶ |
| renderState | renderSections |  | sections | diagnostics / impact / source linksを描画する |
| renderDiagram | renderSections |  | sections | diagram描画後に補助sectionへ進む |
| renderSections | end |  | 完了 | DOM更新を終える |

## Notes

- このprocessはpreview描画の主要な実装経路を小さく要約したもので、すべてのfileType別分岐を詳細化しない。
- `main.ts` は解析済みモデル、diagnostics、rendererSelection、impactSummaryなどを `updateContent` に渡す。
- `ModelingPreviewView` は `state.mode` によって object / diagram / summary / relations / empty などの描画関数へdispatchする。
- Mermaid source生成の詳細は既存のrenderer processに委ね、このprocessではpreview pipeline上の位置づけだけを扱う。
- Explorer画面やExplorer stateはこの処理の対象外である。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/main.ts | syncPreviewToActiveFile | method | active fileからpreview stateを構築する主経路 |
| src/main.ts | buildImpactPreviewProps | method | relationship summary propsを構築する |
| src/views/modeling-preview-view.ts | updateContent | method | preview stateを受け取り再描画する |
| src/views/modeling-preview-view.ts | renderCurrentState | method | state.modeごとに描画関数へdispatchする |
| src/renderers/diagram-renderer.ts | renderDiagramModel | function | ResolvedDiagramをkind別rendererへdispatchする |
