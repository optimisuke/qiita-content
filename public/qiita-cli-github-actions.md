---
title: Zenn と同じ感覚で Qiita 記事を Git 管理する
tags:
  - Qiita
  - GitHubActions
  - QiitaCLI
private: false
updated_at: '2026-05-14T11:51:44+09:00'
id: 0f149e74d1cf7e395e33
organization_url_name: null
slide: false
ignorePublish: false
---

# はじめに

Zenn は GitHub で記事を管理していて、ローカルのエディタで書いて push するだけで公開できるので、すごく快適に使ってました。

一方で Qiita の記事はずっとブラウザ上で書いていて、バージョン管理もできないし、エディタのショートカットも使えないし、地味につらかったです。

ついさっき Qiita CLI の存在に気づいて、「あ、Qiita も同じようにできるんだ」となりました。今更感はありつつも、せっかくなのでセットアップして Zenn と同じ運用に揃えました。

まだ使い始めたばかりですが、かなり便利そうな予感がしています。

同じように Zenn は GitHub 管理してるけど Qiita はブラウザで書いてる、という人の参考になれば。

参考:
https://github.com/increments/qiita-cli
https://qiita.com/Qiita/items/32c79014509987541130

# Qiita CLI とは

公式の CLI ツールで、Qiita の記事を Markdown ファイルとしてローカルで管理できます。プレビュー、投稿、更新まで CLI で完結します。

npm パッケージ名は `@qiita/qiita-cli` です。似た名前の別パッケージがあるので注意です。

記事は `public/` 配下の `.md` ファイルで管理します。Zenn の `articles/` と同じ感じです。

GitHub Actions との連携もサポートされていて、`main` ブランチへの push で自動公開する設定が最初から用意されています。

# セットアップ

## インストールとログイン

```bash
npm install @qiita/qiita-cli --save-dev
npx qiita init
npx qiita login
```

`npx qiita init` を実行すると `qiita.config.json` と `.github/workflows/publish.yml` が作られました。

`npx qiita login` には Qiita のアクセストークンが必要です。`read_qiita` と `write_qiita` 権限をつけて発行してください。

https://qiita.com/settings/applications

Node.js のバージョン管理には Volta を使っていますが、その辺は好みの方法で。

## 既存記事を取得する

ログインできたら、まず既存の記事をローカルに取得します。

```bash
npx qiita pull
```

これで `public/` 配下に記事ファイルが作られます。長年 Qiita に書いてた人は、ここで大量のファイルが降ってきます。

# GitHub リポジトリに乗せる

`git init` して初回コミットを作ります。

`public/.remote/` は Qiita CLI が生成する比較用データなので `.gitignore` に入れておきます（`npx qiita init` で生成される `.gitignore` に最初から含まれています）。

```bash
git init -b main
git add .
git commit -m "Initial Qiita content sync"
```

リポジトリを作って push します。`gh` CLI が入っていれば一発です。

```bash
gh repo create qiita-content --private --source=. --remote=origin --push
```

public にするかどうかは好みですが、どうせ Qiita で公開している記事なので、自分は public にしました。

# GitHub Actions で自動公開する

`npx qiita init` で生成された `.github/workflows/publish.yml` が、`main` への push 時に記事を公開・更新してくれます。

動かすには、GitHub のリポジトリ設定で `QIITA_TOKEN` を secret に登録します。

```bash
gh secret set QIITA_TOKEN --repo <your-repo>
```

これで push するだけで公開されるようになります。

# 運用上の注意

## 直接 publish より git push 経由を推奨

`npx qiita publish` でローカルから直接公開もできますが、基本は git push 経由にしています。

ローカルから直接 publish すると、Git 管理の状態と Qiita 上の状態がずれる場面が出てきます。Actions 経由に統一しておくほうがシンプルです。

## 公開後は git pull を忘れずに

記事が公開されると、Qiita 側で `updated_at` が更新されます。また新規記事の場合は `id` も採番されます。

Actions はその内容を front matter に反映して、自動でコミット・push し返してくれます。なのでローカルは `git pull` するだけで同期できます。

この仕組みを知らずに次の編集をしてしまうと、次回 push 時に衝突します。この記事を公開したときに実際に確認できました。

# おわりに

Zenn と同じ感覚で Qiita の記事も管理できるようになりました。今更感はありますが、知らずにブラウザで書き続けるよりは良さそうです。

Qiita CLI の存在を知らなかっただけで、仕組み自体は Zenn と大差なかったので、Zenn を GitHub 管理している人ならすぐ揃えられると思います。使い始めたばかりですが、この運用が定着するといい感じになりそうで楽しみです。

一点まだ試せていないのが、記事内の画像の扱いです。Zenn はリポジトリの決まったフォルダに画像を置いて相対パスで指定すればよしなにやってくれるんですが、Qiita CLI でどう管理するのがいいか、別途試してみるつもりです。
