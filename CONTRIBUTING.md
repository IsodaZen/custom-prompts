# コントリビューションガイド

Custom Prompts for Claude Code プロジェクトへの貢献に興味を持っていただき、ありがとうございます。このドキュメントでは、新しいスキルの作成や既存スキルの改善に関するガイドラインを提供します。

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

スキルのプロンプトにバグを見つけた場合：

1. **既存のIssueを確認**して重複を避ける
2. **新しいIssueを作成**し、以下を含める：
   - 明確で説明的なタイトル
   - スキル名とバージョン
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

### 新規スキルの作成

新しいレビュースキルを提案する場合：

1. **まずIssueを開いて**以下を議論：
   - スキルの目的と範囲
   - カバーすべきレビュー観点
   - 既存スキルとの違い
2. **フィードバックを待つ**（実装開始前）
3. **開発ガイドライン**に従う

### 既存スキルの改善

既存スキルを改善する場合：

1. **改善内容を特定**：
   - 不足しているレビュー観点
   - より良い出力形式
   - ユーザーインタラクションの改善
   - バグ修正
2. **改善内容を説明するIssueを作成**
3. 可能な限り**後方互換性を維持**

## 開発ガイドライン

### スキルファイルの構造

すべてのスキルは `skills/<skill-name>/SKILL.md` に配置し、以下の構造に従う必要があります：

```markdown
---
name: skill-name
description: スキルの1行説明
license: MIT
metadata:
  author: your-name
  version: "1.0.0"
---

# スキルタイトル

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
- `name`: スキル名（ディレクトリ名と一致させる。例: `review-something`）
- `description`: 1行の説明。Claudeが会話内容から自動的にスキルをトリガーする際の判断材料になるため、目的とユースケースが伝わる説明にする

**オプションフィールド:**
- `license`: ライセンス識別子（本プロジェクトでは `MIT`）
- `metadata.author`: 作成者名
- `metadata.version`: セマンティックバージョニング（例: `1.0.0`）
- `compatibility`: 特定のツールやCLIへの依存がある場合の注記

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
- これはスキルのどの言語版ガイドを参照している場合でも適用
- 前提条件に含める：「このプロンプトのどの言語版を使用している場合でも、ユーザーとの会話はすべて日本語で実施してください。」

**4. 専門性の分離**
- 特定のレビュー観点に焦点を当てる
- 適切な場合は専門スキルを推奨

### ファイル命名と配置

- 配置場所: `skills/<skill-name>/SKILL.md`
- ディレクトリ名とフロントマターの `name` は一致させる（例: `skills/review-security/SKILL.md` の `name: review-security`）
- 日本語話者向けの内容説明は、個別の `-ja` ディレクトリを作らず [README.ja.md](./README.ja.md) にまとめる。スキル自身は実行時に常に日本語で会話するため、言語別のスキル本体を複製する必要はない

### 重大度レベルの定義

常にこれらの定義を使用してください：

- **Critical**: システム全体の侵害、データ侵害、またはリモートコード実行
- **High**: 不正アクセス、特権昇格、または重大な影響
- **Medium**: 範囲が限定的、または特定条件を必要とする
- **Low**: 軽微な問題または多層防御の改善
- **Info**: 即座のリスクのない観察事項（セキュリティのみ）

### Gitワークフロー

**ブランチ命名規則:**
- 機能追加: `feature/skill-name` または `feature/description`
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
- `feat`: 新機能（新しいスキル）
- `fix`: バグ修正
- `docs`: ドキュメントのみの変更
- `style`: フォーマット、セミコロン忘れなど
- `refactor`: リファクタリング
- `test`: テストの追加・修正
- `chore`: ビルドプロセスやツールの変更

**例:**
```
feat: add review-deps skill for dependency analysis
fix: correct severity level definitions in review-security
docs: update CLAUDE.md with new skill structure
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

- [ ] フロントマターがすべて正しく設定されている（`name` がディレクトリ名と一致している）
- [ ] 設計原則に従っている
- [ ] 重大度レベルの定義が一貫している
- [ ] ディレクトリ構成が規則に従っている（`skills/skill-name/SKILL.md`）

### 3. セルフレビュー

可能であれば、`review-prompt` スキルで自己レビューを実施：

```bash
# 新しいスキルファイルをレビュー
# Claude Codeで次のように依頼する:
review-prompt スキルで skills/your-skill-name/SKILL.md をレビューして
```

### 4. ドキュメント更新

