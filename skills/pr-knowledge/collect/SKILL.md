---
name: collect
description: マージ済みPRから知見を抽出し .knowledge/ に蓄積する。差分とレビュー議論の両方を読み、機能・ドメイン知識、アーキテクチャ、開発Tips、コード規約を、OKF形式（簡易版）のMarkdownとして記録・更新する。「YYYY-MM-DDのPRから知見を記録して」「このPR群の知見を返して」のように、日付またはPR番号を伴って呼ばれたときに使う。単なるPR一覧・要約、リリースノートやCHANGELOGの作成には使わない。`.knowledge/` が未初期化なら pr-knowledge:init の実行を促して停止する。pr-knowledgeファミリー（pr-knowledge:collect / pr-knowledge:init / pr-knowledge:orchestrate）の収集役。
license: MIT
metadata:
  author: IsodaZen
  version: "2.0.0"
---

# PR知見蓄積スキル（collect）

マージ済みPRを起点に、将来のDev&Opsで再利用できる知見を抽出する。差分だけでなく**レビューでの議論**を一次情報として扱う。

## スコープ

担うのは「割り当てられた対象から知見を抽出し、指定された形で出力する」ところまで。

**スコープ外（呼び出し側の責務）:** サブエージェントの起動・分割・集約、並行実行時の書き込み調停、複数ワーカー出力のマージと最終反映。

単独セッションで直接呼ばれても、オーケストレータ配下のワーカーとして呼ばれても手順は同じ。違いは出力モードだけ。

**並行実行時の前提:** 書き込みは常に1箇所に集約される。ワーカーは返却モードで動き、`.knowledge/` を読み取り専用で参照する。オーケストレータは全ワーカーの完了後に書き込む。この前提が崩れる場合は、実行前に呼び出し側へ確認する。

## 引数

### 対象（必須）

```
$pr-knowledge:collect 2026-08-25
$pr-knowledge:collect 2026-08-20..2026-08-25
$pr-knowledge:collect #123 #124 #131
```

- 日付は `YYYY-MM-DD` を正とする。相対表現はローカル日付に解決してから使う。
- **対象が指定されていない場合は、今日を仮定せず必ず呼び出し側に確認する。**
- PR番号が列挙された場合は日付検索を行わず、その番号だけを対象とする。

### 出力形式（任意）

指定があれば返却モード、なければファイル更新モード。判定基準は次節。

## 出力モード

### モード判定

以下のいずれかに該当すれば**返却モード**。該当しなければ**ファイル更新モード**。

- 出力形式が名指しされている（JSON、YAML、特定のスキーマ、「この構造で」等）
- 「返して」「出力して」「書き込まずに」と指示されている
- ワーカーとして呼ばれている旨が示されている

判断がつかない場合は**ファイル更新モードを選ばず、呼び出し側に確認する。** 誤って書き込む事故のほうが、確認1回のコストより高い。

### A. ファイル更新モード（既定）

`.knowledge/` を読み、突き合わせて直接書き込む。手順1〜7をすべて実行する。

### B. 返却モード

- **ファイルを一切書かない。** `.knowledge/` は重複判定のため読み取りのみ。
- 手順1〜5を実行し、結果を指定形式で返す。形式の指定が曖昧なら下記の既定スキーマを使い、その旨を明記する。

```json
{
  "target": "2026-08-25",
  "prs_examined": 7,
  "prs_skipped": 4,
  "prs_failed": [{"number": 131, "reason": "fork のため diff 取得不可"}],
  "findings": [
    {
      "type": "architecture",
      "title": "集約境界の切り方",
      "description": "一行要約",
      "tags": ["ddd", "設計判断"],
      "status": "confirmed",
      "body": "## 要点\n...\n\n## 詳細\n...",
      "sources": ["https://github.com/org/repo/pull/123"],
      "existing_file": ".knowledge/architecture/aggregate-boundaries.md",
      "operation": "update",
      "conflicts_with_existing": "リトライ上限3回という既存記述を5回に更新する必要あり"
    }
  ],
  "deferred": [{"summary": "保留した項目", "reason": "レビューで結論が出ていない"}]
}
```

