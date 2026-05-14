# qiita-content

公式の [Qiita CLI](https://github.com/increments/qiita-cli) で管理している Qiita 記事リポジトリです。

公開済みの Qiita 記事を `public/` 配下の Markdown ファイルとして管理しています。

## セットアップ

このリポジトリでは Volta で Node.js のバージョンを固定しています。

```bash
npm install
npx qiita login
```

`npx qiita login` では、`read_qiita` と `write_qiita` 権限を持つ Qiita アクセストークンが必要です。

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

指定した記事を投稿または更新します（ローカル確認用。通常は git push 経由で公開してください）。

```bash
npx qiita publish article-file-name
```

変更された記事をまとめて投稿または更新します（ローカル確認用。通常は git push 経由で公開してください）。

```bash
npx qiita publish --all
```

## GitHub Actions

`.github/workflows/publish.yml` で、`main` または `master` への push 時に記事を投稿・更新します。
GitHub Actions の画面から手動実行することもできます。

利用するには、リポジトリの secret に以下を設定しておく必要があります。

```text
QIITA_TOKEN
```

## 注意点

- 記事ファイルは `public/*.md` に保存されます。
- `public/.remote/` は Qiita CLI が生成する比較用データで、Git 管理からは除外しています。
- Markdown ファイルを削除しても、Qiita 上の記事は削除されません。記事の削除は Qiita 側で行います。
- 記事が公開されると Qiita 側で `updated_at` が更新されます。Actions 経由で公開した後は必ず `npx qiita pull` → `git pull` でローカルを同期してください。同期しないと次回 push 時に衝突する可能性があります。
