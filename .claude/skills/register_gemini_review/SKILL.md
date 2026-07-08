---
name: register_gemini_review
description: Geminiから受け取ったレビュー応答を、該当レビューサイクルのreview_gemini.mdとして保存する。GeminiとClaudeの並走期間に使うスキル。
---

# register_gemini_review

並走期間中、Gemini のレビュー応答をサイクルに記録します。メタレビュー（`review_feedback`）での採用分析と Gemini / Claude 比較の材料になります。

## 手順

1. **対象サイクルの特定**: 引数でサイクルディレクトリが指定されていればそれを使う。指定がなければ、`reviews/` 直下で最新（ディレクトリ名の日付降順）かつ `review_gemini.md` 未作成のものを候補として提示し、ユーザーに確認する。
2. **応答の受け取り**: Gemini の応答テキストの貼り付けまたはファイルパスの指定をユーザーに依頼する（引数で渡されていればそれを使う）。
3. **Markdown 整形**: [docs/text_to_markdown.md](../../../docs/text_to_markdown.md) の大原則に従い、**語句を変えずに**整形する。Gemini の応答が既に見出しや箇条書きの構造を持つ場合は、原文の構造をそのまま優先する。
4. **保存**: frontmatter（`reviewer: gemini` / `reviewed_at`）を付けて `review_gemini.md` として Write する。
5. **報告**: 保存したパスを報告する。
