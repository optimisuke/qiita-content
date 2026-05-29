---
title: Playwright で字幕付きのデモ動画を、動画編集なしで撮る
tags:
  - 動画
  - AI
  - IBM
  - Playwright
  - IBMBob
private: false
updated_at: '2026-05-29T23:31:14+09:00'
id: 3f551cc6f4f2c880c222
organization_url_name: ibm
slide: false
ignorePublish: false
---

# はじめに

デモ動画の撮影、地味につらいんですよね。

画面操作しながらセリフを考えて、字幕をつけるために動画編集ソフトを開いて、使ってる WEB サービスの画面が変わったらまた最初からやり直して…。

先日、IBM Bob（IBM の AI コーディングエージェント）に「このサービスのブラウザ操作デモ動画を作って」と頼んだら、これを全部まとめて解決するアプローチを提案してくれました。やっていること自体は素の Playwright なので、Bob を使っていなくても同じことができます。この記事ではその手法を共有します。

> IBM Bob は無料評価版もあります。
> https://www.ibm.com/jp-ja/products/ai-coding-agent

実際にできあがったのがこれです。Playwright がブラウザを操作しながら、画面下に字幕が出ています。これを録画したものがそのまま完成品です。

![字幕付きデモ動画の例](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/8262d8bb-e8d7-4308-b23f-22dbc4edc632.gif)

# やっていること

頼んだのは「Playwright でブラウザ操作のデモ動画を撮って」というシンプルなものです。返ってきたのは大きく3つでした。

1. デモのストーリーボード（どのシーンで何を見せるか）
2. Playwright スクリプト（実際に動くブラウザ操作コード）
3. 字幕の仕組み（字幕を HTML 要素としてページに注入する）

3 番目が肝です。

# ブラウザに字幕を直接貼り付ける

字幕を動画編集で後付けするのではなく、操作中のページの DOM に `position: fixed` な要素を差し込んで、**録画と字幕表示を同時に**やってしまうアプローチです。

```javascript
async function showSubtitle(page, text, duration = 2000) {
  await page.evaluate((subtitleText) => {
    document.getElementById("demo-subtitle")?.remove();

    const subtitle = document.createElement("div");
    subtitle.id = "demo-subtitle";
    subtitle.textContent = subtitleText;
    subtitle.style.cssText = `
      position: fixed;
      bottom: 50px;
      left: 50%;
      transform: translateX(-50%);
      background: rgba(0, 0, 0, 0.8);
      color: white;
      padding: 15px 30px;
      border-radius: 8px;
      font-size: 24px;
      z-index: 10000;
    `;
    document.body.appendChild(subtitle);
  }, text);

  await page.waitForTimeout(duration);
}
```

あとはデモのシーンごとに、字幕を出してから操作する、を繰り返すだけです。

```javascript
await showSubtitle(page, "Lumina Search にアクセスします", 2000);
await page.goto("http://localhost:3000");

await showSubtitle(page, "検索ワードを入力: Playwright", 3000);
await page.locator('input[name="q"]').fill("Playwright", { delay: 100 });

await showSubtitle(page, "検索を実行します", 1000);
await page.locator('input[name="q"]').press("Enter");
```

録画は Playwright の `recordVideo` オプションに任せます。ブラウザ操作と字幕が一緒に webm として保存されます。

```javascript
const context = await browser.newContext({
  viewport: { width: 1280, height: 720 },
  recordVideo: { dir: "./videos/", size: { width: 1280, height: 720 } },
});
```

動画編集ソフトは一度も開きません。

# デモ対象は使い捨てのサイトを用意した

ちなみに上の例の `localhost:3000` は、実在のサービスではなく Bob にその場で作ってもらった「Lumina Search」という Google 風の検索サイトです。

最初は本物の Google でデモを撮ろうとしたんですが、実在のサービスを bot で自動操作するのはあまりよくなさそうだなと思い直して、デモ用のサイトを1枚用意してもらいました。こういうサイトを気軽に作っちゃえるのもAI時代って感じですよね。

# 何がうれしいか

**動画編集が不要になる**

字幕は DOM 要素なので、字幕入りの画面がそのまま録画に映ります。文言を変えたければスクリプトの文字列を直すだけ。編集ソフトを行き来しなくてよくなります。

**撮り直しがとにかく楽**

デモ対象の画面が変わっても、セレクタや待ち時間を微修正して再実行するだけです。「直す → 実行 → 完成」のループが短い。

これが地味にありがたくて、デモ動画って一度作ると更新が億劫になって、古い画面のまま使い続けがちなんですよね。そのつらさがかなり和らぎます。

ちなみに今回は、Bob が VS Code 上でスクリプトを書いて `npm run demo` で実行する、というところまで一気にやってくれました。

![Bob がデモスクリプトを実行している様子](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/85847543-26b6-487f-bc36-89e924d212be.gif)

# おわりに

字幕を DOM に貼って録画と同時に済ませる、というアイデア自体はシンプルで、知ってしまえば「そりゃそうか」なんですが、自分では思いついていませんでした。

Playwright さえ動かせれば Bob がなくても再現できます。デモ動画をよく撮る人は、一度試してみると編集の手間がまるごと消えるかもしれません。
