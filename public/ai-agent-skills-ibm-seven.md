---
title: AIエージェント構築に必要な7つのスキル + アルファ
tags:
  - AI
  - IBM
  - Agent
  - LLM
private: false
updated_at: '2026-07-04T11:48:20+09:00'
id: ddb236ce57b734b7758d
organization_url_name: ibm
slide: false
ignorePublish: false
posting_campaign_uuid: null
agreed_posting_campaign_term: false
---

# はじめに

AI エージェントを作る話を追っていると、プロンプト、RAG、ツール、評価、ガードレール、マルチエージェントなど、いろいろな言葉が一気に出てきます。

自分も「結局どこからどこまで学べばいいんだろう」と混乱しています。そこで、IBM の動画 [The 7 Skills You Need to Build AI Agents](https://www.youtube.com/watch?v=mtiOK2QG9Q0) を入口にして、AI エージェント構築に必要なスキルを整理してみます。

<iframe width="560" height="315" src="https://www.youtube.com/embed/mtiOK2QG9Q0?si=mqHiZ-Tmw7ke5BYY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

ただ、IBM の7つだけを唯一の地図として見ると、少し抜ける視点もありそうです。この記事では IBM の7スキルを土台にしつつ、Karpathy、Andrew Ng、Anthropic、OpenAI、Google、Chip Huyen などの一次情報と見比べます。

細かい実装手順ではなく、「これから AI エージェントを作るなら、何を学ぶべきか」の地図を作るための記事です。

![AIエージェント構築スキルの整理](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/1fded029-950f-4258-8f23-9a64a84fc0ef.png)

読んだ方に次のような状態を提供できればとおもってます。

- IBM の7スキルを、単なるチェックリストではなく実装の地図として使える
- 自分のプロジェクトで、なにをすべきか判断できる
- 「エージェントを作る」以外に、シンプルなワークフローで済ませる選択肢も持てる

# まず結論

AI エージェント構築でまず押さえるべき核は、次の3つだと思っています。

- **アーキテクチャとオーケストレーション**: LLM、ツール、データ、サブエージェント、人間の確認をどうつなぐか
- **ツール設計**: AI が迷わず使える入出力、スキーマ、権限、失敗時の扱いをどう設計するか
- **評価とオブザーバビリティ**: デモで動いたものを、なぜ失敗したか追える本番システムにできるか

特に評価は、複数の情報源がかなり強く言っています。プロンプトをいじって「なんとなく良くなった気がする」で止めず、ログ、トレース、メトリクス、テストケースで改善するところが、エージェント構築の本番感だと思います。

全体像を先に表にすると、こう見ています。

| 観点     | IBM の7スキルで見えること          | 他の情報源で補うこと                                           |
| -------- | ---------------------------------- | -------------------------------------------------------------- |
| 設計     | システム設計、信頼性、セキュリティ | Anthropic のシンプルなワークフロー、OpenAI のエージェント分割  |
| ツール   | ツールと契約の設計                 | OpenAI / Anthropic のツール仕様、権限、ガードレール            |
| 検索     | リトリーバルエンジニアリング       | Google / Karpathy の memory、context engineering               |
| 評価     | 評価とオブザーバビリティ           | Google の logs/traces/metrics、Huyen の評価体系                |
| 任せ方   | プロダクト思考                     | Karpathy の autonomy slider、OpenAI の人間へのエスカレーション |
| 作る判断 | IBM では薄め                       | Anthropic の「まずシンプルに作る」考え方                       |

# IBM の7つのスキル

IBM Technology の動画は、プロンプトエンジニアリングとエージェントエンジニアリングを分けて考えています。

良いプロンプトを書くことは大事ですが、それだけではエージェントは作れません。LLM、ツール、データベース、外部API、権限、人間の確認、失敗時の回復まで含めて、システムとして設計する必要があります。

動画で挙げられている7つを、自分の理解で整理するとこうです。

| スキル                       | 何を見るか                                                                 |
| ---------------------------- | -------------------------------------------------------------------------- |
| システム設計                 | LLM、ツール、DB、サブエージェントをどう協調させるか。失敗時の経路も含める  |
| ツールと契約の設計           | 入力、出力、エラー、使ってよい条件を型と具体例で固める                     |
| リトリーバルエンジニアリング | RAG の品質。チャンキング、ベクトル化、検索、リランキング                   |
| 信頼性エンジニアリング       | リトライ、タイムアウト、フォールバック、サーキットブレーカー               |
| セキュリティと安全性         | プロンプトインジェクション、入力検証、出力フィルタ、権限分離               |
| 評価とオブザーバビリティ     | 成功率、レイテンシ、コスト、失敗理由、ツール呼び出し履歴を追えるようにする |
| プロダクト思考               | 不確実なときにどう振る舞うか、いつ確認・エスカレーションするか             |

ここで大事なのは、多くが「従来のソフトウェア工学を、確率的に振る舞う部品に適用し直す」話だということです。

エージェントは、モデルが賢ければ勝手に安定するものではありません。外部ツールが落ちる、検索結果が弱い、モデルが形式を間違える、読んだ文書に悪意ある指示が含まれる。そういう前提で設計する必要があります。

# IBM の7つだけでは少し足りないところ

IBM の7スキルはかなり実務的です。一方で、他の一次情報と見比べると、IBM ではあまり前面に出ていない論点もあります。

## メモリとコンテキスト管理

Google の Agents ホワイトペーパーや 5-Day Intensive では、memory が主要な構成要素として扱われています。Karpathy も、LLM は長期的な記憶を自然には持たず、基本的にはコンテキストウィンドウに依存している、という問題を強調しています。

ここは RAG と重なる部分もあります。過去の会話、ユーザーの好み、過去の判断を検索して取り出すなら、リトリーバルの延長です。

ただし、何を覚えるか、何を忘れるか、限られたコンテキストに何を詰めるかは、単なる検索品質だけではなく状態管理に近い話です。少し前からよく言われている context engineering もこの周辺だと思います。

## プランニングとマルチエージェント

Andrew Ng は、エージェントの代表的な設計パターンとして Reflection、Tool use、Planning、Multi-agent collaboration を挙げています。

IBM の7つにもシステム設計やサブエージェントの話はありますが、タスク分解、自己改善、複数エージェントの協調をパターンとして学ぶ切り口は Ng のほうがわかりやすいです。

特に Planning と Multi-agent collaboration は、なんとなく作るとすぐ複雑になります。どこまで分解するか、どのエージェントに何を任せるか、誰が最終判断するかを設計しないと、ただ遅くてデバッグしにくい仕組みになります。

## そもそもエージェントを作るべきか

Anthropic の [Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents) は、この点がかなり現実的です。

最初から複雑なエージェントフレームワークを使うのではなく、シンプルな構成から始める。必要に応じて、Prompt Chaining、Routing、Parallelization、Orchestrator-Worker、Evaluator-Optimizer のようなワークフローを使う。さらに必要になって初めて自律的なエージェントに進む。

単純なワークフローで済むなら、エージェントにしないほうが安定します。AI エージェントを作るスキルには、「作らない判断」も含まれています。

## 自律度と人間の関与

Karpathy の Software 3.0 の話で自分に一番刺さったのは、エージェントを「全部自動でやるもの」と見ないことです。

現時点の LLM はかなり強いですが、常に信頼できるわけではありません。だから、ユーザーが確認しやすく、必要に応じて手綱を短くできる設計が必要になります。Karpathy は autonomy slider という言い方をしています。

これは IBM のプロダクト思考や OpenAI の人間へのエスカレーション設計ともつながります。

# 他の一次情報で補うとどう見えるか

IBM の7つに対して、他の情報源がどこを補っているかを短く整理します。

- **Karpathy**: Software 3.0、部分的な自律、autonomy slider。完全自律ではなく、人間が確認しやすい設計を重視する
- **Andrew Ng**: Reflection、Tool use、Planning、Multi-agent collaboration。エージェント設計を共通語彙で語れるようにする
- **Anthropic**: まずシンプルに作る。複雑なフレームワークや自律エージェントにすぐ飛びつかない
- **OpenAI**: エージェント分割とガードレール。安全性チェック、人間へのエスカレーション、出力検証を設計に含める
- **Google**: models、tools、orchestration、memory、evaluation。Logs、Traces、Metrics、LLM-as-a-Judge、Human-in-the-Loop を具体化する
- **Chip Huyen**: AI Engineering 全体の地図。データセット、ファインチューニング、推論最適化、評価設計まで広く扱う

情報源ごとに強調点は違いますが、重ねるとだいたい同じところに集まります。

まず、アーキテクチャ、ツール設計、評価です。IBM、OpenAI、Anthropic、Google、Ng、Huyen のどれを見ても、この3つは形を変えて出てきます。

特に一番強く重なっているのは評価です。デモと本番の差は、モデルの賢さだけでは埋まりません。どのケースで成功し、どのケースで失敗し、なぜ失敗したかを見えるようにする必要があります。

# 自分ならこの順番で学ぶ

これから AI エージェントを作るなら、全部を一気に学ぶより、次の順番が良さそうです。

## 1. 評価とログを最初に置く

まず、入力、出力、ツール呼び出し、失敗理由を記録する。小さいプロトタイプでも、成功例と失敗例を残しておく。これがないと、後から改善できません。

## 2. ツール設計を固める

次に、ツールの入出力を明確にする。エージェントが使うツールは、説明文、スキーマ、エラー、権限が仕様です。ここが曖昧だと、モデルの性能以前に事故ります。

## 3. アーキテクチャを小さく始める

最初から複雑なマルチエージェントにしない。Prompt Chaining や Routing で済むならそれで済ませる。どうしても必要なら Orchestrator-Worker や自律エージェントに進む。

## 4. 必要に応じて RAG、memory、planning を深める

情報検索が重要なら RAG を深める。継続的な利用が重要なら memory と context engineering を考える。複雑なタスク分解が必要なら planning や multi-agent collaboration を学ぶ。

# まとめ

IBM の7スキルは、AI エージェント構築の良い出発点です。

特に、システム設計、ツール設計、信頼性、セキュリティ、評価、プロダクト思考まで含めているので、「プロンプトを書ける」と「エージェントを作れる」は違う、ということがよくわかります。

ただし、IBM の指標だけだと、メモリとコンテキスト管理、プランニングとマルチエージェント、そもそも作るべきかの判断、自律度と人間の関与といった視点は薄めです。

突き詰めると、AI エージェント構築のスキルとは、信頼しきれない部品を、観測できて、制御できて、人間が必要なところで判断できるシステムに変える能力なのだと思います。

まだ自分の中でも整理中ですが、少なくとも「評価とオブザーバビリティを後回しにしない」は、かなり確度の高い学びとして残りました。

# 参考

- IBM Technology「The 7 Skills You Need to Build AI Agents」: https://www.youtube.com/watch?v=mtiOK2QG9Q0
- Andrej Karpathy「Software 3.0」(YC AI Startup School 2025): https://www.latent.space/p/s3
- Andrew Ng「Agentic Design Patterns」(The Batch): https://www.deeplearning.ai/the-batch/issue-241/
- Anthropic「Building Effective Agents」: https://www.anthropic.com/engineering/building-effective-agents
- OpenAI「A Practical Guide to Building Agents」: https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf
- Google「Agents」ホワイトペーパー: https://www.kaggle.com/whitepaper-agents
- Google「5-Day AI Agents Intensive Course with Google」: https://www.kaggle.com/learn-guide/5-day-agents
- Chip Huyen『AI Engineering』(O'Reilly, 2025): https://www.oreilly.com/library/view/ai-engineering/9781098166298/
