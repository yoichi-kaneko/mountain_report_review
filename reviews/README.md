# reviews/

1レビューサイクル = 1ディレクトリで管理します。ディレクトリ名は `YYYY-MM-DD_<slug>/`（`YYYY-MM-DD` は**草稿の登録日**、slug は半角英小文字とハイフン。例: `2026-07-08_kitadake/`）。

## ファイル構成

| ファイル | 内容 | 作成するスキル |
| --- | --- | --- |
| `draft.md` | レビュー前の草稿 | `/register_draft`（サイクル開始） |
| `review_gemini.md` | Gemini のレビュー応答 | `/register_gemini_review`（並走期間のみ） |
| `review_claude.md` | Claude のレビュー結果 | `/review_report` |
| `final.md` | 公開した最終版 | `/register_final`（サイクル完了の印） |
| `notes.md` | 任意。diff から読み取れないユーザーの所感 | `/register_final` の任意ステップ |

## 参照ルール

過去サイクルの内容を読むのは `review_feedback` と `distill_style_profile`（final.md のみ）だけです。日々のレビュー（`review_report`）は過去サイクルを参照しません（コンテキスト分離。詳細は CLAUDE.md）。