- `existing_file`: 既存に同テーマがあればそのパス、なければ `null`
- `operation`: `create` / `update` / `supersede`（既存記述の置き換えを伴う更新）
- `conflicts_with_existing`: 矛盾がある場合のみ記述。**勝手に解決せず内容を報告する**

複数ワーカーが同じテーマを拾うことはある。重複排除はオーケストレータの仕事なので、他ワーカーの結果を推測せず自分の担当分をそのまま返す。

## 前提条件

作業前に確認し、満たさなければ**停止して報告する**。

1. カレントディレクトリがGitリポジトリである（`git rev-parse --show-toplevel`）
2. `gh` が使え、認証済みである（`gh auth status`）
3. リポジトリがGitHubリモートを持つ（`gh repo view --json nameWithOwner`）
4. `.knowledge/config.json` が存在する

以降のパスはリポジトリルート基準。`gh api` の `{owner}` `{repo}` は gh が現在のリポジトリから自動置換するため、**手で書き換えない。**

### 設定の読み込み

作業前に必ず `.knowledge/config.json` を読む。ここが全パラメータの単一の情報源。

```bash
cat .knowledge/config.json
```

| キー | 用途 |
| :--- | :--- |
| `schema_version` | 互換性チェック。**`1` 以外なら停止する** |
| `base_branches` | 手順1の検索条件。**推測しない** |
| `timezone` | 日付境界の解釈 |
| `types` | 使用可能な `type` 値とディレクトリ対応 |
| `exclude_authors` / `exclude_labels` | 検索段階での除外 |
| `pr_search_limit` | 手順1の取得上限 |
| `language` | 知見本文の記述言語 |
| `frontmatter` / `body_sections` / `status_values` | 出力フォーマット |

バンドルのルートは `.knowledge/` 固定。

**以下の場合は自分で作成・修復せず、`pr-knowledge:init` の実行を促して停止する。**

- `config.json` が存在しない
- JSONとしてパースできない
- `schema_version` が `1` でない

ベースブランチやタイムゾーンを推測で埋めると、対象PRが丸ごとズレる。

## 手順

### 1. 対象PRを特定する

検索条件は `config.json` から組み立てる。

```bash
gh pr list --state merged --limit <pr_search_limit> \
  --search "merged:<DATE> base:<BASE> -author:<EXCLUDED> -label:<EXCLUDED>" \
  --json number,title,url,author,mergedAt,labels
```

- `base:` は `base_branches` の各要素。複数ある場合はブランチごとに検索し、結果を結合して重複を除く（GitHub検索の `base:` はOR結合できない）。
- `-author:` は `exclude_authors`、`-label:` は `exclude_labels` の各要素を並べる。
- 日付境界は `timezone` で解釈する。GitHubの検索はUTC基準なので、`Asia/Tokyo` なら明示する:
  `merged:<DATE>T00:00:00+09:00..<DATE>T23:59:59+09:00`
- 該当0件なら報告して終了。ファイルは作らない。
- **取得件数が `pr_search_limit` に達した場合は、取りこぼしがある前提で報告する。** 黙って先へ進まない。対象期間を分割して再実行するよう促す。
- PR番号が直接指定された場合はこの手順を飛ばす。

### 2. 各PRの本文と差分を読む

```bash
gh pr view <番号> --json title,body,url,author,mergedAt,labels,files
```

`files` の一覧を先に見て、知見が出そうな箇所（設計変更、新規モジュール、設定ファイル、規約に関わる変更）を特定してから差分を読む。

```bash
gh pr diff <番号>
```

- 変更ファイルが多い、または差分が数千行規模の場合は全文を取らず、対象ファイルを絞って読む。
- fork由来のPRやアーカイブ済みリポジトリでは `gh pr diff` が失敗しうる。その場合は本文とレビュー議論だけで判断し、`prs_failed` に理由を記録して次のPRへ進む。**1件の失敗で全体を止めない。**

### 3. レビューの議論を読む

**ここが知見の主要な供給源。** PR本文は「何をしたか」しか書かれていないことが多く、「なぜそうしたか」「なぜそうしなかったか」はレビューのやり取りに残る。

