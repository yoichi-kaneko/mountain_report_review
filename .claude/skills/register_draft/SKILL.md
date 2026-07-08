---
name: register_draft
description: 登山レポートの草稿（テキストベース）をMarkdownに変換し、reviews/配下に新しいレビューサイクルのディレクトリとdraft.mdを作成する。レビューサイクルの起点となるスキル。
---

# register_draft

レビューサイクルを開始し、草稿を `reviews/YYYY-MM-DD_<slug>/draft.md` として登録します。ディレクトリ名の基準日は公開日ではなく**山行日**です。

## 手順

1. **草稿の受け取り**: 引数でファイルパスが渡された場合は Read で読み込む。渡されていない場合は、草稿テキストの貼り付けまたはファイルパスの指定をユーザーに依頼する。
2. **サイクルディレクトリ名の決定**: `reviews/YYYY-MM-DD_<slug>/` を作る。
   - `YYYY-MM-DD` は山行日。ユーザーから山行日が別途テキストで渡されている前提で使う。未指定ならユーザーに確認する。
   - slug は山名などを半角英小文字とハイフンで表したもの（例: `2026-07-08_kitadake`）。ユーザー指定がなければ草稿の内容から候補を提案し、確認を取ってから確定する。
   - 同名ディレクトリが既に存在する場合は、slug の変更をユーザーに相談する。
3. **Markdown 変換**: [docs/text_to_markdown.md](../../../docs/text_to_markdown.md) に従って変換する。**本文の語句は一切変えない**。
4. **保存**: frontmatter（`title` / `created_at`）を付けて `draft.md` として Write する。
5. **報告**: 作成したパスを報告し、次の手順（並走期間中なら Gemini への依頼と `/register_gemini_review`、Claude レビューなら `/review_report`）を一言案内する。
