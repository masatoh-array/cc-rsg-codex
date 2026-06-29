# cc-rsg for Codex

`cc-rsg` の Codex 向け配置です。

既存の本家スキルは `skills/cc-rsg/` に残し、Codex 向けに調整したコピーを `skills-for-codex/cc-rsg/` に置いています。元のサブエージェント定義に対応する Codex custom agent は `skills-for-codex/.codex/agents/` に置いています。利用先リポジトリへコピーまたは symlink して使います。

---

## インストール

### プロジェクトのスキルとして配置する場合

利用先リポジトリのルートで、以下を実行します。

```powershell
New-Item -ItemType Directory -Force -Path .agents/skills | Out-Null
New-Item -ItemType Directory -Force -Path .codex/agents | Out-Null
$Source = "<cc-rsg-codex-root>\skills-for-codex\cc-rsg"
Copy-Item -Recurse $Source .agents\skills\cc-rsg
Copy-Item "<cc-rsg-codex-root>\skills-for-codex\.codex\agents\*.toml" .codex\agents\
```

### ユーザーレベルのスキルとして配置する場合

同じ `cc-rsg` フォルダを `$HOME/.agents/skills/` 配下に置き、`skills-for-codex/.codex/agents/*.toml` を `$HOME/.codex/agents/` 配下に置きます。`$HOME/.codex/agents/` がなければ作成します。

### 動作確認

Codex を起動し、`/skills` で `cc-rsg` を確認します。明示的に呼び出す場合は `$cc-rsg` を使います。

このリポジトリ直下には `.agents/skills/` を作っていません。Codex 向けの配置元は以下です。

```text
skills-for-codex/cc-rsg/
skills-for-codex/.codex/agents/
```

---

## 使い方

### 基本フロー

```
1. 利用先コードベースのルートで Codex を起動
2. $cc-rsg を呼び出す
3. ゴール定義5問に回答(Phase 0)
4. 偵察結果を確認しテンプレート選定(Phase 1)
5. WBS とインベントリをレビュー(Phase 2)
6. 必要に応じてサブエージェント調査を使う(Phase 3)
7. 検証レポートを確認(Phase 4)
8. Question Bank の対話で仕様を精緻化(Phase 5)
9. 最終成果物を受け取る(Phase 6)
```

### 出力場所

生成物は利用先リポジトリ直下の `.cc-rsg/` に保存されます。この作業ディレクトリ名は本家版と同じです。

---

## skills-for-codex 配下の使いどころ

| 種類 | ファイル | 使うシチュエーション |
|------|----------|----------------------|
| Codex スキル本体 | `SKILL.md` | 仕様書逆生成を始める入口。Phase 0〜6 の進行、成果物、検証条件を定義する |
| Codex UI メタデータ | `agents/openai.yaml` | Codex 上の表示名、説明、既定プロンプトを定義する |
| Codex custom agent | `.codex/agents/chapter-investigator.toml` | Phase 3 で章単位の独立調査と章ドラフト作成を任せる場合 |
| Codex custom agent | `.codex/agents/chapter-investigator-mode-b.toml` | Context Optimization mode B で章本文を返さず path / summary / manifest だけ返す場合 |
| サブエージェント参照 | `agents/chapter-investigator.md` | `chapter-investigator` custom agent の skill-local source prompt として使う |
| テンプレート | `templates/web-app.md` | Webアプリケーション仕様書を生成する場合 |
| テンプレート | `templates/batch-system.md` | バッチ処理・スケジュール実行・ジョブ中心の仕様書を生成する場合 |
| テンプレート | `templates/api-service.md` | API サービス仕様書を生成する場合 |
| テンプレート | `templates/library-sdk.md` | ライブラリ / SDK の仕様書を生成する場合 |
| 参照資料 | `references/inventory-units.md` | 対象コードベースから何を全列挙するかを決める場合 |
| 参照資料 | `references/outline-tables.md` | `outline` / `interactive` モードで概観テーブルを作る場合 |
| 参照資料 | `references/template-catalog.md` | Phase 1 でテンプレート候補を選ぶ場合 |
| 参照資料 | `references/question-categories.md` | Question Bank のカテゴリと深刻度を分類する場合 |
| 参照資料 | `references/verification-checklists.md` | Phase 4 の検証観点を確認する場合 |
| 参照資料 | `references/subagent-prompt.md` | サブエージェントに渡す章単位プロンプトを組み立てる場合 |
| 実行スクリプト | `scripts/source-map.py` | Phase 2 でソースユニット一覧を `.cc-rsg/source-map.json` に出力する場合 |
| 実行スクリプト | `scripts/build-trace.py` | `[REF: path:Lstart-Lend]` を `.cc-rsg/trace.json` に集約する場合 |
| 実行スクリプト | `scripts/build-traceability.py` | `trace.json` から人間向けの `traceability.md` を生成する場合 |
| 実行スクリプト | `scripts/coverage-check.py` | Phase 4 でカバレッジ、章品質、Question Bank、MECE を検証する場合 |
| Context Optimization mode B | `variants/B/README.md` | mode B の使いどころと活性化方法を確認する場合 |
| Context Optimization mode B | `variants/B/SKILL.phase3-stepG.md` | Phase 3 STEP G を mode B の manifest relay 方式に差し替える場合 |
| Context Optimization mode B | `variants/B/chapter-investigator.md` | `chapter-investigator-mode-b` custom agent の skill-local source prompt として使う場合 |