3系統すべてを取得する。いずれか1つでは不十分。

```bash
# レビュー総評（Approve / Request changes に添えられたもの。空のことも多い）
gh pr view <番号> --json reviews

# コード行に紐づくインラインレビューコメント（返信スレッド含む）
gh api repos/{owner}/{repo}/pulls/<番号>/comments --paginate

# PR全体への会話コメント
gh api repos/{owner}/{repo}/issues/<番号>/comments --paginate
```

インラインコメントは `in_reply_to_id` と `path` / `line` を持つ。**同じスレッドは時系列に並べ、やり取り全体として読む。** 単発のコメントを切り出すと文脈が失われる。

コメント数が極端に多いPR（目安100件超）は、`path` 単位でスレッドを束ね、議論が長いスレッドから順に読む。全件を均等に読もうとしない。

レート制限や権限エラーで取得できない場合は、取得できた分で判断し、`prs_failed` に記録する。

拾うべきパターン:

| パターン | 拾えるもの |
| :--- | :--- |
| 「なぜこの方法にしたのか」への回答 | 設計判断とその理由（`architecture`） |
| 「Xだと動かない」「以前Yで踏んだ」 | ハマりどころと回避策（`dev-tip`） |
| 「うちではZで統一している」 | 暗黙の規約（`code-convention`） |
| 業務用語・仕様の確認のやり取り | ドメイン知識（`domain`） |
| 「一旦このままで、後で対応」 | 既知の制約・技術的負債（`feature` / `architecture`） |
| 複数PRで繰り返される同じ指摘 | 明文化されていない規約（`code-convention`。優先度高） |

**未決着のままマージされた議論の扱い:**

- 結論が出ないまま時間切れでマージ → `deferred` に回す
- 「今回は見送り、次で対応」と方針が明示されている → 知見として記録し、本文に「未対応の課題」として明記する
- 方針は決まったが根拠が弱い（一人の意見のみ、検証なし） → 記録するが、フロントマターに `status: draft` を付けて確度を示す

### 4. 知見を抽出する

**PRの要約を作るのではない。** 「次に同じ領域を触る人が知らないと損をすること」だけを拾う。

**使える `type` は `config.json` の `types[].id` がすべて。** ここに無い値を新設しない。既定構成では以下。

| type | 拾うもの |
| :--- | :--- |
| `feature` | 機能の振る舞い、仕様上の制約、既知のエッジケース |
| `domain` | 業務ドメインの概念、用語、ビジネスルール |
| `architecture` | 構成、レイヤ境界、依存の方向、設計判断とその理由 |
| `dev-tip` | 開発・デバッグ・運用の勘所、ハマりどころと回避策 |
| `code-convention` | コーディング規約、命名、レビューで繰り返し指摘される事項 |

どのtypeにも収まらない知見が出た場合は、無理に押し込まず `deferred` に回し、type追加の要否を報告する。

除外するもの:

- 依存バージョン更新のみ、typo修正、フォーマット変更のみのPR
- PR固有で再利用性のない事情（「このPRではAをBに変えた」だけの記述）
- コードを読めば自明なこと
- レビューでの単なる同意・称賛（LGTM、nits のみ）

**該当PRが10件あっても知見が1つしか出ないことは正常。** 無理に埋めない。

判断に迷う項目は独断で書かず、`deferred` に回す（返却モード）か、ユーザーに提示して判断を仰ぐ（ファイル更新モード）。

### 5. 既存の知見と突き合わせる

**両モード共通。** 返却モードでも `existing_file` / `operation` を埋めるために必要。

```bash
cat .knowledge/index.md 2>/dev/null
ls .knowledge/*/
```

`index.md` があればまず読んで全体像を把握する（段階的開示）。

**「同一テーマ」の判定基準** — 以下を上から順に当てる。

1. `type` が異なれば別テーマ。同一ファイルにまとめない
2. `type` が同じで、扱う対象（モジュール、概念、業務ルール）が同一なら同一テーマ → 既存ファイルを更新する
3. `type` が同じで対象が異なるなら別ファイルを作る
4. 判断がつかない場合は**新規作成せず**、既存ファイルへの追記を選ぶか、呼び出し側に確認する

