# CLAUDE.md — AI Business OS Core v1.0
> **全エージェントが最初に読む唯一のファイル。更新権限: 人間のみ。**
> 最終更新: 2026-05-11

---

## Mission

AIエージェントを活用して業務を遂行するための標準基盤。
営業企画・業務設計・PdM など、職種を問わず応用できる汎用テンプレート。
**このリポジトリを fork し、自分の職種・業務に合わせてカスタマイズして使うこと。**

---

## ファイル読み込み順序（省略禁止・毎セッション必須）

```
CLAUDE.md（今ここ）→ AGENTS_COMMON.md → agents/{ロール}/.clauderules
```

| ロール | 読む順序 |
|---|---|
| Researcher | CLAUDE.md → AGENTS_COMMON.md → agents/_researcher/.clauderules |
| Writer | CLAUDE.md → AGENTS_COMMON.md → agents/_writer/.clauderules |
| Reviewer | CLAUDE.md → AGENTS_COMMON.md → agents/_reviewer/.clauderules |
| ロール固有 | CLAUDE.md → AGENTS_COMMON.md → agents/[ロール名]/.clauderules |

**自分のロール以外の agents/*/ は読まない（コンテキスト汚染を防ぐ）。**

---

## 絶対制約（違反禁止）

1. **計画先行**: 何の作業も `plans/YYYY-MM-DD-{タスク概要}.md` の作成なしに開始してはならない。
2. **機密情報ゼロトレランス**: 個人情報・社外秘・未公開情報を `outputs/` にそのまま書かない。仮名・匿名化を徹底する。
3. **事実確認義務**: AI が生成した数値・固有名詞・統計は必ず人間が確認してから使用する。鵜呑みにしない。
4. **人間承認**: 外部への送信・配布・公開前は必ず人間が内容を確認・承認する。
5. **目的明確化**: タスク開始時に「誰のために・何を・なぜ達成するか」を一文で明文化する。
6. **scratchpad 使用**: 試行錯誤・下書きは必ず `scratchpad/` に書く。`outputs/` には完成物のみ置く。

---

## エージェント構成

### システムエージェント（全ロール共通・常時使用）

```
agents/_researcher/   調査・情報収集・競合分析
agents/_writer/       文書作成・編集・構成
agents/_reviewer/     品質レビュー・フィードバック
```

### ロールエージェント（職種別・必要なものだけ使う）

```
agents/sales-planning/    営業企画向け
agents/business-design/   業務設計向け
agents/pdm/               PdM向け
```

> **カスタマイズ方法**: 不要なロールは削除し、自分の職種に合った新しいロールを追加する。追加方法は `HOW_TO_ADD_ROLE.md` を参照。

---

## 品質ゲート（成果物のリリース判定基準）

以下をすべて満たさない限り `outputs/` に移動しない。

| 観点 | 基準 |
|---|---|
| 目的明確 | 誰のために・何のための文書か一文で言える |
| 想定読者 | 読み手が特定されており、言葉のレベルが合っている |
| 事実ベース | AI 生成の数値・固有名詞を人間が確認済み |
| アクション明確 | 読んだ後に何をすべきか分かる |
| レビュー済み | Reviewer エージェントまたは人間がレビュー済み |
| 機密チェック | 個人情報・社外秘が含まれていないか確認済み |

---

## 失敗パターン早見表

詳細: `memories/lessons_learned/`

| カテゴリ | パターン | 対策 |
|---|---|---|
| 計画 | タスクの目的が曖昧なまま作業開始 → 方向性がずれた成果物 | 開始前に「誰のために・何を・なぜ」を一文で書く |
| 計画 | plans/ に保存せず口頭・メモリだけで進める → 引き継ぎ不能 | 必ず `plans/YYYY-MM-DD-{概要}.md` をファイルとして書き出す |
| 品質 | AI 生成の数値をそのまま資料に使う → 誤情報の拡散 | 数値・統計・固有名詞は必ず出典を確認してから使用 |
| 品質 | レビューなしで外部配布 → 誤字・論理矛盾の流出 | Reviewer エージェントを必ず通してから配布 |
| outputs | 下書きと完成物が混在 → どれが最新か分からなくなる | 下書きは scratchpad/、完成物のみ outputs/ |
| 機密 | 実名・社外秘データをそのままプロンプトに貼る → 情報漏洩リスク | 仮名・匿名化してからエージェントに渡す |

---

## 優先順位

**正確性 > 機密保護 > 読みやすさ > 速さ**

---

## ディレクトリ読み込みルール

| タスク種別 | 追加で読む場所 |
|---|---|
| 過去の失敗確認 | `memories/lessons_learned/` |
| 過去の成果物参照 | `outputs/` |
| テンプレート活用 | `templates/` |
| モデル選択の相談 | `SKILL_MODEL_MAP.json` |
| タスク状況確認 | `feature_list.json` / `results/` |

---

## タスク開始プロトコル（毎回必須・省略禁止）

1. タスクの目的を一文で明文化する（「誰のために・何を・なぜ」）
2. `feature_list.json` で対象タスクのステータスを確認
3. `memories/lessons_learned/` で関連する過去の失敗を確認
4. `plans/YYYY-MM-DD-{タスク概要}.md` をファイルとして作成・保存
5. 機密情報を含む場合は匿名化・仮名化を先に行う

---

## タスク完了プロトコル（毎回必須・省略禁止）

1. 品質ゲートを全項確認
2. 完成物を `outputs/` に移動（下書きは `scratchpad/` から削除）
3. `results/{タスクID}-complete.json` を作成（`used_model` フィールドに使用モデルIDを記録）
4. `memories/lessons_learned/YYYY-MM-DD-{タスク名}.md` を作成
5. `feature_list.json` の当該タスクの status を COMPLETE に更新
6. 完了した plans を `memories/summaries/` にアーカイブ
7. セッション中に人間が確認した操作を `memories/confirmation_history.md` に記録
8. `memories/lessons_learned/` の未蒸留ファイルが 5件以上なら蒸留タスクをスケジュール

---

## lessons_learned 蒸留サイクル

**蒸留タスクを起動する条件（OR・どちらか早い方）:**

| 条件 | 説明 |
|---|---|
| A. 未蒸留ファイルが **5件** に達した | 活発な運用期に自動発火 |
| B. 前回の蒸留から **30日** 経過 | 低活動期のフォールバック |

**実行手順:**
1. 未蒸留ファイルを精査
2. 「失敗パターン早見表」に一般化・統合
3. 蒸留済みファイルを `memories/summaries/distilled/` へ移動
4. 蒸留完了日時を `memories/confirmation_history.md` に記録

> **現在の蒸留状態**: 未蒸留ファイル 0件（初期状態）

---

## メタデータ

- バージョン: v1.0
- 最終更新: 2026-05-11
- テンプレート元: https://github.com/PIROSHKI1287/Tessid
