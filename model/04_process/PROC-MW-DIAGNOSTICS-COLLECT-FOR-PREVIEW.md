---
type: app_process
id: PROC-MW-DIAGNOSTICS-COLLECT-FOR-PREVIEW
name: Preview診断収集
process_type: diagnostics
tags:
  - ModelWeave
  - Diagnostic
  - Preview
  - Process
  - BusinessFlow
---

# Preview診断収集

## Summary

現在のモデルファイルに対して、parser / index / validator / resolver / render mode / current-file checks から発生した `ValidationWarning` を集約し、Preview state の warnings として渡して表示する処理。
現行実装では `main.ts` が warnings を組み立て、`buildCurrentObjectDiagnostics` または `buildCurrentDiagramDiagnostics` で正規化・重複排除し、`ModelingPreviewView` が Notes / Warnings / Errors に分けて描画する。

## Inputs

| id | data | source | required | notes |
|---|---|---|---|---|
| parsedModel | [[DATA-MW-CORE-PARSED-MODEL]] | [[DFD-MW-OBJ-PARSER]] | Y | 現在のファイルの解析結果 |
| vaultIndex | [[DATA-MW-VAULT-MODEL-INDEX]] | [[DFD-MW-OBJ-VAULT-INDEX]] | Y | `warningsByFilePath` と検証済み状態を持つ |
| indexWarnings | [[DATA-MW-CORE-DIAGNOSTIC]] | `warningsByFilePath` | N | parser / index / validator由来のwarning |
| resolverWarnings | [[DATA-MW-CORE-DIAGNOSTIC]] | [[DFD-MW-OBJ-RESOLVER]] | N | diagram解決時のwarning |
| renderModeWarnings | [[DATA-MW-CORE-DIAGNOSTIC]] | Render mode resolution | N | frontmatterや設定のfallback診断 |

## Outputs

| id | data | target | notes |
|---|---|---|---|
| diagnostics | [[DATA-MW-CORE-DIAGNOSTIC]] | [[DATA-MW-VIEWER-STATE]].diagnostics | Preview stateへ渡される診断 |
| previewWarnings | [[DATA-MW-CORE-DIAGNOSTIC]] | [[DFD-MW-OBJ-PREVIEW-VIEW]] | `view.updateContent` のwarnings |
| diagnosticSections | object | Preview DOM | Notes / Warnings / Errors の表示グループ |
| diagnosticOpenRequest | object | Editor navigation | click可能な診断からのopen要求 |

## Steps

| id | lane | label | kind | input | output | rule | invoke | screen | notes |
|---|---|---|---|---|---|---|---|---|---|
| start | Preview Controller | 診断収集を開始する | start | parsedModel, vaultIndex |  |  |  |  | active fileのpreview更新から開始する |
| collectIndexWarnings | Vault Index | index warningを取得する | process | vaultIndex | indexWarnings |  |  |  | `warningsByFilePath[file.path]` を参照する |
| validateVault | Vault Index | Vault検証warningを保持する | process | vaultIndex | indexWarnings |  |  |  | `ensureVaultValidation` が `validateVaultIndex` 結果を格納する |
| mergeRuntimeWarnings | Preview Controller | runtime warningを追加する | process | indexWarnings, renderModeWarnings, resolverWarnings | warning candidates |  |  |  | render mode / resolver / context warningを含める |
| chooseDiagnosticPath | Preview Controller | current-file診断経路を判定する | decision | parsedModel |  |  |  |  | diagram系とobject系で関数が異なる |
| objectDiagnostics | Diagnostics | object系診断を生成する | process | parsedModel, warning candidates | diagnostics |  |  |  | `buildCurrentObjectDiagnostics` を使う |
| diagramDiagnostics | Diagnostics | diagram系診断を生成する | process | resolved diagram, warning candidates | diagnostics |  |  |  | `buildCurrentDiagramDiagnostics` を使う |
| passPreviewState | Preview Controller | diagnosticsをpreview stateへ渡す | screen | diagnostics | previewWarnings |  |  |  | `view.updateContent` のwarningsに設定する |
| groupBySeverity | Preview View | severity別に分類する | process | previewWarnings | diagnosticSections |  |  |  | info / warning / error に分ける |
| renderGroups | Preview View | 診断グループを描画する | screen | diagnosticSections | preview DOM |  |  |  | Notes / Warnings / Errors detailsを作る |
| clickableCheck | Preview View | 診断を開けるか判定する | decision | previewWarnings |  |  |  |  | `onOpenDiagnostic` がある場合だけclick可能にする |
| openDiagnostic | Preview Controller | 診断位置を開く | screen | diagnosticOpenRequest | editor location |  |  |  | `openDiagnosticLocation` が対象ファイルと行を解決する |
| end | Preview View | 診断表示を終了する | end | preview DOM |  |  |  |  | 診断が空の場合は何も描画しない |

