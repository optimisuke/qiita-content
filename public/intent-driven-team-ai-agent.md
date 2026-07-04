---
title: AIエージェント時代、なぜIntent（意図）が重要になるのか
tags:
  - アジャイル
  - AI
  - scrum
  - 要件定義
  - LLM
private: false
updated_at: "2026-07-04T07:57:09+09:00"
id: 0ea2c72b6fd866505a56
organization_url_name: null
slide: false
ignorePublish: false

agreed_posting_campaign_term: true
---

# はじめに

最近、AIコーディングエージェント界隈で「Intent-Driven Development」や「Intent-First Development」といった言葉を見かけるようになりました。

Claude Code や Codex のようなAIエージェントが広がる中で、「仕様ではなく意図を渡そう」「タスクではなくIntentでチームを動かそう」といった話が増えているように感じます。

ただ、Intent-Driven DevelopmentはScrumやLeSSのような確立された方法論ではありません。どちらかというと、AIエージェント時代の開発の方向性を表す概念に近いものだと思っています。

そこで気になったのが、「なぜ今になってIntentが注目されているのか」ということでした。

この記事では、Intentという言葉を新しい開発手法として紹介するというより、これまでアジャイルや要求工学の中にあった考え方が、AIエージェントによってなぜ改めて重要になっているのかを整理してみます。

![Intent-Driven Teamの概要](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/2d4a1557-6619-434b-ab62-cc88ce7a8379.png)

かなり自分の理解を整理するための記事になっています。

# Intentとは何か

Intentは日本語にすると「意図」「狙い」といった意味です。

ソフトウェア開発の文脈では、だいたい次のようなものを表す言葉として使われます。

- なぜ作るのか
- 何を達成したいのか
- どのような制約があるのか

例えば、次のような表現は要求や仕様に近いです。

- 注文履歴画面を作る
- 検索機能を追加する

一方で、次のような表現はIntentに近いです。

- 再購入率を向上したい
- 問い合わせ件数を減らしたい

つまりIntentは、WhatよりもWhyに近い概念と言えそうです。

# Intentは新しい概念ではない

気になっていたのは、Intentは新しい概念なのかというところですが、昔からある概念のスコープをAIエージェント時代に合わせて切り直した感じがしました。

例えば、アジャイル開発でよく使われるUser Storyは、次のような形式で書かれます。

```text
As a ...
I want ...
So that ...
```

例えば、次のようなUser Storyがあったとします。

```text
顧客として注文履歴を確認したい。
なぜなら再購入したいから。
```

このとき、前半と後半は少し性質が違います。

- 注文履歴を確認したい: What
- 再購入したい: Why

Intentに近いのは後者です。

つまりIntentは、実はUser Storyの中に昔から存在していました。

また、要求工学の世界には Goal-Oriented Requirements Engineering（GORE）という分野があります。GoalからRequirementへ落としていく考え方です。

https://en.wikipedia.org/wiki/Goal-oriented_requirements_engineering

システム開発やMBSEの分野でも、MissionやCapabilityといった概念を使いながら、「なぜ作るのか」と「何を作るのか」をつなげようとしてきました。

そう考えると、Intentは新発明というより、昔からあった考え方をAI時代に再解釈したものにみえます。

# Intentは会話の中に存在していた

小さなアジャイルチームでは、Intentは明文化されていなくても共有できていました。

例えば、

```text
問い合わせを減らしたい
```

とか、

```text
再購入率を上げたい
```

といった背景を、POと開発者が日常的な会話の中で共有できていたからです。

しかし組織が大きくなると、Jiraに残るのはStoryやTaskだけになりがちです。

すると、

```text
この機能は何のために作るんだっけ？
```

という状態が起きます。

Intentは存在していたのですが、たくさんのUser Storyや会話の中に埋もれていたとも言えます。

# なぜ今Intentが重要になるのか

AIエージェントが実装スピードを格段にあげたからです。
従来のAIコーディング支援は、主に実装工程を対象としていました。

```text
Requirement
↓
Design
↓
Code ← AI
```

その結果、ボトルネックが上流に移ってきました。そこで、要求分析や設計までAIに任せたいという期待が高まっています。

```text
Intent
↓
Requirement ← AI
↓
Design ← AI
↓
Code ← AI
```

そうなると、AIに何を渡すかが重要になります。

例えば、

```text
注文履歴画面を作る
```

