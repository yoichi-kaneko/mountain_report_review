# CLAUDE.md

登山レポートのAIレビューと、そのレビュー品質を改善し続けるフィードバックループを管理するリポジトリです。

## 目的

1. ユーザーが書いた登山レポートの草稿を Claude がレビューする（指摘一覧＋セクション構成を維持したほぼ全文のリライト案）
2. レビュー結果と実際の公開版との差分から「どの提案が採用されたか」を定期的に集計し、レビューガイドラインを人手承認で更新する

## ディレクトリ構成

```
trip_report_review/
├── CLAUDE.md                      # このファイル
├── docs/
│   ├── text_to_markdown.md       # テキスト→Markdown 変換の共通規則
│   └── tags.md                   # レポートタグの統制語彙と付与ルール
├── guidelines/
│   ├── review_guideline.md       # レビュー観点の正本（メタレビューで育てる対象）
│   └── style_profile.md          # 文体プロファイル（過去レポートから蒸留）
├── corpus/                        # 過去に公開済みのレポート本文（/import_report で登録）
├── reviews/                       # 1レビューサイクル = 1ディレクトリ
│   └── YYYY-MM-DD_<slug>/
│       ├── draft.md               # レビュー前の草稿
│       ├── review_claude.md       # Claude のレビュー結果
│       ├── review_gemini.md       # Gemini の応答（過去の並走期間のサイクルのみ。現在は作成しない）
│       ├── final.md               # 公開した最終版（サイクル完了の印）
│       └── notes.md               # 任意：ユーザーの所感
├── meta/
│   └── review_markers.md          # メタレビューの実施記録（次回の対象期間の起点）
└── .claude/skills/                # 各スキル（下記フロー参照）
```

## 1レビューサイクルの流れ

1. `/register_draft` — 草稿（テキストベース）と公開URLを登録。`reviews/YYYY-MM-DD_<slug>/draft.md` が作られ、サイクル開始
2. `/review_report` — Claude がレビューを実行し `review_claude.md` を出力
3. レビューを取捨選択してレポートを修正・公開したら、`/register_final` で公開版を登録（サイクル完了）
4. 完了サイクルが3〜5件たまったら `/review_feedback` でメタレビューを実施し、ガイドラインを育てる

補助スキル:

- `/import_report` — 過去に公開済みのレポートを公開URLから1件ずつ `corpus/` に取り込む
- `/distill_style_profile` — corpus と公開版から文体プロファイルを蒸留・更新する（人手承認で反映）

## 参照の原則（コンテキスト分離）

- `review_report` が読んでよいのは、guidelines/ の2ファイル、当該サイクルの draft.md、直近の公開版2〜3本、およびタグ検索で選んだ類似条件の公開版1〜2本まで。frontmatter（タグ）だけの横断検索は `corpus/*.md`・`reviews/*/final.md` に対して行ってよいが、本文まで読むのはその1〜2本に限る（[docs/tags.md](docs/tags.md) 参照）。**過去サイクルのレビュー結果・notes.md は読まない。corpus/ と過去 final の本文を上記の範囲を超えて読まない**
- reviews/ の過去分と corpus/ 全体を横断して読むのは `review_feedback` と `distill_style_profile` だけ
- guidelines/ 配下の更新は、これら2スキルが提示した差分案をユーザーが承認したときのみ行う。それ以外の場面で勝手に編集しない

## 運用状況

- レビューは **Claude に一本化済み**（2026-07-31）。かつて Gemini と並走していた期間の記録は `reviews/*/review_gemini.md` として残っているが、新規サイクルでは作成しない
- メタレビュー（`review_feedback`）が過去の並走期間のサイクルを対象に含む場合のみ、`review_gemini.md` を材料として読む

## 共通ルール

- 日付は実行環境の日本標準時（JST）の現在日を用いる
- テキスト→Markdown 変換は [docs/text_to_markdown.md](docs/text_to_markdown.md) に従う。**変換で本文の語句を変えない**
- corpus / final の frontmatter に付与するタグは [docs/tags.md](docs/tags.md) の統制語彙に従う。語彙にない値はユーザー承認を得て docs/tags.md に追記してから使う
- ツール呼び出し（WebFetch 等）が失敗した場合は1回だけ再試行し、それでも失敗したらユーザーに報告して指示を仰ぐ
