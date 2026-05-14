---
title: IBM watsonx Orchestrate にPyPIで公開されているPythonのMCPサーバーを登録する方法
tags:
  - Python
  - IBM
  - MCP
  - watsonxOrchestrate
  - AIエージェント
private: false
updated_at: '2025-07-19T22:17:53+09:00'
id: 05fa38ac70ac2c7bead8
organization_url_name: ibm
slide: false
ignorePublish: false
---
## 概要

IBM watsonx OrchestrateはIBMが提供するAI Agentプラットフォームです。watsonx OrchestrateはMCPツールとしてPythonランタイムもサポートしていますが、2025/07時点では公式ドキュメントに詳細を見つけられませんでした。試行錯誤の結果、uvxを使用することで簡単にPythonで公開されているMCPサーバーをツールとして登録できることがわかりました。

## 背景

[IBM watsonx Orchestrateの公式ドキュメント](https://developer.watson-orchestrate.ibm.com/tools/toolkits)では、ランタイムとしてNodeとPythonの両方がサポートされていることが明記されています。しかし、Pythonでの具体的な利用方法については詳細が不足していました。

## NodeのMCPサーバー利用方法（参考）

Nodeで作成されたMCPサーバーの場合は、[`npx`](https://docs.npmjs.com/cli/v10/commands/npx)を使用して簡単にMCPサーバーを利用できます。

```bash
npx <package-name>
```

npxの動作原理：
1. ローカルにpackageがあるかチェック
2. なければリモートからダウンロード
3. 実行

## PythonでのMCPサーバー利用方法

Pythonで同様の動作を実現するには、**uvx**を使用します。uvxは最近よく見るパッケージ管理ツールの[uv](https://docs.astral.sh/uv/)の付属的なコマンドで、[`uv tool run`](https://docs.astral.sh/uv/guides/tools/#running-tools)のエイリアスです。uvxはPyPIからパッケージを取得し、npxと同様の機能を提供します。

### 使用例：mcp-server-fetch

今回はAnthropicが提供している[mcp-server-fetch](https://pypi.org/project/mcp-server-fetch/)を使用しました。このパッケージはPyPI上で公開されており、uvxで直接実行可能です。

### 設定方法

watsonx Orchestrateのツール設定画面で、以下のようなコマンドを設定するだけです：

```bash
uvx mcp-server-fetch
```

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/0ac8994e-c006-40ba-a5c6-efd71072e6af.png)

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/9edf583c-f3c8-4f83-b6a4-32e601bb8fed.png)


参考までに、以下コマンドでは動きませんでした。

```bash
python -m mcp_server_fetch
```
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/531c623e-9741-44f7-8f1e-5912e6348d18.png)

## まとめ

- watsonx Orchestrate はMCPサーバーとしてPythonランタイムをサポートしているが、公式ドキュメントに詳細記載なし
- `npx`（Node）と同様に`uvx`（Python）を使用することで、PyPI上のMCPサーバーを簡単に登録・実行可能
- npxとuvxの両方が使えることで、NPMとPyPIにあるMCPサーバーを利用でき、選択肢が大幅に広がる
- これにより、watsonx Orchestrateでできることが飛躍的に増加

## 参考リンク

**IBM watsonx Orchestrate**
- [IBM watsonx Orchestrate Tools Documentation](https://developer.watson-orchestrate.ibm.com/tools/toolkits)

**MCP Server**
- [mcp-server-fetch on PyPI](https://pypi.org/project/mcp-server-fetch/)

**uv / uvx**
- [uv Documentation](https://docs.astral.sh/uv/)
- [uv GitHub Repository](https://github.com/astral-sh/uv)
- [Running Tools with uv](https://docs.astral.sh/uv/guides/tools/#running-tools)

**Node.js**
- [npx Documentation](https://docs.npmjs.com/cli/v10/commands/npx)
