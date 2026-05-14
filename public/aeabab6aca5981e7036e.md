---
title: IBM watsonx Orchestrateを使ったファイルアップロードツールの作成
tags:
  - Python
  - IBM
  - watsonxOrchestrate
  - AIエージェント
private: false
updated_at: '2025-08-05T09:24:08+09:00'
id: aeabab6aca5981e7036e
organization_url_name: ibm
slide: false
ignorePublish: false
---
# はじめに

IBM watsonx Orchestrateでは、AI Agentが呼べるツールをPythonで作成することができます。また、`bytes`型のパラメータを使用することで、ユーザーがファイルをアップロードできるツールを作成できます。この記事では、CSVファイルをアップロードして内容を表示する簡単なツールの作成方法を紹介します。

## 前提条件

開発環境は構築済みとします。環境構築については以下の公式ドキュメント等を参照してください

https://developer.watson-orchestrate.ibm.com/getting_started/installing

## ファイルアップロードツールの作成

### ツールのコード実装

`bytes`型のパラメータを定義することで、watsonx OrchestrateのUIにアップロードボタンが自動的に表示されます。

```python
from ibm_watsonx_orchestrate.agent_builder.tools import tool, ToolPermission

@tool(name="process_uploaded_file", description="Allow the user upload the CSV file from chat and take that in as bytes to process it", permission=ToolPermission.ADMIN)
def process_uploaded_file(file_bytes: bytes) -> str:
    """
    Processes the uploaded CSV file and returns the content as a string.
    :param file_bytes: The bytes of the uploaded CSV file.
    :returns text: A string containing the CSV file content.
    """
    # Convert bytes to string (UTF-8)
    file_content = file_bytes.decode('utf-8')
    
    return file_content
```

このコードのポイント：
- **`file_bytes: bytes`**: パラメータを`bytes`型で定義することで、UIにファイルアップロード機能が追加される
- **UTF-8 デコード**: アップロードされたファイルのバイト列をテキストに変換

### ツールの登録

作成したツールを登録します：

```bash
orchestrate tools import -k python -f upload_csv.py
```

## Agentの設定

Agentに以下のBehaviorを設定します：

```
分析を頼まれたら、質問はせずに、まずは必ず process_uploaded_file ツールを呼び出して。ファイルをアップロードしてもらうために。ファイル内にデータが入ってるから。process_uploaded_fileの出力はmarkdown tableで。
```

このBehaviorにより以下の機能が利用可能になります。

- ユーザーが分析を依頼すると自動的にファイルアップロードツールが呼び出される
- LLMが出力をMarkdownテーブル形式に整形してUIを見やすくする

## 動作確認

### テスト用CSVファイル

以下の内容でCSVファイル（例：`hoge.csv`）を作成します：

```csv
a,b,c
1,2,3
あ,い,う
```

### 実際の動作


ユーザーの入力に従って`Add file`ボタンが表示され、ファイルのアップロードUIが表示され処理が開始されます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/e7260834-582f-4925-8e0f-2b1014048b04.png)


## まとめ

この記事では、IBM watsonx Orchestrateでファイルアップロード機能を持つツールの作成方法を紹介しました。

**重要なポイント：**
1. **`bytes`型パラメータ**: ツールの引数を`bytes`型で定義するとアップロードボタンが自動生成される
2. **LLMによる整形**: AgentのBehaviorでMarkdown出力を指定することで、見やすい表形式で表示される
3. **シンプルな実装**: 基本的なファイル処理は数行のコードで実現可能

:::note warn
watsonx Orchestrateの仕様は今後変更される可能性があります。最新の情報については公式ドキュメントを参照してください。
:::

この基本的なファイルアップロード機能を応用して、より高度なデータ分析ツールやファイル処理機能を持つAgentを構築することができます。
