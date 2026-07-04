---
description: 開いているPRの未解決レビューコメントに対応する（リストアップ→妥当性確認→修正→コミット→プッシュ→返答）
argument-hint: [PR番号（省略時は現在のブランチのPR）]
---

対象PR: $ARGUMENTS （省略時は現在の作業ブランチの head から owner=ryohei-sumii repo=forgehub の該当PRを特定する）。

手順:

1. `pr-comment-resolver` サブエージェント（Agent tool, subagent_type=pr-comment-resolver）を起動し、対象PR番号またはブランチ名を渡す。foreground で実行し完了を待つ。
2. エージェントから返る電文形式の最終報告（`PR=` / `TOTAL=` / `COMMIT=` / `PUSH=` / `REPLIED=` / `OPEN=` 等）を受け取り、ユーザーには自然文で短く要約する（対応件数、コミット有無、返信件数、未解決が残っていればその内容を明記）。

エージェントの内部処理（LIST/VALID/FIX/COMMIT/PUSH/REPLYの電文）は呼び出し元では展開・再掲しない。最終報告の電文のみを受け取り、それを人間向けに要約すること。トークン節約が目的のため、エージェントの内部ログを先読みしたり二重に確認したりしない。
