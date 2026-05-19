---
type: codeset
id: CODE-MW-CORE-MODEL-TYPE
name: モデル種別
kind: enum
tags:
  - Core
  - CodeSet
---

# モデル種別

## Summary

Model Weave でサポートされる各種モデルの定義済み種別（type）です。

## Values

| code | label | sort_order | active | notes |
|---|---|---:|---|---|
| class | クラス | 10 | Y | |
| er_entity | ER エンティティ | 20 | Y | |
| dfd_object | DFD オブジェクト | 30 | Y | |
| dfd_diagram | DFD ダイアグラム | 40 | Y | |
| data_object | データオブジェクト | 50 | Y | |
| app_process | アプリプロセス | 60 | Y | |
| screen | 画面 | 70 | Y | |
| rule | ルール | 80 | Y | |
| codeset | コードセット | 90 | Y | |
| message | メッセージセット | 100 | Y | |
| mapping | マッピング | 110 | Y | |
| er_diagram | ER ダイアグラム | 120 | Y | |
| class_diagram | クラスダイアグラム | 130 | Y | |

## Notes

- frontmatter の `type` フィールドで指定される値に対応します。