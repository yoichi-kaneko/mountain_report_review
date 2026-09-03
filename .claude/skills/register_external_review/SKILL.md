---
name: register_external_review
description: 外部LLM（Cursor等）が返したレビュー応答をMarkdownに変換し、該当サイクルのreview_external.mdとして登録する。2段レビューの1段目の記録で、次のreview_reportが突き合わせの材料に使う。
---

# register_external_review

外部LLMのレビュー応答を `reviews/<cycle>/review_external.md` として取り込みます。運用規則は [docs/external_review.md](../../../docs/external_review.md) セクション4が正本です。

**この工程はレビューではありません。**内容の評価・取捨選択・要約は一切行わず、原文のまま構造だけ整えて保存します。評価は次の `/review_report`（突き合わせ）が行います。

## 手順

1. **対象サイクルの特定**: 引数でサイクルディレクトリが指定されていればそれを使う。指定がなければ、`reviews/` 直下で `draft.md` があり `review_external.md` 未作成のもののうち最新（ディレクトリ名の日付降順）を候補として提示し、ユーザーに確認する。既に `review_external.md` がある場合は、上書きしてよいかを必ず確認する。
2. **応答の受け取り**: 引数でファイルパスが渡された場合は Read で読み込む。渡されていない場合は、応答テキストの貼り付けまたはファイルパスの指定をユーザーに依頼する。
3. **レビュアーの確認**: `reviewer`（ツール／サービス名。例: `cursor`）と `model`（分かる場合のみ。不明なら `unknown`）をユーザーに確認する。会話で既に分かっていれば再確認しない。
4. **Markdown 変換**: [docs/text_to_markdown.md](../../../docs/text_to_markdown.md) に従って構造を整える。
   - **本文の語句を一切変えない。** 挨拶・前置き・自己言及（「拝見しました！」等）も削らずそのまま残す。レビュアーの癖はメタレビューの材料になる。
   - リライト案が引用ブロック（`>`）やコードフェンスで返ってきた場合は、その形のまま残す。
   - 指摘の番号を振り直さない。`［※指摘N］` マーカーもそのまま残す。
5. **構造の確認**: 総評 / 指摘一覧 / 全文リライト案の3部が揃っているかを確認する。欠けていても保存は行い、何が欠けているかを手順7で報告する（突き合わせ側で吸収する）。
6. **保存**: frontmatter（`reviewer` / `model` / `reviewed_at`（取り込み日・JST） / `guideline_updated_at`（プロンプトに用いたガイドラインの変更履歴の最新日付。`prepare_external_review` の報告か、guidelines/review_guideline.md の変更履歴の最新日付を用いる） / `review_focus`（渡していれば））を付けて `review_external.md` として Write する。
7. **報告**: 保存したパス、`reviewer` / `model`、指摘の件数（番号から数えられる場合）、3部構成の欠けがあればその旨を報告し、次の手順（`/review_report` での突き合わせ）を案内する。
