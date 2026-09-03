# reviews/

1レビューサイクル = 1ディレクトリで管理します。ディレクトリ名は `YYYY-MM-DD_<slug>/`（`YYYY-MM-DD` は**草稿の登録日**、slug は半角英小文字とハイフン。例: `2026-07-08_kitadake/`）。

## ファイル構成

| ファイル | 内容 | 作成するスキル |
| --- | --- | --- |
| `draft.md` | レビュー前の草稿 | `/register_draft`（サイクル開始） |
| `review_external.md` | 外部LLM（1回目）のレビュー結果。指摘一覧とリライト案を含む | `/register_external_review` |
| `review_claude.md` | Claude（2回目）のレビュー結果。外部レビューの検証と、統合した指摘一覧・リライト案 | `/review_report` |
| `review_gemini.md` | Gemini のレビュー応答 | ―（過去の並走期間のサイクルにのみ存在。現在は作成しない） |
| `final.md` | 公開した最終版 | `/register_final`（サイクル完了の印） |
| `notes.md` | 任意。diff から読み取れないユーザーの所感 | `/register_final` の任意ステップ |

## 参照ルール

過去サイクルの内容を読むのは `review_feedback` と `distill_style_profile`（final.md のみ）だけです。日々のレビュー（`review_report`）は過去サイクルを参照しません（コンテキスト分離。詳細は CLAUDE.md）。

`review_report` が読んでよい同一サイクルのファイルは `draft.md` と `review_external.md` です。ただし `review_external.md` は、Claude が自前のレビューを固定してから開きます（アンカリング防止。[guidelines/review_guideline.md](../guidelines/review_guideline.md) セクション4）。
