---
type: color_scheme
id: COLOR-MW-DOGFOOD
name: Model Weave Dogfood Color Scheme
tags:
  - ModelWeave
  - ColorScheme
---

# Model Weave Dogfood Color Scheme

## Summary

Model Weave dogfood vault の Domain group、DFD object、app_process Business Flow step、Weave Map layer を確認するための代表配色です。
このファイルは `defaultColorSchemeRef` から参照されるColor Schemeの例であり、Markdownモデルの正本内容を書き換えず、描画結果の見た目に反映されます。

## Colors

| target | kind | fill | stroke | text | notes |
|---|---|---|---|---|---|
| domain | application | #DDEBFF | #4F81BD | #111111 | Application boundary |
| domain | model | #E8DDF7 | #8064A2 | #111111 | Markdown model area |
| domain | data | #E2F0D9 | #70AD47 | #111111 | Data / storage area |
| domain | integration | #FFF2CC | #D6B656 | #111111 | Integration / resolver area |
| domain | renderer | #DDEBF7 | #5B9BD5 | #111111 | Renderer area |
| domain | ui | #FCE4D6 | #ED7D31 | #111111 | Viewer UI area |
| domain | operations | #F4CCCC | #C00000 | #111111 | Diagnostics / impact operations |
| domain | external | #E7E6E6 | #7F7F7F | #111111 | External area |
| domain | export | #D9EAD3 | #38761D | #111111 | Export area |
| dfd | external | #E7E6E6 | #7F7F7F | #111111 | DFD external object |
| dfd | process | #DDEBFF | #4F81BD | #111111 | DFD process object |
| dfd | datastore | #E2F0D9 | #70AD47 | #111111 | DFD datastore object |
| app_process | start | #D9EAD3 | #38761D | #111111 | Business Flow start |
| app_process | process | #DDEBFF | #4F81BD | #111111 | Business Flow process |
| app_process | decision | #FFF2CC | #D6B656 | #111111 | Business Flow decision |
| app_process | subflow | #E8DDF7 | #8064A2 | #111111 | Business Flow subflow |
| app_process | screen | #FCE4D6 | #ED7D31 | #111111 | Business Flow screen |
| app_process | end | #F4CCCC | #C00000 | #111111 | Business Flow end |
| weave_map | ui | #FCE4D6 | #ED7D31 | #111111 | Weave Map UI layer |
| weave_map | process | #DDEBFF | #4F81BD | #111111 | Weave Map process layer |
| weave_map | rule | #FFF2CC | #D6B656 | #111111 | Weave Map rule layer |
| weave_map | rule_state | #FCE4D6 | #ED7D31 | #111111 | Weave Map rule state layer |
| weave_map | ui_message | #F4CCCC | #C00000 | #111111 | Weave Map UI message layer |
| weave_map | data | #E2F0D9 | #70AD47 | #111111 | Weave Map data layer |
| weave_map | mapping | #E8DDF7 | #8064A2 | #111111 | Weave Map mapping layer |
| weave_map | implementation | #DDEBF7 | #5B9BD5 | #111111 | Weave Map implementation layer |
| weave_map | data_flow | #D9EAD3 | #38761D | #111111 | Weave Map data flow layer |
| weave_map | relationship | #E7E6E6 | #7F7F7F | #111111 | Weave Map relationship layer |
| weave_map | source | #FFF2CC | #D6B656 | #111111 | Weave Map source layer |
| weave_map | warning | #F4CCCC | #C00000 | #111111 | Weave Map warning layer |
| weave_map | other | #E7E6E6 | #7F7F7F | #111111 | Weave Map fallback layer |

## Notes

- `target=domain` は domains / domain_diagram / DFD Domain subgraph / app_process Business Flow Domain group に使われる。
- `target=dfd` は DFD object kind に使われる。
- `target=app_process` は app_process Business Flow の Steps.kind に使われる。
- `target=weave_map` は Relationship View内の Weave Map layer に使われる。Weave Map layerはtarget-specificで、global kindやglobal defaultではlayer既定色を置き換えない。
- `defaultColorSchemeRef` が未設定、未解決、または color_scheme 以外を指す場合、Viewerは組み込み配色へfallbackする。
- Applied Color Scheme sectionはViewer下部の確認表示であり、このColor Scheme fileの内容そのものやMermaid source本文ではない。
- PNG exportで色が保持される場合も、これは描画結果の出力でありMarkdownモデルの正本を変更しない。

## Source Links

| path | notes |
|---|---|
| src/core/color-scheme.ts | resolveDefaultColorScheme / resolveColorStyle が defaultColorSchemeRef と target / kind から色を解決する |
| src/core/color-scheme.ts | getAppliedColorSchemeRowsForTargets が Applied Color Scheme section の表示行を構築する |
| src/renderers/dfd-mermaid.ts | target=dfd と target=domain をDFD Mermaid sourceへ反映する |
| src/renderers/app-process-business-flow.ts | target=app_process と target=domain をBusiness Flow Mermaid sourceへ反映する |
| src/renderers/weave-map-mermaid.ts | target=weave_map をWeave Map layerへ反映する |
| src/views/applied-color-scheme-renderer.ts | Applied Color Scheme section のsummaryとtableを表示する |