同じモジュールでも「設計理由」と「デバッグの勘所」は `type` が異なる（`architecture` と `dev-tip`）ため、規則1で自動的に別ファイルになる。

ファイルが増えて `ls` だけで見通せなくなったら、`index.md` から各 type 配下の `index.md` へ分割し、階層化する。

ここから先はファイル更新モードのみ。返却モードは手順5の結果を返して終了する。

### 6. 書き込む

#### ディレクトリ構成

`pr-knowledge:init` が生成した構成に従う。ディレクトリは `config.json` の `types[].dir` を使い、**自分で新設しない。**

```
.knowledge/
├── config.json           # 設定（読み取りのみ。書き換えない）
├── index.md              # 全概念の一覧（type別）
├── _template.md          # フロントマター雛形
├── features/
├── domain/
├── architecture/
├── dev-tips/
└── code-conventions/
```

ファイル名はテーマを表すkebab-case（例: `.knowledge/architecture/aggregate-boundaries.md`）。**日付やPR番号をファイル名に含めない。**

#### フロントマター（OKF簡易版）

新規ファイルは `_template.md` をコピーして埋める。**キー構成は `_template.md` が正。** `config.json` の `frontmatter` と食い違う場合はテンプレートに従い、不一致を報告する（設定の修正は `pr-knowledge:init` の責務）。

本文は `config.json` の `language` で書く。

```yaml
---
type: architecture          # 必須。config.json の types[].id のいずれか
title: 集約境界の切り方
description: 一行要約
tags: [ddd, 設計判断]         # 任意
status: confirmed           # 任意。確度が低い場合のみ draft
updated: 2026-08-25
sources:
  - https://github.com/org/repo/pull/123
---
```

#### 本文

セクション構成は `config.json` の `body_sections` に従う（既定は 要点 / 詳細 / 注意点 / 関連）。

- **要点** は3行以内。ここだけ読めば伝わるように書く
- **詳細** は背景、理由、具体例。コード片は最小限に
- **注意点** はハマりどころ、やってはいけないこと。無ければ省略可
- **関連** は通常のMarkdownリンクで相互参照する。リンクを張ったら相手側にも逆リンクを追加する

レビュー議論が出典の場合、該当スレッドの `html_url` を `## 詳細` 内に残すと後から辿れる。

#### 追記のルール

1. **重複を書かない。** 同趣旨の記述があれば追記せず、必要なら表現を精緻化する
2. **矛盾したら本文を上書きし、履歴を残す**

   ```markdown
   ## 変更履歴

   - 2026-08-25: リトライ上限を3回から5回に変更 ([#123](https://github.com/org/repo/pull/123))
   ```
3. `updated` を対象日に更新し、`sources` にPRのURLを追加する

#### index.md

書き込み後に更新する。

見出しは `config.json` の `types[].label` に一致させる。**見出しを追加・改名・削除しない。前文もそのまま残す。**

```markdown
# Knowledge Index

このディレクトリは pr-knowledge スキルが管理します。
書式とベースブランチ等の設定は config.json を参照してください。

## アーキテクチャ

- [集約境界の切り方](architecture/aggregate-boundaries.md) — 集約をどこで割るかの判断基準
```

`.knowledge/` やディレクトリが存在しない場合は**作成せず**、`pr-knowledge:init` の実行を促して停止する。

### 7. 報告する

- 対象と対象PR件数（知見なしとして除外した件数、取得に失敗した件数を内訳で）
- 新規作成したファイル一覧
- 更新したファイル一覧と、何を追記/変更したかの一行説明
- 判断を保留した項目

## 禁止事項

- **`.knowledge/` の外を書き換えない。** ソースコードは読むだけ
- **コミットしない。** 変更はワーキングツリーに残す。明示的に依頼された場合のみコミットする
- **返却モードでは絶対に書き込まない。** 並行実行中の書き込み衝突は、このスキルが最も避けるべき事故
