# Claude Code カスタムプロンプト 開発・使用ガイド

## 目次

1. [このドキュメントについて](#このドキュメントについて)
2. [セットアップ](#セットアップ)
3. [コマンドの使用方法](#コマンドの使用方法)
4. [プロンプト開発の基礎](#プロンプト開発の基礎)
5. [カスタマイズ方法](#カスタマイズ方法)
6. [トラブルシューティング](#トラブルシューティング)
7. [ベストプラクティス](#ベストプラクティス)
8. [高度な使用例](#高度な使用例)
9. [コントリビューション](#コントリビューション)

## このドキュメントについて

### 対象読者

このドキュメントは以下の方を対象としています：

1. **Claude Code（AIアシスタント）**: プロンプトの構造と設計原則を理解し、適切に動作するため
2. **エンドユーザー**: カスタムコマンドのセットアップと使用方法を理解するため
3. **コントリビューター**: 新規プロンプトの作成や既存プロンプトの改善を行うため

### ドキュメント構成

- **セクション1-3**: エンドユーザー向け（セットアップと基本使用方法）
- **セクション4-5**: プロンプト開発者向け（開発の基礎とカスタマイズ）
- **セクション6-8**: 共通（トラブルシューティング、ベストプラクティス、高度な使用例）
- **セクション9**: コントリビューター向け（貢献方法）

### 関連ドキュメント

- **[README.md](./README.md)**: プロジェクト概要とクイックスタート（ユーザー向け）
- **[CONTRIBUTING.md](./CONTRIBUTING.md)**: 詳細なコントリビューションガイドライン（開発者向け）

---

## セットアップ

このセクションではエンドユーザー向けのインストール方法を説明します。

### 方法1: コマンドディレクトリ全体を一括配置

**プロジェクトレベルのインストール**（推奨）:

```bash
# リポジトリをクローン
git clone https://github.com/IsodaZen/custom-prompts.git

# プロジェクトディレクトリに移動
cd your-project

# commandsディレクトリを.claudeにコピー
cp -r /path/to/custom-prompts/commands .claude/
```

**ユーザーレベルのインストール**（全プロジェクトで利用可能）:

```bash
# commandsディレクトリをユーザーの.claudeにコピー
cp -r /path/to/custom-prompts/commands ~/.claude/
```

### 方法2: 必要なコマンドのみ選択的に配置

```bash
# プロジェクトレベル: セキュリティとパフォーマンスレビューのみ使用する場合
mkdir -p .claude/commands
cp /path/to/custom-prompts/commands/review:security.md .claude/commands/
cp /path/to/custom-prompts/commands/review:perf.md .claude/commands/

# ユーザーレベルの場合
mkdir -p ~/.claude/commands
cp /path/to/custom-prompts/commands/review:security.md ~/.claude/commands/
cp /path/to/custom-prompts/commands/review:perf.md ~/.claude/commands/
```

### ディレクトリ構成確認

**プロジェクトレベル**の場合:

```
your-project/
├── .claude/
│   └── commands/
│       ├── review:security.md
│       ├── review:after.md
│       ├── review:perf.md
│       └── review:prompt.md
├── src/
└── ...
```

**ユーザーレベル**の場合:

```
~/.claude/
└── commands/
    ├── review:security.md
    ├── review:after.md
    ├── review:perf.md
    └── review:prompt.md
```

### 日本語版について

リポジトリ内の `commands-ja/` ディレクトリには各コマンドの日本語版があります。これらは参照用であり、インストールには不要です。

- **英語版** (`commands/`): Claude Codeにインストールして使用
- **日本語版** (`commands-ja/`): コマンドの内容を日本語で確認したい場合に参照

どちらを使用しても、Claudeとの会話は日本語で実施されます。

### プロジェクトレベル vs ユーザーレベル

| | プロジェクトレベル | ユーザーレベル |
|---|---|---|
| **配置場所** | `.claude/commands/` | `~/.claude/commands/` |
| **スコープ** | 特定のプロジェクトのみ | すべてのプロジェクト |
| **チーム共有** | Gitで共有可能 | 個人環境のみ |
| **表示** | `/help` で `(project)` と表示 | `/help` で `(user)` と表示 |
| **推奨用途** | チーム標準、プロジェクト固有 | 個人的なカスタマイズ |

## コマンドの使用方法

### 基本的な実行方法

Claude Codeでコマンドを実行するには、スラッシュ (`/`) に続けてコマンド名を入力します:

```
/review:security
```

### コマンド一覧の確認

インストールされているすべてのコマンドを確認するには:

```
/help
```

プロジェクトレベルのコマンドは `(project)` と表示され、ユーザーレベルのコマンドは `(user)` と表示されます。

### コマンドの対話的な実行

各コマンドは実行時に必要な情報を対話形式で質問します。例:

```
ユーザー: /review:security

Claude: セキュリティレビューを開始します。以下の情報を教えてください:
1. レビュー範囲: どの特定の領域、コンポーネント、またはファイルを分析すべきですか?
2. アプリケーションの種類: (Webアプリ、API、モバイルバックエンドなど)
...
```

### 利用可能なコマンドの詳細

各コマンドの詳細な説明、レビュー観点、推奨使用タイミングについては、[README.md](./README.md) または [README.ja.md](./README.ja.md) を参照してください。

---

## プロンプト開発の基礎

このセクションはプロンプト開発者とAIアシスタント向けです。

### ファイル構造

すべてのコマンドプロンプトは以下の構造に従います：

```markdown
---
name: review:commandname
description: コマンドの1行説明
version: 1.0.0
---

# コマンドタイトル

## 役割
専門家としての役割と専門性を定義

## レビュー目的
レビューが達成すべき目標

## 前提条件
- Claude Codeでの使用を想定
- ユーザーに必要な情報を要求
- 技術スタックの確認方法

## レビュー観点
### 1. 観点名
評価すべき内容の詳細

## 出力形式
**すべてのレビュー出力は日本語で記述してください。**

レビュー結果の構造

## レビュープロセス
1. 前提条件の収集
2. 分析の実施
3. 結果の提供

## 重要な注意事項
重要な考慮事項とガイドライン
```

### フロントマター仕様

**必須フィールド:**
- `name`: コマンド名（形式: `review:something`）
- `description`: 1行の簡潔な説明
- `version`: セマンティックバージョニング（例: 1.0.0）

**オプションフィールド:**
- `allowed-tools`: コマンドが使用できるツールのリスト（例: `[bash, str_replace, view]`）
- `argument-hint`: コマンド引数のヒント（例: `[file] or [directory]`）
- `model`: 特定のモデルを指定（例: `claude-sonnet-4`）

### 設計原則

**1. 汎用性の重視**
- 具体的なチェックリストをハードコードしない
- プロジェクト固有の要件に対応できる柔軟な構造
- 技術スタックを自動判断し、不明な場合はユーザーに確認
- 技術スタックについて推測や仮定をしない

**2. ユーザーインタラクションの設計**
- レビュー開始前に必要な情報を明示的に収集
- 出力形式の選択肢を提供（コンソール出力 or ファイル出力）
- 柔軟なレビュー粒度：
  - コーディング規約が提供される場合: 包括的レビュー
  - 提供されない場合: High/Critical重大度の問題のみに焦点
- 重大度レベルの明確な定義（Critical/High/Medium/Low）

**3. 会話言語の統一**
- **すべてのプロンプトで日本語での会話を強制**
- 英語版プロンプトでも、ユーザーとの会話は日本語で実施
- レビュー出力も日本語で記述
- 前提条件に明記: "このプロンプトのどの言語版を使用している場合でも、ユーザーとの会話はすべて日本語で実施してください。"

**4. 専門レビューへの委譲設計**
- 汎用レビューコマンドから専門的な観点（セキュリティ、パフォーマンス）を分離
- 必要に応じて専用コマンド（`/review:security`, `/review:perf`）を推奨
- 「追加レビューの推奨」セクションで適切に案内

### 重大度レベルの定義

プロンプト内で一貫して使用する重大度レベル：

- **Critical**: データ損失、セキュリティ侵害、システムクラッシュ、機能の完全な停止、認証・認可のバイパスを引き起こす可能性のある問題
- **High**: 機能的な欠陥、著しいパフォーマンス劣化、移行パスのない破壊的変更、コア機能に影響する論理エラー
- **Medium**: 保守性に影響するコード品質の問題、重要なパスのテストカバレッジ不足、複雑なロジックの不完全なドキュメント
- **Low**: コードスタイルの不整合、命名規則の逸脱、軽微なリファクタリングの機会

### Git操作の明示化

変更差分をレビューするコマンド（例: `/review:after`）では、git diff の具体的なコマンド例を提供：

- ブランチ比較: `git diff main...feature-branch`
- コミット比較: `git diff abc123..def456`
- ファイルリスト取得: `git diff --name-only`
- ユーザーにブランチ名/コミットIDの提供を求める

### ファイル命名と配置

**英語版:**
- 配置場所: `commands/`
- 命名規則: `review:commandname.md`
- 例: `commands/review:security.md`

**日本語版:**
- 配置場所: `commands-ja/`
- 命名規則: `review:commandname.ja.md`
- 例: `commands-ja/review:security.ja.md`

---

## カスタマイズ方法

### プロンプトの編集

各プロンプトファイルは標準的なMarkdown形式です。プロジェクト固有の要件に応じてカスタマイズできます。

#### 例: セキュリティレビューにカスタムチェック項目を追加

```markdown
### 12. プロジェクト固有のセキュリティ要件

#### カスタム認証フロー
評価項目:
- 2段階認証の実装確認
- 生体認証連携の安全性
- セッションタイムアウトの適切性（15分）
```

### 重大度レベルのカスタマイズ

プロジェクトの性質に応じて、重大度レベルの定義を調整できます:

```markdown
- **Critical**: 
  - 金融取引に影響する問題
  - 個人情報漏洩のリスク
  - システム全体の停止

- **High**:
  - 主要機能の動作不良
  - 顕著なパフォーマンス劣化
  - セキュリティベストプラクティスからの逸脱
```

### 出力フォーマットのカスタマイズ

レビュー結果の出力形式を変更する場合:

```markdown
## 出力形式

レビュー結果は以下の形式で出力:

### プロジェクト名: [PROJECT_NAME]
### レビュー日時: [TIMESTAMP]
### レビュー担当: [REVIEWER]
### レビュー範囲: [SCOPE]

[既存の出力形式...]
```

## トラブルシューティング

### 問題1: コマンドが認識されない

**原因**: コマンドファイルが正しいディレクトリに配置されていない

**解決策**:
```bash
# ディレクトリ構造を確認
ls -la .claude/commands/

# プロジェクトレベル: ファイルが存在しない場合は再度配置
mkdir -p .claude/commands
cp /path/to/custom-prompts/commands/review:security.md .claude/commands/

# ユーザーレベル: ファイルが存在しない場合は再度配置
mkdir -p ~/.claude/commands
cp /path/to/custom-prompts/commands/review:security.md ~/.claude/commands/

# コマンド一覧を確認
/help
```

### 問題2: 技術スタックが正しく認識されない

**原因**: ワークスペースに技術スタックを示す情報が不足

**解決策**:
- README.mdに技術スタック情報を追加
- package.json、requirements.txt等の設定ファイルを確認
- Claudeの質問に正確に回答

### 問題3: レビュー結果が表面的

**原因**: 
- コーディング規約が提供されていない
- レビュー範囲が広すぎる

**解決策**:
- コーディング規約ドキュメントを用意
- レビュー範囲を具体的に指定（ファイルやディレクトリ単位）
- 複数回に分けてレビューを実施

### 問題4: コマンドファイルを更新したが反映されない

**原因**: 
- Claude Codeが古いバージョンのファイルをキャッシュしている
- ファイルが正しく保存されていない

**解決策**:
```bash
# ファイルの内容を確認
cat .claude/commands/review:security.md

# ファイルのタイムスタンプを確認
ls -l .claude/commands/review:security.md

# Claude Codeを再起動（通常は不要ですが、問題が続く場合）
# コマンドは実行時に読み込まれるため、通常は自動的に反映されます
```

## ベストプラクティス

### 1. 段階的なレビュー実施

一度にすべてを レビューするのではなく、段階的に実施:

1. `/review:after` で全体の変更を確認
2. セキュリティに敏感な変更がある場合は `/review:security`
3. パフォーマンスが重要な機能の場合は `/review:perf`

### 2. コーディング規約の明文化

レビューの品質を高めるために:

- プロジェクト固有のコーディング規約を文書化
- 受け入れ基準を明確に定義
- レビュー時にこれらのドキュメントを参照

### 3. レビュー結果の活用

レビュー結果を効果的に活用:

```bash
# レビュー結果をファイルに保存（Claudeに依頼）
# タイムスタンプ付きファイル名で保存される
# 例: review_security_20250423_143022.md

# レビュー結果をチケット管理システムに連携
# GitHub Issues, Jira等に転記
```

### 4. 定期的なレビュー実施

品質を維持するために定期的にレビュー:

- 週次: 主要な変更に対して `/review:after`
- 月次: コードベース全体に対して `/review:security`
- リリース前: `/review:perf` でパフォーマンス確認

### 5. チーム内での知識共有

レビュー結果を活用してチームの知識を共有:

- レビューで発見された問題をドキュメント化
- ベストプラクティスをWikiやドキュメントに追加
- 定期的にレビュー結果を共有するミーティングを開催

### 6. カスタマイズの継続的改善

プロジェクトの成長に合わせてプロンプトを改善:

- レビューで見落とされた問題を分析
- プロンプトに新しいチェック項目を追加
- チームのフィードバックを反映

### 7. 出力形式の統一

チーム内でレビュー結果の形式を統一:

- ファイル出力を標準化
- 重大度レベルの定義を共有
- レビュー結果のテンプレートを作成

## 高度な使用例

### プロジェクト固有のレビューコマンド作成

既存のコマンドをベースに、プロジェクト固有のカスタムコマンドを作成:

```markdown
---
name: review:fintech
description: 金融システム特化のセキュリティレビュー
version: 1.0.0
---

# 金融システムセキュリティレビュー

## 追加要件
- PCI-DSS準拠の確認
- 金融取引の整合性検証
- 監査ログの完全性
- データ暗号化の強度（AES-256以上）
...
```

このファイルを `.claude/commands/review:fintech.md` として保存すれば、`/review:fintech` コマンドが使えるようになります。

### サブディレクトリでのコマンド整理

チームやカテゴリごとにコマンドを整理:

```
.claude/
└── commands/
    ├── frontend/
    │   ├── component.md       # /component (project:frontend)
    │   └── accessibility.md   # /accessibility (project:frontend)
    └── backend/
        ├── api.md             # /api (project:backend)
        └── database.md        # /database (project:backend)
```

`/help` で表示される際、サブディレクトリ名が `(project:frontend)` のように表示されます。

### CI/CDパイプラインとの統合

レビューを自動化する例:

```yaml
# .github/workflows/security-review.yml
name: Security Review

on:
  pull_request:
    branches: [ main ]

jobs:
  security-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run Security Review
        run: |
          # Claude Code CLIを使用してレビュー実行
          claude-code /review:security > security-review.md
      - name: Upload Review Results
        uses: actions/upload-artifact@v3
        with:
          name: security-review
          path: security-review.md
```

## コントリビューション

このプロジェクトへの貢献に興味がある場合は、[CONTRIBUTING.md](./CONTRIBUTING.md) を参照してください。

### 貢献の種類

- **バグ報告**: GitHub Issuesでバグを報告
- **機能提案**: 新しいコマンドや機能の提案
- **新規コマンド作成**: 新しいレビューコマンドの開発
- **既存コマンド改善**: レビュー観点の追加や出力形式の改善
- **ドキュメント改善**: READMEやガイドの改善

### クイックリンク

- **詳細なガイドライン**: [CONTRIBUTING.md](./CONTRIBUTING.md)
- **開発ワークフロー**: [CONTRIBUTING.md#gitワークフロー](./CONTRIBUTING.md#gitワークフロー)
- **テスト方法**: [CONTRIBUTING.md#テストと検証](./CONTRIBUTING.md#テストと検証)
- **プルリクエストプロセス**: [CONTRIBUTING.md#プルリクエストプロセス](./CONTRIBUTING.md#プルリクエストプロセス)

---

**最終更新**: 2025年10月23日
**バージョン**: 1.0.0
