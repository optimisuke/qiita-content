---
title: npx skills は GitHub Enterprise でも使えそうだった
tags:
  - GitHub
  - codex
  - AIエージェント
  - ClaudeCode
private: false
updated_at: "2026-07-04T07:57:09+09:00"
id: eb6d83dcb75642b444e2
organization_url_name: ibm
slide: false
ignorePublish: false

agreed_posting_campaign_term: false
---

# はじめに

AI Agent 向けの Skill を、チームやプロジェクトで共有しやすくできないかを検証しました。

使ったのは `npx skills` です。

`npx skills` は、Skill を GitHub repo や別フォルダから取得して、各 Agent の skills フォルダへ配置する CLI です。

結論、GitHub Enterprise の repository からも Skill を取得できました。社内で Skill を共有する用途でも、かなり使えそうです。

検証対象の Agent は主に IBM Bob / Claude Code / Codex です。IBM Bob は、社内で使っている AI Agent のひとつです。

今回確認した流れを図にすると、だいたい以下のようなイメージです。

![skill-sharing-overview.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/5b02e092-5358-4d7c-9bbb-71fd078c5fc3.png)

# 何に困っていたか

Skill を使い始めると、以下の管理がだんだんめんどくさくなります。

- チーム共有
- プロジェクト別管理
- Agent 別管理

また、便利な Skill を作っても、自分の `~/.agents/skills` だけに閉じていると、結局その知見は自分の環境から出ていきません。

せっかく暗黙知を `SKILL.md` として形式知にしても、共有・更新・削除の運用がないと、チームやプロジェクトで再利用しにくいままです。

これらの課題を解決するために、`npx skills` で、GitHub Enterprise / GitHub public repo / 別フォルダにある Skill を、project scope または global scope の skills フォルダへ配置できるかを確認しました。

# Skill repo の構成

検証用に、最小構成の Skill を用意しました。

```text
skills/
└── hello-skill/
    └── SKILL.md
```

repository 内の構成は、まずは `skills/<skill-name>/SKILL.md` に寄せるのがわかりやすそうでした。

公式の探索ルールはこちらです。

https://github.com/vercel-labs/skills#skill-discovery

# コマンド例

IBM Bob 向けに global install する例です。

```bash
npx skills add <repo-url> --skill hello-skill -g -a bob -y
```

一覧確認:

```bash
npx skills list -g -a bob
```

削除:

```bash
npx skills remove hello-skill -g -a bob -y
```

`--skill` を指定すると、repository 内の特定 Skill だけ取得できます。

Agent 名は CLI 側の対応状況や環境によって変わる可能性があるので、実際に使う Agent に合わせて確認してください。

# 検証結果

確認できたことは以下です。

- GitHub Enterprise の repository URL から Skill を取得できた
- `--skill` で特定 Skill だけ取得できた
- `-a bob` / `-a codex` で対象 Agent を切り替えられた（入れるフォルダが変わるだけですが）
- global install / remove は動作した
- 同じ Skill 資産を複数 Agent から参照する構成にできそうだった

GitHub Enterprise の private repository から取得できたのが、一番大きい確認ポイントでした。

# `gh skill` との違い

2026年6月初旬時点の検証では、`gh skill` では GitHub Enterprise 上の Skill repo をうまく扱えませんでした。

一方で、`npx skills` は GitHub Enterprise の repository URL を指定して Skill を取得できました。

`npx skills` は `git` コマンド経由で repository を取得する挙動に近そうで、既存の GitHub Enterprise 認証や Git の接続設定を利用できた可能性があります。

# 注意点

`add` / `remove` は使いやすかった一方で、`update` はまだ注意が必要でした。

自分の環境では、`npx skills update` があやしい挙動をしてました。issues にもいくつか `update` まわりのものがありました。当面は `update` よりも、`add` を再実行して上書きする方が安全そうです。

```bash
npx skills add <repo-url> --skill hello-skill -g -a bob -y
```

# おわりに

`npx skills` を使うと、AI Agent 向け Skill を GitHub Enterprise や GitHub の repository で管理して、必要な Agent に配布する運用ができそうでした。

まだ `update` 周りには注意が必要ですが、社内で Skill を共有する入口としてはかなり良さそうです。
