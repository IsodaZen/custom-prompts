---
name: init
description: pr-knowledge:collect スキル用の知見バンドルを初期化する。ベースブランチやタイムゾーン、対象typeをヒアリングして .knowledge/config.json に確定させ、ディレクトリ構成・index.md・フロントマター雛形を生成する。「知見バンドルを初期化して」「.knowledge をセットアップして」「pr-knowledge の初期設定をして」のように呼ばれたときに使う。既に初期化済みのバンドルへの知見追記には使わない（pr-knowledge:collect を使う）。pr-knowledgeファミリー（pr-knowledge:collect / pr-knowledge:init / pr-knowledge:orchestrate）の初期化役。
license: MIT
metadata:
  author: IsodaZen
  version: "1.0.0"
---

# 知見バンドル初期化スキル（init）

`pr-knowledge:collect` が使う `.knowledge/` バンドルを、リポジトリごとの設定込みで用意する。

**このスキルの成果物は設定と器だけ。** 知見そのものは書かない。

## 呼び出し

引数は取らない。

```
$pr-knowledge:init
```

設定値は必ず手順2のヒアリングで確定させる。**引数や既存ファイルからの推測で埋めない。**

## 手順

### 1. 前提確認と冪等性チェック

```bash
git rev-parse --show-toplevel
gh auth status
gh repo view --json nameWithOwner,defaultBranchRef
```

満たさなければ停止して報告する。

バンドルのルートは `.knowledge/` 固定。可変にしない。

`.knowledge/config.json` が既に存在する場合:

- **上書きしない。** 現在の設定を表示し、「変更したい項目があるか」を尋ねる
- 変更指示があった項目だけを更新し、`schema_version` は据え置く
- 変更がなければ、不足しているディレクトリ・ファイルだけを補完して終了する

`config.json` が存在するがJSONとしてパースできない場合:

- **自動修復しない。** パースエラーの内容を提示し、手で直すか作り直すかをユーザーに選ばせる
- 作り直す場合のみ、破損ファイルを `config.json.broken` にリネームしてから手順2へ進む

### 2. ヒアリング

以下をまとめて提示し、既定値を示したうえで回答を求める。1問ずつ聞かない。

| 項目 | 既定値 | 補足 |
| :--- | :--- | :--- |
| ベースブランチ | `gh repo view --json defaultBranchRef` の値 | **必ず確認する。** 複数可（`main` と `develop` の併用、リリースブランチ運用など）。ここを誤ると対象PRが丸ごとズレる |
| タイムゾーン | システムのTZ、不明なら `UTC` | 日付境界の解釈に使う。GitHub検索はUTC基準なので、JST運用ならここで確定させる |
| 対象type | `feature` `domain` `architecture` `dev-tip` `code-convention` | 追加・削除・リネーム可。リポジトリの性質に合わないtypeは外す |
| 記述言語 | 直近のコミットメッセージ/PRタイトルから推定 | `ja` / `en` |
| 除外する作成者 | `app/dependabot` `app/renovate` | bot PRをクエリ段階で落とす |
| 除外するラベル | なし | `chore` `dependencies` などを運用しているなら指定 |

ベースブランチについては、`defaultBranchRef` の値をそのまま既定として提示しつつ、**「リリースブランチやdevelopへのマージも対象にするか」を明示的に尋ねる。** GitHub Flow 以外の運用は既定値の推定が外れやすい。

### 3. config.json を生成する

`.knowledge/config.json` に書き出す。

```json
{
  "schema_version": 1,
  "repository": "org/repo",
  "base_branches": ["main"],
  "timezone": "Asia/Tokyo",
  "language": "ja",
  "types": [
    { "id": "feature",         "dir": "features",          "label": "機能・仕様",     "description": "機能の振る舞い、仕様上の制約、既知のエッジケース" },
    { "id": "domain",          "dir": "domain",            "label": "ドメイン知識",   "description": "業務ドメインの概念、用語、ビジネスルール" },
    { "id": "architecture",    "dir": "architecture",      "label": "アーキテクチャ", "description": "構成、レイヤ境界、依存の方向、設計判断とその理由" },
    { "id": "dev-tip",         "dir": "dev-tips",          "label": "開発Tips",       "description": "開発・デバッグ・運用の勘所、ハマりどころと回避策" },
    { "id": "code-convention", "dir": "code-conventions",  "label": "コード規約",     "description": "コーディング規約、命名、レビューで繰り返し指摘される事項" }
  ],
  "exclude_authors": ["app/dependabot", "app/renovate"],
  "exclude_labels": [],
  "pr_search_limit": 100,
  "frontmatter": {
    "required": ["type", "title", "description", "updated", "sources"],
    "optional": ["tags", "status"]
  },
  "status_values": ["confirmed", "draft"],
  "body_sections": ["要点", "詳細", "注意点", "関連"],
  "initialized_at": "2026-08-27"
}
```

- `schema_version` は**このスキルとconfigの構造互換性**を表す。`pr-knowledge:collect` は自分が解釈できる版と一致しない場合に停止する。設定値を変更しただけでは上げない
- `type` はOKFで唯一の必須フロントマターなので、`types[].id` がそのまま `type` の許容値になる。ここに無い値は `pr-knowledge:collect` 側で使わせない
- `dir` は `.knowledge/` からの相対パス
- `language` は知見本文の記述言語。`pr-knowledge:collect` はこれに従って書く
- `status_values` は知見の確度。議論が未決着のまま記録する場合に `draft` を使う
- 生成後に `python3 -m json.tool .knowledge/config.json` でパース検証する

### 4. ディレクトリと雛形を生成する

```
.knowledge/
├── config.json
├── index.md
├── _template.md
├── features/.gitkeep
├── domain/.gitkeep
├── architecture/.gitkeep
├── dev-tips/.gitkeep
└── code-conventions/.gitkeep
```

`types` で選ばれたものだけ作る。既存ディレクトリは削除しない。

#### `_template.md`

フロントマターを定型化するための雛形。`pr-knowledge:collect` はこれを起点に新規ファイルを作る。

```markdown
---
type:
title:
description:
tags: []
updated:
sources:
  -
---

## 要点

## 詳細

## 注意点

## 関連
```

`config.json` の `frontmatter` と `body_sections` を変更した場合は、`_template.md` も必ず追従させる。**両者が食い違うとフォーマットが崩れる。**

#### `index.md`

`types[].label` から見出しだけを生成する。項目は空でよい。**`pr-knowledge:collect` が更新するのはこの見出しの配下だけで、前文は保持される。**

```markdown
# Knowledge Index

このディレクトリは pr-knowledge スキルが管理します。
書式とベースブランチ等の設定は config.json を参照してください。

## 機能・仕様

## ドメイン知識

## アーキテクチャ

## 開発Tips

## コード規約
```

### 5. 検証して報告する

- `config.json` がJSONとしてパースできること
- `types[].dir` がすべて実在すること
- `_template.md` のフロントマターキーが `config.json` の `frontmatter.required` を満たすこと
- `index.md` の見出しが `types[].label` と一対一で対応すること

報告内容:

- 生成したファイル・ディレクトリ一覧
- 確定した `base_branches` と `timezone`（**ここは必ず読み上げる。** 誤りが最も影響する項目）
- 既定値のまま採用した項目
- 次のアクション（`$pr-knowledge:collect <日付>` で運用開始できる旨）

## 禁止事項

- **既存の `config.json` を無確認で上書きしない**
- **既存の知見ファイルを一切変更・削除しない**
- **コミットしない。** 変更はワーキングツリーに残す
- **知見の中身を書かない。** サンプル知見やダミーエントリも作らない