### 実行スクリプトの確認コマンド

```powershell
python skills-for-codex\cc-rsg\scripts\source-map.py --help
python skills-for-codex\cc-rsg\scripts\build-trace.py --help
python skills-for-codex\cc-rsg\scripts\build-traceability.py --help
python skills-for-codex\cc-rsg\scripts\coverage-check.py --help
```

---

## 本家ファイルとの対応表

本家 `skills/cc-rsg/` に変更が入った場合は、以下の対応表を見て `skills-for-codex/cc-rsg/` 側も更新します。

| 本家ファイル | Codex 向けファイル | 更新時の見方 |
|--------------|--------------------|--------------|
| `skills/cc-rsg/SKILL.md` | `skills-for-codex/cc-rsg/SKILL.md` | Codex 用語・配置・サブエージェント表現を維持して反映する |
| `skills/cc-rsg/agents/chapter-investigator.md` | `skills-for-codex/cc-rsg/agents/chapter-investigator.md` + `skills-for-codex/.codex/agents/chapter-investigator.toml` | Markdown は source prompt、TOML は Codex custom agent 定義として扱う |
| `skills/cc-rsg/references/inventory-units.md` | `skills-for-codex/cc-rsg/references/inventory-units.md` | 抽出単位の追加・修正を反映する |
| `skills/cc-rsg/references/outline-tables.md` | `skills-for-codex/cc-rsg/references/outline-tables.md` | outline 表定義の追加・修正を反映する |
| `skills/cc-rsg/references/question-categories.md` | `skills-for-codex/cc-rsg/references/question-categories.md` | Question Bank 分類の追加・修正を反映する |
| `skills/cc-rsg/references/subagent-prompt.md` | `skills-for-codex/cc-rsg/references/subagent-prompt.md` | Codex subagent 向け表現を維持して反映する |
| `skills/cc-rsg/references/template-catalog.md` | `skills-for-codex/cc-rsg/references/template-catalog.md` | テンプレート選定ロジックの変更を反映する |
| `skills/cc-rsg/references/verification-checklists.md` | `skills-for-codex/cc-rsg/references/verification-checklists.md` | Phase 4 検証観点の変更を反映する |
| `skills/cc-rsg/scripts/source-map.py` | `skills-for-codex/cc-rsg/scripts/source-map.py` | 原則そのまま同期する |
| `skills/cc-rsg/scripts/build-trace.py` | `skills-for-codex/cc-rsg/scripts/build-trace.py` | 原則そのまま同期する |
| `skills/cc-rsg/scripts/build-traceability.py` | `skills-for-codex/cc-rsg/scripts/build-traceability.py` | 原則そのまま同期する |
| `skills/cc-rsg/scripts/coverage-check.py` | `skills-for-codex/cc-rsg/scripts/coverage-check.py` | 原則そのまま同期する |
| `skills/cc-rsg/templates/web-app.md` | `skills-for-codex/cc-rsg/templates/web-app.md` | テンプレート変更を反映する |
| `skills/cc-rsg/templates/batch-system.md` | `skills-for-codex/cc-rsg/templates/batch-system.md` | テンプレート変更を反映する |
| `skills/cc-rsg/templates/api-service.md` | `skills-for-codex/cc-rsg/templates/api-service.md` | テンプレート変更を反映する |
| `skills/cc-rsg/templates/library-sdk.md` | `skills-for-codex/cc-rsg/templates/library-sdk.md` | テンプレート変更を反映する |
| `skills/cc-rsg/variants/B/README.md` | `skills-for-codex/cc-rsg/variants/B/README.md` | mode B の説明変更を Codex 表現で反映する |
| `skills/cc-rsg/variants/B/SKILL.phase3-stepG.md` | `skills-for-codex/cc-rsg/variants/B/SKILL.phase3-stepG.md` | mode B の STEP G 変更を Codex subagent 表現で反映する |
| `skills/cc-rsg/variants/B/chapter-investigator.md` | `skills-for-codex/cc-rsg/variants/B/chapter-investigator.md` + `skills-for-codex/.codex/agents/chapter-investigator-mode-b.toml` | Markdown は source prompt、TOML は mode B 用 Codex custom agent 定義として扱う |

### Codex 専用追加ファイル

| ファイル | 役割 |
|----------|------|
| `skills-for-codex/cc-rsg/agents/openai.yaml` | Codex 上の UI メタデータ。対応する本家ファイルはない |
| `skills-for-codex/.codex/agents/chapter-investigator.toml` | Codex custom agent 定義。元の `agents/chapter-investigator.md` のサブエージェント定義意図を Codex に移したもの |
| `skills-for-codex/.codex/agents/chapter-investigator-mode-b.toml` | mode B 用 Codex custom agent 定義。元の `variants/B/chapter-investigator.md` のサブエージェント定義意図を Codex に移したもの |

---

## 注意点

- `skills-for-codex/` は Codex 向けの配置元であり、このリポジトリ直下で自動検出される root `.agents/skills/` ではありません。
- `.cc-rsg/` は利用先リポジトリに作られる作業ディレクトリです。
- Plugin 化と MCP 統合はこの Codex 移行の対象外です。
