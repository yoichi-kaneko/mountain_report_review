# CLAUDE.md

登山レポートのAIレビューと、そのレビュー品質を改善し続けるフィードバックループを管理するリポジトリです。

## 目的

1. ユーザーが書いた登山レポートの草稿を**2段構成でレビューする**（外部LLMによる1回目 → Claude が突き合わせて統合する2回目。成果物は指摘一覧＋セクション構成を維持したほぼ全文のリライト案）
2. レビュー結果と実際の公開版との差分から「どの提案が採用されたか」を定期的に集計し、レビューガイドラインを人手承認で更新する

## ディレクトリ構成

```
trip_report_review/
├── CLAUDE.md                      # このファイル
├── docs/
│   ├── text_to_markdown.md       # テキスト→Markdown 変換の共通規則
│   ├── tags.md                   # レポートタグの統制語彙と付与ルール
│   └── external_review.md        # 外部レビューの運用規則とプロンプト雛形
├── guidelines/
│   ├── review_guideline.md       # レビュー観点の正本（メタレビューで育てる対象）
│   └── style_profile.md          # 文体プロファイル（過去レポートから蒸留）
├── corpus/                        # 過去に公開済みのレポート本文（/import_report で登録）
├── reviews/                       # 1レビューサイクル = 1ディレクトリ
│   └── YYYY-MM-DD_<slug>/
│       ├── draft.md               # レビュー前の草稿
│       ├── review_external.md     # 外部LLM（1回目）のレビュー結果
│       ├── review_claude.md       # Claude（2回目・突き合わせ）のレビュー結果
│       ├── review_gemini.md       # Gemini の応答（過去の並走期間のサイクルのみ。現在は作成しない）
│       ├── final.md               # 公開した最終版（サイクル完了の印）
│       └── notes.md               # 任意：ユーザーの所感
├── meta/
│   └── review_markers.md          # メタレビューの実施記録（次回の対象期間の起点）
└── .claude/skills/                # 各スキル（下記フロー参照）
```

## 1レビューサイクルの流れ

1. `/register_draft` — 草稿（テキストベース）と公開URLを登録。`reviews/YYYY-MM-DD_<slug>/draft.md` が作られ、サイクル開始
2. `/prepare_external_review` — 外部LLM（Cursor 等）に渡すレビュー依頼プロンプトを `tmp/` に生成。ユーザーが外部LLMに投入する
3. `/register_external_review` — 外部LLMの応答を `review_external.md` として登録（1回目のレビュー。指摘一覧とリライト案を含む）
4. `/review_report` — Claude が草稿と外部レビューを突き合わせ、統合した `review_claude.md` を出力（2回目のレビュー）
5. レビューを取捨選択してレポートを修正・公開したら、`/register_final` で公開版を登録（サイクル完了）
6. 完了サイクルが3〜5件たまったら `/review_feedback` でメタレビューを実施し、ガイドラインを育てる

外部レビューに渡すのはガイドラインの**核（セクション1）と出力形式（セクション3）、文体プロファイル**までで、**観点（セクション2「彩り」）は渡さない**。詳細は [docs/external_review.md](docs/external_review.md)

補助スキル:

- `/import_report` — 過去に公開済みのレポートを公開URLから1件ずつ `corpus/` に取り込む
- `/distill_style_profile` — corpus と公開版から文体プロファイルを蒸留・更新する（人手承認で反映）

## 参照の原則（コンテキスト分離）

- `review_report` が読んでよいのは、guidelines/ の2ファイル、**当該サイクルの** draft.md と review_external.md、直近の公開版2〜3本、およびタグ検索で選んだ類似条件の公開版1〜2本まで。frontmatter（タグ）だけの横断検索は `corpus/*.md`・`reviews/*/final.md` に対して行ってよいが、本文まで読むのはその1〜2本に限る（[docs/tags.md](docs/tags.md) 参照）。**過去サイクルのレビュー結果（review_external.md を含む）・notes.md は読まない。corpus/ と過去 final の本文を上記の範囲を超えて読まない**
- `review_report` は**当該サイクルの review_external.md も、自前のレビューを固定してから開く**。外部の指摘に引きずられないための順序であり、省略しない（[guidelines/review_guideline.md](guidelines/review_guideline.md) セクション4）
- `prepare_external_review` が外部LLMへ渡してよいのは、当該サイクルの draft.md・重点ポイント・ガイドラインの核と出力形式・文体プロファイルのみ。過去のレポートやレビューは渡さない
- reviews/ の過去分と corpus/ 全体を横断して読むのは `review_feedback` と `distill_style_profile` だけ
- guidelines/ 配下の更新は、これら2スキルが提示した差分案をユーザーが承認したときのみ行う。それ以外の場面で勝手に編集しない

## 運用状況

レビュー体制は時期によって異なる。メタレビュー（`review_feedback`）は対象期間がまたぐフェーズを判別し、集計を混ぜない。下表の期間は**レビュー実施日**の目安で、個々のサイクルがどのフェーズかはサイクル内のレビューファイルの顔ぶれで判定する（草稿登録が山行日より遅れる運用のため、ディレクトリ名の日付では判定しない）。

| 期間 | 体制 | サイクル内のレビューファイル |
| --- | --- | --- |
| 〜2026-07-30 | **並走期**: Claude と Gemini が独立にレビュー | `review_claude.md` + `review_gemini.md` |
| 2026-07-31〜2026-09-02 | **Claude 単独期** | `review_claude.md` |
| 2026-09-03〜 | **2段レビュー期**: 外部LLM（1回目）→ Claude の突き合わせ（2回目） | `review_external.md` + `review_claude.md` |

- 過去のフェーズのファイルはリネームせず、そのまま保全する（`review_gemini.md` を含む）
- 外部レビューを担当するモデル／ツールを差し替えてもファイル名は `review_external.md` のまま。`reviewer` / `model` は frontmatter に記録し、切り替え時はこの表に1行追記する（[docs/external_review.md](docs/external_review.md) セクション5）

## 共通ルール

- 日付は実行環境の日本標準時（JST）の現在日を用いる
- テキスト→Markdown 変換は [docs/text_to_markdown.md](docs/text_to_markdown.md) に従う。**変換で本文の語句を変えない**。外部レビューの取り込み（`register_external_review`）も同じ規則に従い、挨拶や前置きも含めて原文のまま保存する
- corpus / final の frontmatter に付与するタグは [docs/tags.md](docs/tags.md) の統制語彙に従う。語彙にない値はユーザー承認を得て docs/tags.md に追記してから使う
- ツール呼び出し（WebFetch 等）が失敗した場合は1回だけ再試行し、それでも失敗したらユーザーに報告して指示を仰ぐ
