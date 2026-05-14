---
title: IBM Bob の MCP設定にあるAPIトークンを環境変数に分離してGitで共有する
tags:
  - IBM
  - MCP
  - IBMBob
private: false
updated_at: '2026-05-14T10:40:59+09:00'
id: 3ff5cc91f5cd73eeccd3
organization_url_name: ibm
slide: false
ignorePublish: false
---
## はじめに

IBM BobでMCPサーバーを使うとき、APIトークンを `mcp.json` に直接書くのはセキュリティ的に気になります。特にGitで管理しているプロジェクトだと、うっかりコミットしてしまいそうで怖いです。

シンプルな解決策として `mcp.json` を `.gitignore` に追加する方法もあります。ただそれだと、「どのMCPサーバーを使うか」という設定自体もチームで共有できなくなってしまいます。

トークンだけを外に出して、設定ファイル自体はGitで共有する——そのための方法が `${env:変数名}` の形式で環境変数を参照することです。やってみたら意外とシンプルだったので整理しておきます。なお、本記事はmacOS環境を前提にしています。

## 設定方法

### 1. 環境変数を設定する

`.zshrc` に環境変数を追加します。

```bash
export MONDAY_API_TOKEN="your_api_token_here"
```

保存したら反映します。

```bash
source ~/.zshrc
```

設定できたか確認します。

```bash
echo $MONDAY_API_TOKEN
```

### 2. mcp.json で環境変数を参照する

ここでは例としてMonday.com MCPを使いますが、APIトークンが必要な他のMCPサーバーでも同じ方法が使えます。

`.bob/mcp.json` で `${env:環境変数名}` の形式で参照します。

```json
{
  "mcpServers": {
    "monday": {
      "type": "sse",
      "url": "https://mcp.monday.com/sse",
      "headers": {
        "Authorization": "${env:MONDAY_API_TOKEN}"
      },
      "alwaysAllow": ["get_board_items_page", "search", "get_board_info"]
    }
  }
}
```

`"Authorization": "${env:MONDAY_API_TOKEN}"` の部分がポイントです。

この形だと `mcp.json` をGitにコミットしてもトークンは含まれません。チームメンバーは各自で環境変数を設定すればOKです。

### 3. IBM Bob を完全再起動する

ここが地味に重要です。

**VSCode の「Reload Window」では環境変数が反映されません。**

IBM Bob（アプリ自体）を完全に終了して、もう一度起動する必要があります。VSCodeの拡張機能として動いているので、プロセスレベルで再起動しないとダメです。

macOS の場合:

1. `Command + Q` で IBM Bob を完全終了
2. もう一度 IBM Bob を起動

これで環境変数が読み込まれて、MCPサーバーに接続できるようになります。

## チームで使うとき

新しく参加した方向けに、READMEに設定方法を書いておくと親切です。

````markdown
## MCP設定

以下の環境変数を `.zshrc` に設定してください：

```bash
export MONDAY_API_TOKEN="your_api_token_here"
```

設定後、IBM Bob を再起動してください。
````

## おわりに

環境変数で管理するようにしたら、`mcp.json` をGitにコミットしても安心になりました。

他のMCPサーバーでも同じ方法が使えるので、APIトークンが必要なサーバーを使うときはこの形にしておくと楽だと思います。

間違いや、もっといい方法があれば教えてください。