という要求を渡せば、AIはその通りに実装します。

しかし、

```text
再購入率を向上したい
```

というIntentを渡した場合は、複数の解決策を検討できます。

- 注文履歴
- お気に入り
- ワンタップ再注文
- レコメンド

つまりIntentの価値は、Solution Spaceを広げることにあります。

人間が最初から解決策を決めるのではなく、達成したい目的や制約を共有し、その上でAIと一緒に解決策を探索する。この使い方が、AIエージェントに期待されてきています。

AIコーディングエージェントが普及する前ですが、通信業界のEricssonの解説記事でも、AIがIntentを解釈し、オーケストレーションやアシュアランスに活用していく方向性が説明されています。

https://www.ericsson.com/ja/blog/2024/11/how-ai-empowers-intent-driven-service-orchestration-and-assurance

Intentという考え方そのものが、AIと相性の良い抽象化レイヤーなのかもしれない、と思いました。

# Spec-Driven Developmentとの関係

Intent-Driveという言葉が出てきた際に、

```text
Spec-Driven Developmentと何が違うんだろう？
```

と思う方もいるかもしれません。

自分の理解では、Spec-Driven Developmentは次のような考え方です。

```text
Human
↓
Spec
↓
AI
↓
Code
```

人間がAIと一緒に仕様を明確にし、AIはその仕様に従って実装します。

一方、Intent-Driven Developmentは次のようなイメージです。

```text
Human
↓
Intent
↓
AI
↓
Spec
↓
Code
```

違いは、AIにどこまで任せるか

Intent-Driven DevelopmentはSpec-Driven Developmentを置き換えるものというより、その上流、なぜ作るかも含めた考え方として捉える方が自然だと思います。

IntentからいきなりCodeに飛ぶのではなく、IntentをもとにAIとRequirementやSpecを作り、そのうえで実装する。そう考えると、Spec-Drivenな進め方とも矛盾しません。

# 本当に変わるのはチームかもしれない

調べていく中で、自分が興味を持ったのはIntent-Driven Developmentそのものよりも、Intent-Driven Teamという考え方でした。

アジャイル開発は元々、「正しく作る」よりも「正しいものを作る」ことを重視してきました。

そのために、次のような概念があります。

- Product Goal
- Outcome
- Customer Value

例えばScrumではProduct Goalが定義されていますし、LeSSでも「1 Product, 1 Product Goal」という考え方が重視されています。

- Scrum Guide: https://scrumguides.org/
- LeSS: https://less.works/

一方で現実には、次のようなものが中心になりがちです。

- Story
- Task
- Velocity
- Sprint

気が付けば、「何を達成したいのか」よりも「どれだけ消化したか」に意識が向いてしまいます。

これは以前からアジャイルや大規模アジャイルで指摘されていた課題だと思います。

チームや組織が大きくなるほど、局所的な最適化に陥りがちです。

- Story最適
- Feature最適
- チーム最適

一方で、次のような最適化は難しくなります。

- プロダクト最適
- 顧客価値最適

だからこそ、LeSSやSAFeでは、Storyより上位のProduct Goal、Outcome、Capabilityといった概念が重視されてきたのだと思います。

AIエージェントが増えると、この問題はさらに大きくなりそうです。

例えば、次のようなエージェントが同時に動く世界を考えてみます。

- Planner Agent
- Architect Agent
- Coding Agent
- Test Agent

それぞれがTaskだけを見て最適化すると、効率よく間違ったものが量産されます。

これは人間だけの組織でも起きていた問題ですが、AIによって実行速度が上がることで、改めて重要性が増しているように見えます。

ここで重要になるのは、Task Alignmentではなく、Intent Alignmentです。

人間もAIも、「何を作るか」だけでなく、「なぜ作るのか」を共有する必要があります。

# おわりに

Intentは新しい概念ではありません。

User Storyにも、要求工学にも、システム開発にも、昔から存在していました。

しかしAIエージェントによって、実装の制約は小さくなり、「どう作るか」よりも「なぜ作るか」の重要性が増しています。

Intent-Driven Developmentという言葉が注目されている背景には、AIを活用してより価値のあるものを作りたいという期待があるのだと思います。

そして変わるべきは、開発プロセスだけでなく、組織なのかと思います。そして、AIエージェント時代の競争力は、人間とAIが同じIntentを共有できるかどうかで決まるんじゃないかと思います。
