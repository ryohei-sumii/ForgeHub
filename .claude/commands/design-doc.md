---
description: ForgeHub の詳細設計ドキュメントを作成/更新する（Opusで計画→Sonnetで執筆の2段パイプライン）
argument-hint: [対象スコープ（例: F-03 API管理、DB設計、認証フロー）省略時はrequirements.md全体]
---

ForgeHub の設計ドキュメントを作成/更新する。対象スコープ: $ARGUMENTS （省略時は `docs/requirements.md` 全体から優先度順に対象を選ぶ）。

手順:

1. `design-doc-planner` サブエージェント（Agent tool, subagent_type=design-doc-planner）を起動し、上記スコープを渡して電文形式のプランを取得する。foreground で実行し、結果を待つ。
2. プランナーの出力（電文）をそのまま `design-doc-writer` サブエージェント（Agent tool, subagent_type=design-doc-writer）への入力として渡し、設計文書を書かせる。
3. writer から返る完了報告（`FILE=` / `SECT=` / `OPEN=`）を受け取り、ユーザーには短く要約して報告する（書いたファイルパス、章立て、未決事項があれば明記）。

エージェント間で受け渡すコンテキスト（プランナー→ライター、ライター→呼び出し元）は電文形式のまま転送し、自然文に展開し直さない。トークン節約が目的のため、オーケストレーター自身もプランを要約・言い換えせずそのまま次のエージェントに渡すこと。