必要に応じて以下を更新：

- [ ] `README.md` - 新しいスキルを一覧に追加
- [ ] `README.ja.md` - 同上（日本語版）
- [ ] `CLAUDE.md` - 必要に応じて使用例を追加
- [ ] `.claude-plugin/plugin.json` - `skills` 配列に新しいスキルディレクトリを追加

### 5. コミットとプッシュ

```bash
# 変更をステージング
git add .

# コミット（規則に従ったメッセージ）
git commit -m "feat: add review-example skill"

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

新しいスキルや変更をテストする方法：

**1. ローカル環境にインストール**

```bash
# テスト用プロジェクトディレクトリを作成
mkdir test-project
cd test-project

# .claude/skillsディレクトリを作成
mkdir -p .claude/skills

# 開発中のスキルをコピー
cp -r /path/to/custom-prompts/skills/review-your-skill .claude/skills/
```

**2. Claude Codeでテスト**

```bash
# Claude Codeを起動
# スキルが認識されているか確認
/help

# スキルを実行（名前を指定して明示的に呼び出す）
/review-your-skill
```

**3. 異なるシナリオでテスト**

- 最小限の情報しか提供しない場合
- 技術スタックが不明な場合
- エッジケース（大規模プロジェクト、複雑な構造など）
- Claudeが説明文に基づいて自動的にスキルをトリガーするケース

### 検証チェックリスト

実装したスキルが以下を満たしているか確認：

- [ ] スキルが正しく認識される（`/help`に表示、または関連する依頼で自動トリガーされる）
- [ ] ユーザーとの対話が日本語で実施される
- [ ] 必要な情報を適切に収集する
- [ ] 出力が構造化されている
- [ ] 重大度レベルが適切に使用されている
- [ ] エラーハンドリングが適切
- [ ] ドキュメントが明確で理解しやすい

### セルフレビューガイド

`review-prompt` スキルを使用してセルフレビューする際の観点：

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

### プロジェクト固有のレビュースキル作成

既存のスキルをベースに、プロジェクト固有のカスタムスキルを作成できます：

```markdown
---
name: review-fintech
description: 金融システム特化のセキュリティレビュー
license: MIT
metadata:
  author: your-name
  version: "1.0.0"
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

このファイルを `.claude/skills/review-fintech/SKILL.md` として保存すれば、`review-fintech` スキルが（明示的な呼び出し・自動トリガーの両方で）使えるようになります。

### 関連スキルの整理

チームやカテゴリごとにスキルを整理する場合は、名前にプレフィックスを付けて論理的にグループ化できます：

```
.claude/
└── skills/
    ├── frontend-component/
    │   └── SKILL.md
    ├── frontend-accessibility/
    │   └── SKILL.md
    ├── backend-api/
    │   └── SKILL.md
    └── backend-database/
        └── SKILL.md
```

各スキルは独立したディレクトリを持つフラットな構成になります。ディレクトリの入れ子ではなく、命名規則でグループを表現してください。

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
          claude-code /review-security > security-review.md
      - name: Upload Review Results
        uses: actions/upload-artifact@v3
        with:
          name: security-review
          path: security-review.md
```

**注意**: CI/CD統合は、Claude Code CLIの将来的な機能として想定されています。現時点では手動でのレビュー実行を推奨します。

## カスタマイズ例

### プロンプトの編集

各 `SKILL.md` は標準的なMarkdown形式です。プロジェクト固有の要件に応じてカスタマイズできます。

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

チーム全体で使用するスキルセットをテンプレート化できます：

```bash
# プロジェクトテンプレートの作成
mkdir -p project-templates/web-app/.claude/skills

# 必要なスキルをコピー
cp -r skills/review-security project-templates/web-app/.claude/skills/
cp -r skills/review-after project-templates/web-app/.claude/skills/
cp -r skills/review-perf project-templates/web-app/.claude/skills/

# カスタマイズしたスキルを追加
mkdir -p project-templates/web-app/.claude/skills/review-accessibility
cat > project-templates/web-app/.claude/skills/review-accessibility/SKILL.md <<EOF
---
name: review-accessibility
description: アクセシビリティレビュー用カスタムスキル
license: MIT
---
...
EOF

# 新規プロジェクトで使用
cp -r project-templates/web-app/.claude new-project/
```

## 追加リソース

### 参考資料

- [Claude Code公式ドキュメント（Agent Skills）](https://docs.claude.com/ja/docs/claude-code/skills)
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
