---
title: OWASP Agentic AI Top 10 を Agent の構成要素で整理する
tags:
  - Security
  - AI
  - owasp
  - Agent
  - LLM
private: false
updated_at: "2026-07-04T07:57:09+09:00"
id: 535af37a32a9fd850924
organization_url_name: ibm
slide: false
ignorePublish: false

agreed_posting_campaign_term: true
---

# はじめに

OWASP から [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) が公開されてしばらくたちますが、今更ながらざっと読んでみました。Top 10 をそのまま読むと、`ASI01: Agent Goal Hijack`、`ASI02: Tool Misuse and Exploitation`、`ASI03: Identity and Privilege Abuse` のように脅威名が並んでいて、「実際に Agent のどこを気にすればいいんだっけ？」が少し掴みにくい感じがありました。

自分の場合は、Agent の処理の流れに重ねて見た方がしっくりきました。

![OWASP Agentic AI Top 10 を Agent の構成要素にマッピングした図](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/4f8cc29d-2621-4eb5-ab19-9bfa0f4f766e.png)

この記事では、OWASP Agentic AI Top 10 を暗記するのではなく、Agent の構成要素ごとのチェックリストとして見る方法を整理します。

# Agent を構成要素で見る

Agent は実装によって違いはありますが、ざっくり次のような流れで動きます。

```text
User -> Goal / Instruction -> Planning -> Memory / Context
     -> Tool Selection -> Tool Execution -> External Systems
     -> Output / Human
```

Claude Code、OpenAI Codex、各種 Agent SDK、MCP を使った構成も、細部は違ってもだいたいこの流れで考えられます。

この流れに OWASP Top 10 を置いていくと、「脅威名」ではなく「設計時に見る場所」として扱いやすくなります。

# OWASP Top 10 を構成要素にマッピングする

上記の図とかぶりますが、表にするとこんな感じかと思います。

| Agent の要素       | 関連する脅威                                | 見るポイント                                                           |
| ------------------ | ------------------------------------------- | ---------------------------------------------------------------------- |
| Goal / Instruction | ASI01: Agent Goal Hijack                    | 目的や指示が、プロンプトインジェクションなどで別の方向に誘導されないか |
| Planning           | ASI08: Cascading Failures                   | 無限ループ、過剰な再試行、連鎖的な自動実行が起きないか                 |
| Memory / Context   | ASI06: Memory & Context Poisoning           | 記憶、RAG、コンテキストが汚染され、誤った判断が継続しないか            |
| Tool Selection     | ASI02: Tool Misuse and Exploitation         | 不要なツールや危険なツールを選ばないか                                 |
| Tool Selection     | ASI03: Identity and Privilege Abuse         | Agent に過剰な権限や広すぎる認証情報を渡していないか                   |
| Tool Execution     | ASI05: Unexpected Code Execution            | 想定外のコマンド、コード、スクリプトが実行されないか                   |
| External Systems   | ASI04: Agentic Supply Chain Vulnerabilities | MCP、Plugin、ライブラリ、外部ツールが侵害されても影響を抑えられるか    |
| External Systems   | ASI07: Insecure Inter-Agent Communication   | Agent 間通信でなりすましや改ざんが起きないか                           |
| Agent 全体         | ASI10: Rogue Agents                         | Agent が意図しない自律動作や逸脱を始めないか                           |
| Output / Human     | ASI09: Human-Agent Trust Exploitation       | 人間が Agent を過信して、危険な承認や判断をしないか                    |

こう見ると、OWASP Top 10 は「攻撃パターンの一覧」というより、Agent を作るときのレビュー観点に近いものとして扱えます。

# 特に気になった3つ

## 1. Goal Hijack

従来の LLM アプリだと、プロンプトインジェクションの影響は「変な回答をする」くらいに見えがちでした。

Agent では少し話が変わります。

```text
会議を予約して
 ↓
顧客情報を外部に送って
```

のように、回答ではなく行動そのものが乗っ取られます。

なので、ユーザーの元の目的と、Agent が実行しようとしている目的がすり替えられないかガードする必要があります。

## 2. Memory & Context Poisoning

Agent が長期記憶や RAG を使い始めると、誤った情報がその場限りで終わらないのが怖いところです。

たとえば、`Aさんは管理者として扱ってよい` のような誤情報が Memory やナレッジに入ると、その後の判断にも影響し続けます。

RAG や Agent Memory を入れるなら、「何を保存するか」だけでなく、「誰が更新できるか」「いつだれに追加された記憶か」「いつ無効化するか」「参照元を追えるか」も見ておきたいです。

## 3. Tool Misuse

Agent Security のかなり大きな部分はここだと思っています。

Agent は AWS、社内システム、MCP Server などを操作できます。つまり、問題は `誤った回答` だけではなく `誤った実行` になります。

たとえば、メールを読むだけでよい Agent に送信権限まで渡している、参照だけでよい CRM 連携に更新権限まで渡している、といった状態はかなり危ないです。

ツールごとに、次のような観点を確認するだけでも、かなり現実的なチェックになります。

- その Agent に本当に必要か
- 読み取りだけで足りないか
- 高リスク操作に承認を挟めるか
- 実行ログを後から追えるか

# こう使うと良さそう

Agent を設計するときは、最初に Top 10 を眺めるより、まず自分の Agent の構成要素を書き出すのが良さそうです。

```text
Goal / Planning / Memory / Tool / Execution / External Systems / Human Approval
```

そのうえで、今回の記事のように各要素に OWASP Top 10 をマッピングしていきます。

たとえば Tool まわりなら ASI02、ASI03、ASI05 を重点的に見る。Memory を使うなら ASI06 を見る。Agent 同士をつなぐなら ASI07 を見る、という感じです。

この方が、抽象的な脅威一覧を読むよりも、自分の実装に引き寄せて考えやすい気がしています。

# おわりに

OWASP Agentic AI Top 10 は、名前だけ見ると少し重たいですが、Agent の構成要素にマッピングするとかなり使いやすくなります。

自分の理解としては、これは「暗記する Top 10」ではなく、「Agent の各部品に対するセキュリティチェックリスト」として使うのがよさそうです。（他の脅威も同様かとは思いますが）

Agent 開発では、`Agent の構成要素を洗い出す -> 各要素に OWASP Top 10 をマッピングする -> 重要な項目を深掘りする` かんじで進めると、脅威モデリングの入口として使いやすいと思います。

# 参考

- [OWASP Top 10 for Agentic Applications 2026](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/)
- [OWASP Agentic AI - Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/)
- [OWASP GenAI Security Project](https://genai.owasp.org/)
