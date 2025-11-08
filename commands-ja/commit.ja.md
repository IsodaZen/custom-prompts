---
allowed-tools: Bash(git add:*), Bash(git status:*), Bash(git checkout:*)
description: Gitコミットを作成する
---

## タスク

* 現在のリポジトリの修正に対して、Commitを実行してください。

## 要件

* **自動生成フッターなし** - Claude Codeの自動生成フッターをコミットメッセージに追加しないでください

## コミットメッセージ

### Conventional Commits形式

```
<type>(<scope>): <description>

<body>
```

### Type一覧
- `feat`: 新機能
- `fix`: バグ修正
- `docs`: ドキュメント
- `style`: コードスタイル（機能に影響なし）
- `refactor`: リファクタリング
- `test`: テストの追加・修正
- `chore`: ビルド・設定の変更
