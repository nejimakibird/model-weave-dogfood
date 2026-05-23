---
type: data_object
id: DATA-MW-CORE-DIAGNOSTIC
name: モデル診断情報
kind: diagnostic
data_format: object
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
| section | 対象セクション | string | | N | | | 任意項目。未設定の場合あり |
| row | 対象行番号 | number | | N | | | 任意項目。未設定の場合あり |
| targetRef | 参照ターゲット | string | | N | | | 任意項目。未解決参照等の場合の対象識別子 |

## Notes

- Viewerの診断パネルおよびエディタ内でのハイライト表示のソースとして使用されます。
- Markdownテーブル安全性のため、type列は単純型で表現し、任意性は required / notes で表現します。

## Source Links

| path | symbol | kind | notes |
|---|---|---|---|
| src/types/models.ts | ValidationWarning | type | 診断情報のデータ構造定義 |
