---
description: ForgeHub の詳細設計ドキュメントを作成/更新する（Opusで計画→Sonnetで執筆の2段パイプライン）
argument-hint: [対象スコープ（例: F-03 API管理、DB設計、認証フロー）省略時はrequirements.md全体]
---

ForgeHub の設計ドキュメントを作成/更新する。対象スコープ: $ARGUMENTS （省略時は `docs/requirements.md` 全体から優先度順に対象を選ぶ）。

手順:

1. `design-doc-planner` サブエージェント（Agent tool, subagent_type=design-doc-planner）を起動し、上記スコープを渡して電文形式のプランを取得する。foreground で実行し、結果を待つ。
2. プランナーの出力（電文）をそのまま `design-doc-writer` サブエージェント（Agent tool, subagent_type=design-doc-writer）への入力として渡し、設計文書を書かせる。
3. writer から返る完了報告（`FILE=` / `SECT=` / `OPEN=`）を受け取る。
4. `FILE=` のパスをコミットし、専用ブランチにpushしてPRを作成する（ユーザーから別ブランチ名の指示が無ければ `claude/forgehub-<スコープのスラッグ>-design` とする）。
5. PR作成直後に `design-doc-reviewer` サブエージェント（Agent tool, subagent_type=design-doc-reviewer）を自動起動し、作成したPR番号を渡す。foreground で実行し結果を待つ。
6. writerの完了報告とreviewerの完了報告（`FINDINGS=` / `EVENT=` / `OPEN=`）を、ユーザーには短く要約して報告する（書いたファイルパス、章立て、PRリンク、レビュー結果、未決事項があれば明記）。

エージェント間で受け渡すコンテキスト（プランナー→ライター、ライター→呼び出し元）は電文形式のまま転送し、自然文に展開し直さない。トークン節約が目的のため、オーケストレーター自身もプランを要約・言い換えせずそのまま次のエージェントに渡すこと。

## スコープが広い場合の分割（中間忘却対策）

対象が `docs/requirements.md` 全体、または複数機能（F-01〜F-05等）にまたがる場合、1回の planner/writer 呼び出しに全機能を詰め込まない。機能単位（F-01, F-02, ... の優先度順）で「planner起動 → writer起動 → 完了報告」を機能ごとに直列で繰り返すこと。1回のコンテキストに情報を詰め込みすぎると、途中の機能の決定事項がplanner/writer双方で読み落とされる（位置バイアス・中間忘却）ため、機能ごとに区切って精度を優先する。全機能分が終わったら、書いたファイル一覧（`FILE=`の一覧）をまとめてユーザーに報告する。
