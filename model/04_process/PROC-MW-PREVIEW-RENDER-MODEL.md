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

## Domain Sources

| ref | notes |
|---|---|
| [[DOMAINS-MW-ARCHITECTURE]] | Model Weave architecture domains |

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

| id | domain | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | preview_pipeline | Preview描画を開始する | start | parsedModel |  |  |  |  | active fileの解析結果から開始する |
| resolveRenderMode | preview_pipeline | render modeを解決する | process | parsedModel, rendererSelection | rendererSelection | [[RULE-MW-RENDERER-RENDER-MODE-RESOLUTION]] |  |  | toolbar / frontmatter / settings / fallbackを反映する |
| prepareImpact | impact_analysis | impact summaryを準備する | process | parsedModel, vaultIndex | impactSummary |  | [[PROC-MW-IMPACT-SUMMARY-BUILD]] |  | relationship viewが有効な場合に生成する |
| detectPreviewMode | preview_pipeline | preview modeを判定する | decision | parsedModel | previewState |  |  |  | fileTypeごとのmodeへ分岐する |
| resolveDiagram | relation_resolution | diagram relationsを解決する | subflow | parsedModel, vaultIndex | resolvedDiagram |  | [[PROC-MW-RESOLVER-RESOLVE-DIAGRAM]] |  | diagram / dfd-diagramで使用する |
| buildDiagnostics | diagnostics | diagnosticsを組み立てる | process | parsedModel, resolvedDiagram | diagnostics |  |  |  | parser warningsとcurrent file diagnosticsをまとめる |
| updateDiagram | viewer_ui | diagram preview stateを渡す | screen | resolvedDiagram, diagnostics | previewState |  |  |  | `mode: "diagram"` または `dfd-object` |
| updateSummary | viewer_ui | summary preview stateを渡す | screen | parsedModel, diagnostics | previewState |  |  |  | data_object / app_process / screenなど |
| updateEmpty | viewer_ui | empty preview stateを渡す | screen | parsedModel | previewState |  |  |  | unsupportedやmarkdown fallback |
| renderState | viewer_ui | renderCurrentStateで描画する | screen | previewState | preview DOM |  |  |  | mode別のrender関数へdispatchする |
| renderDiagram | renderer_area | diagram rendererを実行する | subflow | resolvedDiagram | mermaidSource |  |  |  | `renderDiagramModel` がkind別rendererへdispatchする |
| renderSections | viewer_ui | diagnosticsと補助sectionを描画する | screen | diagnostics, impactSummary | preview DOM |  |  |  | Source Linksやrelationship sectionを含む場合がある |
| end | viewer_ui | Preview描画を終了する | end | preview DOM |  |  |  |  | viewport / scroll stateはview側で保持される |

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

| path | notes |
|---|---|
| src/main.ts | Steps: start, resolveRenderMode, prepareImpact, detectPreviewMode. syncPreviewToActiveFile / buildImpactPreviewProps がpreview stateを組み立てる |
| src/views/modeling-preview-view.ts | Steps: updateDiagram, updateSummary, updateEmpty, renderState, renderSections, end. updateContent / renderCurrentState がstateを受け取り再描画する |
| src/renderers/diagram-renderer.ts | Steps: renderDiagram. renderDiagramModel がResolvedDiagramをkind別rendererへdispatchする |
