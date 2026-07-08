---
name: import_report
description: 過去に公開済みの登山レポートを公開URLから1件取得し、Markdown変換してcorpus/に登録する。文体プロファイル蒸留の材料となる過去レポートの取り込み用。
---

# import_report

過去レポートを1件ずつ `corpus/` に取り込みます。本システム運用開始後の新しいレポートは `reviews/*/final.md` がコーパスを兼ねるため、このスキルの対象は運用開始前の過去レポートです。

## 手順

1. **取得**: 引数の公開URLを WebFetch で取得する。失敗時は1回だけ再試行し、それでも失敗したらユーザーに報告して終了する。本文・タイトル・公開日を抽出する。
2. **公開日の確認**: ページから公開日が読み取れない場合はユーザーに確認する。
3. **Markdown 変換**: [docs/text_to_markdown.md](../../../docs/text_to_markdown.md) に従い、**語句を変えずに**変換する。frontmatter は `title` / `published_at` / `source_url` / `created_at`。
4. **保存**: `corpus/YYYY-MM-DD_<slug>.md`（`YYYY-MM-DD` は公開日）として Write する。slug は山名等から候補を提案し、ユーザーに確認してから確定する。同名ファイルが既にある場合は上書きせず相談する。
5. **報告**: 保存したパスと、`corpus/` 内の登録済み件数を報告する。5本程度たまっていて文体プロファイルが未蒸留（または蒸留が古い）なら、`/distill_style_profile` の実施を案内する。