## Flows

| from | to | condition | label | notes |
|---|---|---|---|---|
| start | collectIndexWarnings |  | index warnings | file path別warningを取得する |
| collectIndexWarnings | validateVault |  | validation | Vault検証済みwarningもindexに含まれる |
| validateVault | mergeRuntimeWarnings |  | merge | runtime warning候補をまとめる |
| mergeRuntimeWarnings | chooseDiagnosticPath |  | 経路判定 | preview対象の種類で分岐する |
| chooseDiagnosticPath | diagramDiagnostics | diagram preview state | diagram | resolved diagram用の診断経路 |
| chooseDiagnosticPath | objectDiagnostics |  | object系 | 現在ファイルのmodel-type別診断 |
| objectDiagnostics | passPreviewState |  | previewへ渡す | dedupe済みdiagnosticsを渡す |
| diagramDiagnostics | passPreviewState |  | previewへ渡す | dedupe済みdiagnosticsを渡す |
| passPreviewState | groupBySeverity |  | severity分類 | Preview View側で分類する |
| groupBySeverity | renderGroups |  | 描画 | Notes / Warnings / Errorsを描画する |
| renderGroups | clickableCheck |  | click判定 | open handlerの有無を確認する |
| clickableCheck | openDiagnostic | `onOpenDiagnostic` | open | 診断クリックで対象位置を開く |
| clickableCheck | end |  | 表示のみ | click handlerなし |
| openDiagnostic | end |  | 完了 | editor navigation後に終了する |

## Notes

- Parser warning、Vault validation warning、render mode warning、resolver warning、current-file diagnostics はすべて `ValidationWarning` として扱われる。
- `warningsByFilePath` は parser / index / validator 由来のwarningをfile path別に保持する。
- `buildCurrentObjectDiagnostics` はformat別のチェックを追加し、severityを正規化して重複を除く。
- `buildCurrentDiagramDiagnostics` はdiagram系warningを正規化して重複を除く。
- `renderDiagnostics` はdiagnosticsを info / warning / error に分け、Notes / Warnings / Errors として表示する。
- `openDiagnosticLocation` は診断にfile pathやline情報がある場合に、対応するMarkdown位置を開くためのpreview側操作である。
- Explorer stateやplannedの診断パネル表示設定はこの処理の対象外である。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/core/validator.ts | validateVaultIndex | function | Vault全体の検証warningを生成する |
| src/core/current-file-diagnostics.ts | buildCurrentObjectDiagnostics | function | current-file診断を生成する |
| src/core/current-file-diagnostics.ts | buildCurrentDiagramDiagnostics | function | diagram診断を正規化する |
| src/core/vault-index.ts | warningsByFilePath | field | file path別warning map |
| src/core/vault-index.ts | ensureVaultValidation | function | vault validation warningをindexへ格納する |
| src/main.ts | view.updateContent | usage | warningsをPreview stateへ渡す |
| src/main.ts | openDiagnosticLocation | method | 診断クリック時のファイル位置open |
| src/views/modeling-preview-view.ts | renderDiagnostics | function | Notes / Warnings / Errors を描画する |
| src/types/models.ts | ValidationWarning | type | 診断データ構造 |
