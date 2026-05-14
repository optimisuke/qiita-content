# qiita-content

公式の [Qiita CLI](https://github.com/increments/qiita-cli) で管理している Qiita 記事リポジトリです。

## 運用フロー

記事の投稿・更新は **git push 経由** で行います。`npx qiita publish` での直接公開は基本使いません。

```
記事を編集 → git push → GitHub Actions が自動で Qiita に投稿・更新
                              ↓
                        git pull（updated_at が書き戻される）
```

公開後は Qiita 側で `updated_at` と `id` が更新され、Actions がその内容を commit して push し返します。
**`git push` 後は必ず `git pull` でローカルを同期してください。** 同期しないと次回 push 時に衝突します。

## 記事ファイルについて

- 記事ファイルは `public/*.md` に保存されます
- ファイル名は任意です。Qiita との紐づけは front matter の `id` フィールドで行われます
- `id: null` で push すると新規記事として作成され、Qiita が採番した ID が Actions によって front matter に書き戻されます（ID は自分では決められません）
- `public/.remote/` は Qiita CLI が生成する比較用データで、Git 管理からは除外しています
- Markdown ファイルを削除しても Qiita 上の記事は削除されません。削除は Qiita 側で行います

## セットアップ

このリポジトリでは Volta で Node.js のバージョンを固定しています。

```bash
npm install
npx qiita login
```

`npx qiita login` では、`read_qiita` と `write_qiita` 権限を持つ Qiita アクセストークンが必要です。

GitHub Actions を動かすには、リポジトリの secret に以下を設定しておく必要があります。

```text
QIITA_TOKEN
```

## よく使うコマンド

Qiita 上の記事をローカルに同期します。

```bash
npx qiita pull
```

ローカルプレビューを起動します。

```bash
npx qiita preview
```

新しい記事ファイルを作成します。

```bash
npx qiita new article-file-name
```
