---
title: IBM watsonx Orchestrate ADKでKnowledgeを活用してAgentの自律性を拡張する
tags:
  - IBM
  - ADK
  - watsonxOrchestrate
  - AIAgent
  - AIエージェント
private: false
updated_at: '2025-07-19T20:20:43+09:00'
id: ee6481fbae81c9dc9e0f
organization_url_name: ibm
slide: false
ignorePublish: false
---
## はじめに

IBM watsonx Orchestrate のKnowledge機能は、単純なRAG（Retrieval Augmented Generation）を超えて、Agentに豊富なコンテキストを与えることで自律的な判断と行動の範囲を大幅に拡張できる強力な機能です。
watsonx OrchestrateのAgentは管理画面からもKnowledgeを登録できますが、2025年7月時点ではembeddingモデルの指定ができません。 ADK (Application Development Kit) を使用することでembeddingモデルを選択でき、より柔軟なKnowledge設定が可能になります。
この記事では、ADKを使用してビルトインのMilvusベクターデータベースを活用し、より賢く自律的に動作するAgentを構築する方法を解説します。


https://developer.watson-orchestrate.ibm.com/

## Knowledgeによる自律Agent拡張のコンセプト

watsonx OrchestrateのKnowledge機能は、Agentに対して必要に応じて関連情報を自動的に提供し、より多様で複雑なタスクを自律的に実行できるようにします。従来のRAGが「質問に答える」ことに特化していたのに対し、この仕組みでは「状況に応じて最適な行動を選択する」Agentを実現できます。

## 実装手順

### 1. Knowledgeベースの定義

Agentの自律性を高めるため、多様なコンテキストを提供するKnowledgeベースを構築します。

```yaml
# knowledge.yaml
spec_version: v1
kind: knowledge_base
name: knowledge
description: >
  資料
documents:
  - "/Users/hoge/doc.pdf"
vector_index:
  embeddings_model_name: intfloat/multilingual-e5-large
```

### 2. Knowledgeベースのインポート

orchestrateコマンドを使用してKnowledgeベースをインポートします。

```bash
# Knowledgeベースのインポート
orchestrate knowledge-bases import -f knowledge.yaml

# 出力例
[INFO] - Successfully imported knowledge base 'pm_knowledge'

# インポート状況の確認
orchestrate knowledge-bases list
```

### 3. Agentの定義

Knowledgeベースを活用して自律的に動作するAgentを定義します。
必要に応じて、collaboratorsに他のAgent、toolsにツールを登録します。

```yaml
# agent_knowledge.yaml
spec_version: v1
kind: native
name: agent_knowledge
llm: watsonx/meta-llama/llama-3-2-90b-vision-instruct
style: default
description: >
  Knowledgeに関する質問に回答します。
instructions: >
  ユーザーの質問に関連する情報を knowledgeのdoc.pdf を検索し、日本語で回答してください。
collaborators:
tools:
knowledge_base:
  - knowledge
```

### 4. Agentのインポート

```bash
# Agentのインポート
orchestrate agents import -f agent_knowledge.yaml

# 出力例
[INFO] - Agent 'agent_knowledge' imported successfully

# インポート状況の確認
orchestrate agents list
```

### 5. 実行とテスト

Agentのインポートが完了したら、Webインターフェースやチャット機能を使用して動作を確認できます。
今回は、シンプルにドキュメントの内容に応じて回答してもらいました。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/53663856-4b74-419e-bc04-3927c0b3b00a.png)

## マルチリンガル対応

日本語を含む多言語対応のembeddingモデルとして、例えば以下が利用できます：

- **ibm/granite-embedding-107m-multilingual** - IBMのマルチリンガル対応軽量モデル
- **intfloat/multilingual-e5-large** - オープンなマルチリンガル埋め込みモデル

YAMLのvector_indexセクションでembeddings_model_nameを指定することで利用できます。

## ファイル制限事項

Knowledgeベースにアップロードできるファイルには以下の制限があります：

> A single batch can include up to 20 files, with a total size limit of 30 MB.
The maximum file size for .docx, .pdf, .pptx, and .xlsx files is 25 MB.
The maximum file size for .csv, .html, and .txt files is 5 MB.

## 外部ベクターデータベースの利用

ビルトインのMilvus以外にも、外部のElasticsearchやMilvusインスタンスを利用することも可能です。既存データベースを活用したい場合に有効な選択肢となります。

## まとめ

IBM watsonx Orchestrate ADKのKnowledge機能を活用することで、Agentに豊富なコンテキストを提供し、自律的な判断と行動を可能にできます。企業の既存文書や知識を活用して、より賢いAIアシスタントを簡単に構築できる強力な機能です。

**注意**: この記事は2025年7月時点の仕様に基づいて書かれています。最新の機能や設定方法については、IBM watsonx Orchestrateの公式ドキュメントを参照してください。

## 参考リンク

- [IBM watsonx Orchestrate Knowledge Base構築ガイド](https://developer.watson-orchestrate.ibm.com/knowledge_base/build_kb)
- [IBM watsonx.ai Foundation Models - Embedding](https://dataplatform.cloud.ibm.com/docs/content/wsj/analyze-data/fm-models-embed.html?context=wx&locale=ja)
