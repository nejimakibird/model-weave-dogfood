---
type: data_object
id: DATA-MW-CORE-DIAGNOSTIC
name: モデル診断情報
tags:
  - Core
  - Data
  - Diagnostic
---

# モデル診断情報

## Summary

モデルのパースやバリデーションの結果として生成される、エラー、警告、または情報を保持するデータオブジェクトです。

## Fields

| name | label | type | length | required | path | ref | notes |
|---|---|---|---|---|---|---|---|
| severity | 深刻度 | string | | Y | | | Error / Warning / Info |
| code | 診断コード | string | | Y | | | 例: W-TAB-001 |
| message | メッセージ内容 | string | | Y | | | ユーザー向けの説明文 |
| filePath | 対象ファイル | string | | Y | | | 問題が検出されたファイルのパス |
| section | 対象セクション | Optional<string> | | N | | | |
| row | 対象行番号 | Optional<number> | | N | | | |
| targetRef | 参照ターゲット | Optional<string> | | N | | | 未解決参照等の場合の対象識別子 |

## Notes

- Viewerの診断パネルおよびエディタ内でのハイライト表示のソースとして使用されます。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| source/model-weave-repo/src/types/models.ts | ValidationWarning | type | 診断情報のデータ構造定義 |