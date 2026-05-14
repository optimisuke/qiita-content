---
title: 誰でも簡単に既存データソース（RDB/NoSQL/REST API）から GraphQL API を構築：StepZen 活用法
tags:
  - nosql
  - RDB
  - REST-API
  - GraphQL
  - StepZen
private: false
updated_at: '2023-07-04T15:53:24+09:00'
id: 396a2ebfb26afbb8e53a
organization_url_name: ibm
slide: false
ignorePublish: false
---
# はじめに

この記事では、複数の既存データソースを統合し、一つの GraphQL API として提供する StepZen の基本的な使用法について解説します。公式ドキュメントは詳細な情報を提供していますが、一部分かりにくい部分もあるため、それらを整理して説明します。詳細については公式ドキュメント（[StepZen Product Documentation](https://stepzen.com/docs)）を確認ください。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/74643c90-adbf-b2fb-2a77-ca2a4833a2e2.png)

# StepZen とは

簡単に・宣言的に・高いパフォーマンスで、既存データソース（RDB/NoSQL/REST API等）をGraphQL API化するマネージドサービスです。

公式ドキュメント（[StepZen](https://stepzen.com/)）には下記記載があります。

> GraphQL-as-a-Service: Build GraphQL faster, run better, scale seamlessly.
> Build GraphQL Easily
Optimize & Scale GraphQL Automatically
The only declarative approach for federated access to data

![Screenshot 2023-07-04 at 10.54.07.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/1c2516d1-d67c-0e47-400a-2d118e045f36.png)

上記説明だけでは、イメージしづらいと思いますので、以下で他のサービスとの違いや構成要素、使い方を説明していきます。

# 他のサービスとの違い

## [StepZen](https://stepzen.com/)

StepZen は、複数の既存データソース（REST API、データベースなど）を一つの GraphQL API に統合します。これにより、複雑なバックエンドロジックを簡素化し、フロントエンドの開発を効率化します。

## [Apollo GraphOS](https://www.apollographql.com/docs/graphos/)

Apollo GraphOS は、異なる GraphQL サービスを一つの API に統合する、GraphQL Federation の構築をサポートします。各サービスが独立して進化する一方で、フロントエンドからは一つの API として扱うことができます。

> OSSのApolloライブラリは今回、比較対象にはせずに有償サービスのApollo GraphOSと比較しました

## [AWS AppSync](https://aws.amazon.com/jp/appsync/)

AWS AppSync は AWS の各種サービスと深く統合され、リアルタイムのデータ同期やオフラインユーザーのサポートなど、高度な機能を提供しながら GraphQL サーバーを構築します。

## [HASURA](https://hasura.io/)

HASURA は、データベーススキーマを起点にして、直感的な GUI を通じて短時間で高性能な GraphQL API を構築します。リアルタイムのデータ同期や権限管理などの高度な機能も提供します。

# StepZen の構成要素

StepZen の主要な構成要素は以下の 3 つです。

## 1. StepZen GraphQL Server
既存のデータソースを GraphQL 化するためのサーバー。構成ファイルに従ってデータソースとの連携を行い、GraphQL API を提供します。


![Screenshot 2023-07-04 at 10.53.31.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/e7a0c405-6153-7f57-22ed-e55a2f4baafa.png)


## 2. StepZen Dashboard
API の管理や運用を行うためのダッシュボード。API の呼び出し数やレスポンス時間を確認でき、運用の見通しを立てるのに役立ちます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/e17ff9bd-4ba8-7ce4-3c3f-0841af7c3a58.png)

## 3. StepZen CLI
コマンドラインから StepZen の操作を行うためのツール。構成ファイルの生成やデプロイを行うことができます。`npm install -g stepzen`でインストールできます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/46c82ecb-ad50-8b7e-b36c-23f085950ae6.png)

# StepZen の基本

StepZen の基本的な流れは以下の通りです。
![Screenshot 2023-07-04 at 10.26.57.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/82f7c7bb-5aaa-3193-9bb8-bee828d80824.png)

## 1. 構成ファイルの生成

既存のデータリソースからStepZen CLIを用いて構成ファイルを生成します。この構成ファイルには、データソースとの接続情報や GraphQL スキーマが含まれます。必要に応じて生成された`index.graphql`を修正してAPIをカスタマイズします。

## 2. デプロイ

構成ファイルを元にStepZen CLIを用いて、StepZen GraphQL Server をデプロイします。これにより、様々なデータソースが一つの GraphQL API として提供されるようになります。

## 3. 運用

StepZen Dashboard を使用して API の運用状況を確認します。呼び出し回数やレスポンス時間など、API のパフォーマンスを維持し、改善するための情報が得られます。

# StepZen の応用

StepZen は、StepZenが定義している下記GraphQL Directivesを用いて、構成ファイル（`index.graphql`）を修正することで、APIのカスタマイズも可能になっています。

- `@sdl`：複数の`.graphql` ファイルを統合して、大規模な GraphQL API を構築することが可能となります。これにより、複数の開発者がそれぞれの領域に集中して作業することができます。
- `@dbquery`：このディレクティブを使用すると、SQL 文を直接記述して詳細なデータの取得や操作を行うことができます。これにより、複雑なクエリやデータ操作が可能となります。
- `@materializer`：このディレクティブを使うことで、データを階層化して扱うことができます。これにより、複雑なデータ構造を簡単に表現することができます。


他にも色々なGraphQL Directivesがあるので公式ドキュメント（[GraphQL Directives Reference @rest @dbquery @graphql @materializer](https://stepzen.com/docs/custom-graphql-directives/directives)）を参照ください。

# おわりに

本記事では、StepZen の基本的な使用法と応用的な使用法について説明しました。StepZen は、既存のデータリソースを一つの GraphQL API に統合することで、フロントエンドの開発を大幅に効率化します。そして、基本的な使用法だけでなく、`@materializer`、`@dbquery`、`@sdl` といった高度なディレクティブを用いることで、より柔軟で強力な API の構築が可能となります。

公式ドキュメントに沿って実際に手を動かしてみることで、より深い理解が得られるでしょう。これらの機能を使いこなせば、StepZen を通じてデータ管理が一段と容易になるはずです。まずは小規模なプロジェクトから始めてみることをおすすめします。
