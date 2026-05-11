# JIRA連携セットアップ手順

このリポジトリはJIRAと連携できます。初回のみ以下の手順を実施してください。

---

## 1. uv をインストールする

`mcp-atlassian` の実行に必要です。ターミナルで以下を実行します。

**Windows（PowerShell）:**
```
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

**Mac / Linux:**
```
curl -LsSf https://astral.sh/uv/install.sh | sh
```

---

## 2. JIRA APIトークンを発行する

1. https://id.atlassian.com/manage-profile/security/api-tokens にアクセス
2. 「APIトークンを作成する」をクリック
3. 名前を入力（例: `claude-code`）してトークンを発行
4. 表示されたトークンをコピーして保存（再表示されません）

---

## 3. 環境変数を設定する

> **セキュリティ注意事項**
> - APIトークンは秘匿情報です。**絶対にファイルに直接書いてGitにコミットしないでください。**
> - `.claude/settings.json` はトークンを変数参照（`${JIRA_API_TOKEN}`）で読み込む設計になっており、トークン自体は含まれていません。
> - トークンはOS の環境変数としてのみ保存してください（以下の手順）。

**Windows（PowerShell・永続設定）:**
```powershell
[System.Environment]::SetEnvironmentVariable("JIRA_URL", "https://yourcompany.atlassian.net", "User")
[System.Environment]::SetEnvironmentVariable("JIRA_USERNAME", "your-email@company.com", "User")
[System.Environment]::SetEnvironmentVariable("JIRA_API_TOKEN", "発行したトークン", "User")
```

**Mac / Linux（~/.zshrc または ~/.bashrc に追記）:**
```
export JIRA_URL="https://yourcompany.atlassian.net"
export JIRA_USERNAME="your-email@company.com"
export JIRA_API_TOKEN="発行したトークン"
```

設定後、ターミナルを再起動してください。

---

## 4. 動作確認

Claude Code でこのリポジトリを開き、以下を話しかけてみてください。

```
「PROJ-1 のチケットのステータスを確認して」
```

JIRAのチケット情報が返ってくれば連携成功です。

---

## JIRA連携でできること

- チケットのステータス確認・更新
- タスク開始時に自動でIn Progressに変更
- タスク完了時に自動でDoneに変更
- plans/ ファイルへのチケット番号の自動記録
- 複数プロジェクトの横断操作（プロジェクトキーを都度指定）

---

## プロジェクトキーの指定方法

話しかける際にJIRAのチケット番号を含めるだけです。

```
「PROJ-123 のWBSを作って」
「OPS-456 のステータスをIn Progressに更新して」
```

対象プロジェクトが決まったら CLAUDE.md の「JIRA連携ルール」に追記してください。
