---
title: チャットだけじゃない生成AIのインターフェース（IBM watsonx.ai）
tags:
  - 生成AI
  - LLM
  - watsonx
  - watsonx.ai
private: false
updated_at: '2024-03-22T23:46:06+09:00'
id: 9ee47fc31e5bdc79cec5
organization_url_name: ibm
slide: false
ignorePublish: false
---
# はじめに
生成AIのインターフェースといえば、ChatGPTのようなチャットUIか、API呼び出しがメインかなと思いますが、他にもユースケースに合わせたインターフェースがどんどん出てきてる気がします。
例えば、IBMの生成AIプラットフォームのwatsonx.aiでは、チャットインターフェースも含めて3つのインターフェースがあります。ここでは、簡単にその3つを紹介したいと思います。

# チャットインターフェース
チャットインターフェースはこんな感じです。ChatGPT等、よくあるインターフェースです。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/4d874bf1-4b76-52ba-07b8-ab5fa9d46163.png)

# 構造化インターフェース
構造化された指示を出すときは、こんなインターフェースもあります。
「命令」と「例」の「入力」・「出力」と「試行」の「入力」を記入して結果を取得します。
いわゆるfew-shotとして、例を渡すことで精度が上がります。
また、複数の入力を入れることで、並列に結果を取得することができます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/c2ca37da-84a1-e19e-8aa5-b1655748f3ae.png)

# フリー・フォーム
最後は、フリーフォームということで、エディタのような画面で、プロンプトを記載し、プロンプトに続けて結果が記載されます。生成後、生成結果が編集可能な状態になって、さらに「生成」することも可能です。自由度が高いです。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/f5a075c2-55f1-d45d-f866-d9b7bc9cdf8e.png)

# おわりに
あっさりですが、チャットインターフェースを含む3つのインターフェースを紹介しました。
生成AIといえばチャットインターフェースではありますが、状況に応じて違うインターフェースを選択することで、より生成AIを活用できると思います。
