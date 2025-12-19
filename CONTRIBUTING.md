# コントリビューションガイド

Custom Prompts for Claude Code プロジェクトへの貢献に興味を持っていただき、ありがとうございます。このドキュメントでは、新しいコマンドの作成や既存コマンドの改善に関するガイドラインを提供します。

## 目次

1. [行動規範](#行動規範)
2. [貢献方法](#貢献方法)
3. [開発ガイドライン](#開発ガイドライン)
4. [プルリクエストプロセス](#プルリクエストプロセス)
5. [テストと検証](#テストと検証)

## 行動規範

### 基準

私たちは、歓迎的で包括的な環境を提供することに尽力しています。すべての貢献者には以下を期待します：

- 歓迎的で包括的な言葉遣いを使用する
- 異なる視点や経験を尊重する
- 建設的な批判を優雅に受け入れる
- コミュニティにとって最善なことに焦点を当てる
- 他のコミュニティメンバーに対して共感を示す

### 許容されない行動

- ハラスメント、差別的なコメント、または個人攻撃
- 荒らし行為や侮辱的・軽蔑的なコメント
- 他者の個人情報を許可なく公開すること
- その他、不適切と合理的に判断される行為

## 貢献方法

### バグ報告

コマンドプロンプトにバグを見つけた場合：

1. **既存のIssueを確認**して重複を避ける
2. **新しいIssueを作成**し、以下を含める：
   - 明確で説明的なタイトル
   - コマンド名とバージョン
   - 再現手順
   - 期待される動作と実際の動作
   - 環境情報（Claude Codeのバージョン、OS）
   - 該当する場合は入出力例

### 機能提案

新機能や改善を提案する場合：

1. **既存のIssueを確認**して類似の提案がないか確認
2. **新しいIssueを作成**し、以下を含める：
   - 機能の明確な説明
   - ユースケースとメリット
   - 潜在的な実装アプローチ（任意）
   - 動作例

### 新規コマンドの作成

新しいレビューコマンドを提案する場合：

1. **まずIssueを開いて**以下を議論：
   - コマンドの目的と範囲
   - カバーすべきレビュー観点
   - 既存コマンドとの違い
2. **フィードバックを待つ**（実装開始前）
3. **開発ガイドライン**に従う

### 既存コマンドの改善

既存コマンドを改善する場合：

1. **改善内容を特定**：
   - 不足しているレビュー観点
   - より良い出力形式
   - ユーザーインタラクションの改善
   - バグ修正
2. **改善内容を説明するIssueを作成**
3. 可能な限り**後方互換性を維持**

## 開発ガイドライン

### コマンドファイルの構造

すべてのコマンドは以下の構造に従う必要があります：

```markdown
---
name: review:commandname
description: コマンドの1行説明
version: 1.0.0
---

# コマンドタイトル

## 役割
役割と専門性を定義

## レビュー目的
レビューが達成を目指すこと

## 前提条件
- 必要な要件とセットアップ
- ユーザーに情報を要求

## レビュー観点
### 1. 観点名
評価すべき内容

### 2. 別の観点
評価すべき内容

## 出力形式
**すべてのレビュー出力は日本語で記述してください。**

レビュー出力の構造

## レビュープロセス
ステップバイステップのプロセス

## 重要な注意事項
重要な考慮事項
```

### フロントマターの仕様

**必須フィールド:**
- `name`: コマンド名（形式: `review:something`）
- `description`: 1行の説明
- `version`: セマンティックバージョニング（1.0.0）

**オプションフィールド:**
- `allowed-tools`: コマンドが使用できるツールのリスト
- `argument-hint`: コマンド引数のヒント
- `model`: 使用する特定のモデル

### 設計原則

**1. 汎用性重視**
- 具体的なチェックリストをハードコードしない
- カスタマイズ可能な柔軟な構造
- 技術スタックを自動判断し、不明な場合はユーザーに確認
- 技術スタックについて推測や仮定をしない

**2. ユーザー中心設計**
- レビュー開始前に必要な情報を収集
- 出力形式の選択肢を提供（コンソール/ファイル）
- 重大度レベルを明確に定義（Critical/High/Medium/Low）

**3. 言語の一貫性**
- ユーザーとの会話はすべて日本語で実施
- これはプロンプトのどの言語版を使用している場合でも適用
- 前提条件に含める：「このプロンプトのどの言語版を使用している場合でも、ユーザーとの会話はすべて日本語で実施してください。」

**4. 専門性の分離**
- 特定のレビュー観点に焦点を当てる
- 適切な場合は専門コマンドを推奨

### ファイル命名と配置

**英語版:**
- 配置場所: `commands/`
- 命名規則: `review:commandname.md`
- 例: `commands/review:security.md`

**日本語版:**
- 配置場所: `commands-ja/`
- 命名規則: `review:commandname.ja.md`
- 例: `commands-ja/review:security.ja.md`

### 重大度レベルの定義

常にこれらの定義を使用してください：

- **Critical**: システム全体の侵害、データ侵害、またはリモートコード実行
- **High**: 不正アクセス、特権昇格、または重大な影響
- **Medium**: 範囲が限定的、または特定条件を必要とする
- **Low**: 軽微な問題または多層防御の改善
- **Info**: 即座のリスクのない観察事項（セキュリティのみ）

### Gitワークフロー

**ブランチ命名規則:**
- 機能追加: `feature/command-name` または `feature/description`
- バグ修正: `fix/issue-description`
- ドキュメント: `docs/description`

**例:**
```bash
feature/review-deps
fix/security-validation
docs/update-readme
```

**コミットメッセージ規則:**

形式: `<type>: <subject>`

**Type:**
- `feat`: 新機能（新しいコマンド）
- `fix`: バグ修正
- `docs`: ドキュメントのみの変更
- `style`: フォーマット、セミコロン忘れなど
- `refactor`: リファクタリング
- `test`: テストの追加・修正
- `chore`: ビルドプロセスやツールの変更

**例:**
```
feat: add review:deps command for dependency analysis
fix: correct severity level definitions in review:security
docs: update CLAUDE.md with new command structure
```

## プルリクエストプロセス

### 1. 準備

1. **リポジトリをフォーク**
2. **フィーチャーブランチを作成**
   ```bash
   git checkout -b feature/your-feature-name
   ```
3. **変更を実装**

### 2. 品質チェック

実装後、以下を確認：

- [ ] フロントマターがすべて正しく設定されている
- [ ] 日本語版と英語版の両方が存在する
- [ ] 設計原則に従っている
- [ ] 重大度レベルの定義が一貫している
- [ ] ファイル名が規則に従っている（`review:name.md`）
- [ ] 適切なディレクトリに配置されている

### 3. セルフレビュー

可能であれば、`/review:prompt` コマンドで自己レビューを実施：

```bash
# commands/ディレクトリに移動
cd commands

# 新しいコマンドをレビュー
/review:prompt review:your-command.md
```

### 4. ドキュメント更新

必要に応じて以下を更新：

- [ ] `README.md` - 新しいコマンドを一覧に追加
- [ ] `README.ja.md` - 同上（日本語版）
- [ ] `CLAUDE.md` - 必要に応じて使用例を追加

### 5. コミットとプッシュ

```bash
# 変更をステージング
git add .

# コミット（規則に従ったメッセージ）
git commit -m "feat: add review:example command"

# プッシュ
git push origin feature/your-feature-name
```

### 6. プルリクエスト作成

1. **GitHubでPRを作成**
2. **PRテンプレートに従って記入**：
   - 変更の説明
   - 関連するIssue番号
   - 実施したテスト
   - スクリーンショット（該当する場合）

### 7. レビュー対応

- メンテナーからのフィードバックに対応
- 必要に応じて変更を追加コミット
- レビューが承認されたらマージ

## テストと検証

### ローカルテスト

新しいコマンドや変更をテストする方法：

**1. ローカル環境にインストール**

```bash
# テスト用プロジェクトディレクトリを作成
mkdir test-project
cd test-project

# .claude/commandsディレクトリを作成
mkdir -p .claude/commands

# 開発中のコマンドをコピー
cp /path/to/custom-prompts/commands/review:your-command.md .claude/commands/
```

**2. Claude Codeでテスト**

```bash
# Claude Codeを起動
# コマンドが認識されているか確認
/help

# コマンドを実行
/review:your-command
```

**3. 異なるシナリオでテスト**

- 最小限の情報しか提供しない場合
- 技術スタックが不明な場合
- エッジケース（大規模プロジェクト、複雑な構造など）

### 検証チェックリスト

実装したコマンドが以下を満たしているか確認：

- [ ] コマンドが正しく認識される（`/help`に表示）
- [ ] ユーザーとの対話が日本語で実施される
- [ ] 必要な情報を適切に収集する
- [ ] 出力が構造化されている
- [ ] 重大度レベルが適切に使用されている
- [ ] エラーハンドリングが適切
- [ ] ドキュメントが明確で理解しやすい

### セルフレビューガイド

`/review:prompt` を使用してセルフレビューする際の観点：

1. **明確性と具体性**
   - タスクと期待される出力が明確か
   - 曖昧さはないか

2. **構造と整理**
   - 論理的な流れになっているか
   - セクションが適切に分割されているか

3. **指示の品質**
   - ステップバイステップのガイダンスがあるか
   - エッジケースの処理が含まれているか

4. **安全性と堅牢性**
   - プロンプトインジェクションのリスクはないか
   - 適切な検証が含まれているか

## 高度な使用例

### プロジェクト固有のレビューコマンド作成

既存のコマンドをベースに、プロジェクト固有のカスタムコマンドを作成できます：

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
- 二要素認証の実装
...
```

このファイルを `.claude/commands/review:fintech.md` として保存すれば、`/review:fintech` コマンドが使えるようになります。

### サブディレクトリでのコマンド整理

チームやカテゴリごとにコマンドを整理することも可能です：

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

レビューを自動化する例：

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

**注意**: CI/CD統合は、Claude Code CLIの将来的な機能として想定されています。現時点では手動でのレビュー実行を推奨します。

## カスタマイズ例

### プロンプトの編集

各プロンプトファイルは標準的なMarkdown形式です。プロジェクト固有の要件に応じてカスタマイズできます。

#### 例1: セキュリティレビューにカスタムチェック項目を追加

```markdown
### 12. プロジェクト固有のセキュリティ要件

#### カスタム認証フロー
評価項目:
- 2段階認証の実装確認
- 生体認証連携の安全性
- セッションタイムアウトの適切性（15分）
- ログアウト時のトークン無効化
```

#### 例2: 重大度レベルのカスタマイズ

プロジェクトの性質に応じて、重大度レベルの定義を調整できます：

```markdown
## 重大度レベル定義（金融システム向け）

- **Critical**:
  - 金融取引に影響する問題
  - 個人情報漏洩のリスク
  - システム全体の停止
  - 規制違反（PCI-DSS、GDPR等）

- **High**:
  - 主要機能の動作不良
  - 顕著なパフォーマンス劣化
  - セキュリティベストプラクティスからの逸脱
  - 監査ログの不完全性
```

#### 例3: 出力フォーマットのカスタマイズ

レビュー結果の出力形式を変更する場合：

```markdown
## 出力形式

レビュー結果は以下の形式で出力:

### プロジェクト名: [PROJECT_NAME]
### レビュー日時: [TIMESTAMP]
### レビュー担当: [REVIEWER]
### レビュー範囲: [SCOPE]
### コンプライアンス基準: [STANDARDS]

[既存の出力形式...]
```

### プロジェクトテンプレートの作成

チーム全体で使用するコマンドセットをテンプレート化できます：

```bash
# プロジェクトテンプレートの作成
mkdir -p project-templates/web-app/.claude/commands

# 必要なコマンドをコピー
cp commands/review:security.md project-templates/web-app/.claude/commands/
cp commands/review:after.md project-templates/web-app/.claude/commands/
cp commands/review:perf.md project-templates/web-app/.claude/commands/

# カスタマイズしたコマンドを追加
cat > project-templates/web-app/.claude/commands/review:accessibility.md <<EOF
# アクセシビリティレビュー用カスタムコマンド
...
EOF

# 新規プロジェクトで使用
cp -r project-templates/web-app/.claude new-project/
```

## 追加リソース

### 参考資料

- [Claude Code公式ドキュメント](https://docs.claude.com/ja/docs/claude-code/slash-commands)
- [CLAUDE.md](./CLAUDE.md) - 詳細な開発ガイド
- [README.md](./README.md) - プロジェクト概要

### サポート

質問がある場合：

1. **GitHub Discussions** で質問を投稿
2. **GitHub Issues** でバグや提案を報告
3. **既存のドキュメント** を確認

### コミュニティ

- GitHub Discussions: プロジェクトについての議論
- GitHub Issues: バグ報告や機能提案

## ライセンス

このプロジェクトに貢献することで、あなたの貢献がMITライセンスの下でライセンスされることに同意したものとみなされます。

---

**ありがとうございます！**

あなたの貢献がこのプロジェクトをより良いものにします。質問や提案があれば、遠慮なくIssueを開いてください。
