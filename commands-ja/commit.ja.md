---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git diff:*)
description: Gitコミットを作成する
---

## コンテキスト

- 現在のgitステータス: !`git status`
- 現在のgit diff（ステージ済み・未ステージの変更）: !`git diff HEAD`
- 現在のブランチ: !`git branch --show-current`
- 最近のコミット: !`git log --oneline -10`

## タスク

現在の変更に対して、単一のgitコミットを作成してください。

- 1つのレスポンスで複数のツールを呼び出すことができます。
- 1つのメッセージでステージングとコミットを実行してください。

## 要件

- 他のツールの使用や他の操作は行わないでください。
- これらのツール呼び出し以外のテキストやメッセージは送信しないでください。
- **自動生成フッターなし** - Claude Codeの自動生成フッターをコミットメッセージに追加しないでください
- コミットメッセージはCLAUDE.mdの言語を用いること。
- コミットメッセージは1行で完結し、50〜80文字程度を目安とする

## コミットメッセージ

### Conventional Commits形式

```
<type>(<scope>): <description>
```

### Type一覧

- `feat`: 新機能
- `fix`: バグ修正
- `docs`: ドキュメント
- `style`: コードスタイル（機能に影響なし）
- `refactor`: リファクタリング
- `test`: テストの追加・修正
- `chore`: ビルド・設定の変更
