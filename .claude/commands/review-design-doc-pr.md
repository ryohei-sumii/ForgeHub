---
description: 設計ドキュメントPR（docs/design/*.md）をレビューし、GitHub PRにインラインコメントを投稿する
argument-hint: [PR番号（省略時は現在のブランチのPR）]
---

対象PR: $ARGUMENTS （省略時は現在の作業ブランチの head から owner=ryohei-sumii repo=forgehub の該当PRを特定する）。

手順:

1. `design-doc-reviewer` サブエージェント（Agent tool, subagent_type=design-doc-reviewer）を起動し、対象PR番号またはブランチ名を渡す。foreground で実行し完了を待つ。
2. エージェントから返る電文形式の最終報告（`PR=` / `FINDINGS=` / `EVENT=` / `OPEN=` 等）を受け取り、ユーザーには自然文で短く要約する（指摘件数と内訳、レビュー結果（REQUEST_CHANGES/COMMENT/APPROVE）、未対応の残件があれば明記）。

エージェントの内部処理（差分把握・突き合わせ・投稿の電文）は呼び出し元では展開・再掲しない。
