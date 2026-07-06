---
title: IBM ELM を OSLC API で操作してみた
tags:
  - Java
  - Elm
  - IBM
  - DoorsNext
  - OSLC
private: false
updated_at: '2026-07-06T13:53:15+09:00'
id: 6be47adb0d3c489fb9df
organization_url_name: ibm
slide: false
ignorePublish: false
posting_campaign_uuid: 783b7a849caf11eefd91
agreed_posting_campaign_term: true
---

# はじめに

IBM ELM（DOORS Next / EWM / ETM）を API 経由で操作したかったので、OSLC という標準プロトコルと Java クライアント SDK（Eclipse Lyo）で色々ためしてみました。最初に知っておけば楽だったことを整理します。

![oslc.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/c70708a0-d232-4da3-a114-6e23dcac25d8.png)

# OSLC と Lyo について

**OSLC**（Open Services for Lifecycle Collaboration）は、IBM ELM などで使われている REST ベースの標準プロトコルです。要件管理や変更管理などのリソースを共通の API で操作できるようにする仕様で、OASIS Open Project が策定しています。

https://open-services.net/

IBM ELM における OSLC の概念については以下のドキュメントも参考になります。

https://www.ibm.com/docs/ja/jfsm/1.1.2.1?topic=services-oslc-concepts

**Eclipse Lyo** は OSLC の Java クライアント SDK で、OSLC リソースの CRUD を Java の Bean と RDF/XML の間でシリアライズ・デシリアライズしてくれます。`OslcQuery` でリソースを検索したり、`OslcClient` で CRUD するあたりは Lyo を使うと楽に書けました。

https://github.com/eclipse-lyo/lyo

# 製品名と OSLC リソース名のギャップ

最初のつまずきは「どのエンドポイントを叩けばいいか分からない」でした。

ELM の製品 UI に表示されている名前と、OSLC 上のリソース名が違います。

| 製品 UI での名前           | OSLC リソース名         |
| -------------------------- | ----------------------- |
| EWM の「Task / Work Item」 | Change Request          |
| DOORS Next の「Artifact」  | Requirement             |
| DOORS Next の「Module」    | Requirement Collection  |
| ETM の「Test Case」        | Test Case（これは同じ） |

UI にある「Work Item」は、API では Change Request. —これを先にしらべておくと、rootservices を辿る手がかりになります。OSLC の世界に入ると製品名は関係なくなる、というのは覚えておくと最初のとっかかりが楽になります。

# OSLC 標準以外の API も使う場面がある

Lyo で CRUD は書けましたが、ELM の機能を活用するには OSLC 標準の外にある API も使う場面がありました。

## EWM のステータス変更

EWM の Work Item のステータス（In Progress → Completed など）を変更しようとしたとき、`rtc_cm:state` プロパティを PUT で変更しようとしたら動きませんでした。

次に `rtc_cm:action` にアクションの URI を渡すという方法を試しましたが、`Unknown attribute` で明確に拒否されました。

調べてみると、正解は **`?_action=<actionId>` というクエリパラメータを付けて PUT する** という方法でした。OSLC のプロパティ操作ではなく、EWM 独自のアクション実行 API です。アクション ID は RDF を手でパースして取得する必要があり、Lyo は関係なかったです。

```
GET <workItemUri>          # ETag を取得
PUT <workItemUri>?_action=<actionId>  # If-Match: <ETag> を付けて PUT
```

## その他、実装してみて気づいたこと

- **モジュール構造の編集** — モジュールへの要件追加・削除は通常の OSLC CRUD ではなく、専用エンドポイント（`/structure`）と独自ヘッダーが必要。OSLC の仕様外の API が製品側にあるケース
- **delete の権限** — Work Item の削除は EWM のプロセス権限設定に依存する。API としては正しく実装できていても、プロジェクトの権限設定次第で 403 になる場合がある
- **totalCount が返ってこない** — OSLC 仕様には `oslc:totalCount` があるが、DOORS Next は実装していないみたい。件数を事前に知る方法がなく、`oslc:nextPage` を辿りながら `hasMore` フラグで代替した

# DOORS Next の configurationUri

DOORS Next には構成管理（stream / baseline）というgit のブランチみたいな概念があり、stream を指定しないと意図しないデータが返ってきます。

さらにクエリはコンポーネント単位になっていて、**別コンポーネントの stream を渡すとエラーではなく 0 件が返ってくる**のが気づきにくかったです。エラーが出ないので原因の特定に時間がかかりました。

DOORS Next を API で触る場合は、最初に components → streams の順で stream URI を取得して、すべての操作に `configurationUri` として渡すのが基本になります。

```
list-rm-components
  -> list-rm-streams(componentUri)
  -> 各 API に configurationUri=<stream URI> を渡す
```

# おわりに

OSLC という標準プロトコルのおかげで CRUD の基本部分は Lyo で楽に書けました。ステータス変更やモジュール操作など ELM 固有の機能については、製品の仕様を把握した上で実装する部分もありますが、それを知っておくと見通しが立てやすくなります。

同じように ELM を API で触ろうとしている人の参考になれば。間違いや補足があればコメントで教えてください。
