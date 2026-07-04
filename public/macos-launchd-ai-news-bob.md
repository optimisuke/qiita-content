---
title: SNSをだらだら見ないためにlaunchdでAIニュース要約を毎朝自動生成する
tags:
  - macOS
  - 自動化
  - launchd
  - AI
private: false
updated_at: "2026-07-04T07:57:09+09:00"
id: 4f75030b85374cdfcb35
organization_url_name: ibm
slide: false
ignorePublish: false

agreed_posting_campaign_term: true
---

# はじめに

AI ニュースは追いたいんですが、スマホのニュースアプリや Twitter/X を見に行くと、つい関係ないものまでだらだら見てしまいます。

ニュースサイトもアプリも SNS も、基本的には「もっと見てもらう」方向に作られています。自分で情報収集しているつもりでも、気づくと流れてくるものを受け身で見ているだけになりがちです。

それを少し避けたくて、自分の好みに寄せたプロンプトで AI ニュース要約を毎朝生成し、それを読んだら他は見ない、という運用を試すことにしました。

情報収集の主導権を、少しこっちに戻すイメージです。

最初は「定期実行といえば cron かな」と思って AI に聞いてみたんですが、macOS だと標準で `launchd` が入っていて、今回の用途にはそっちのほうが良さそうでした。

# やりたいこと

やりたいことはシンプルです。

- 毎日1回、AIニュース要約を Markdown で生成する
- Mac がスリープ中なら、復帰後に実行されればよい
- できるだけ macOS 標準機能だけで済ませる
- `.sh` は作らず、plist だけで済ませる
- 多少の重複や漏れは許容する

今回は `bob` を使っていますが、同じような CLI なら `claude -p` でもできます。

たとえば手動なら、こういう感じです。

```bash
bob --approval-mode=auto_edit -p "24時間以内に発表されたAI についてのニュースについて短く URL つきで整理して" > test.md
```

`claude -p` を使うなら、イメージとしてはこうです。

```bash
claude -p "24時間以内に発表されたAIニュースを短くURLつきで整理して" > test.md
```

この記事の例では、Volta 配下の `$HOME/.volta/bin/bob` を使っています。ここは自分の環境の `bob` や `claude` のパスに置き換えてください。

# launchd を使う

macOS 標準で定期実行するなら `launchd` を使います。

Linux サーバーの感覚だと cron が先に浮かびますが、macOS では `launchd` が標準の仕組みとして用意されています。ログイン時に動かしたり、指定時刻に動かしたりする用途なら、まずはこれを見るのが良さそうです。

今回使ったのはこの2つです。

- `RunAtLoad`
- `StartCalendarInterval`

`RunAtLoad` はログイン時や LaunchAgent の読み込み時に実行されます。

`StartCalendarInterval` は指定時刻に実行されます。指定時刻に Mac がスリープしていた場合は、次に起きたタイミングで1回実行されます。数日スリープしていた場合も、復帰時に何回もまとめて実行されるのではなく、1回に寄せられるようです。

自分の用途では、これで十分でした。Mac を自動で起こしてまで実行したいわけではありません。

# 最小の plist

`~/Library/LaunchAgents/com.example.ai-news.plist` に置きます。

細かいログ設定やプロンプトはあとから育てる前提で、まずは最小にするとこんな感じです。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
 "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>Label</key>
    <string>com.example.ai-news</string>

    <key>ProgramArguments</key>
    <array>
      <string>/bin/zsh</string>
      <string>-lc</string>
      <string>cd "$HOME/ai-news" &amp;&amp; "$HOME/.volta/bin/bob" --approval-mode=auto_edit -p "24時間以内に発表されたAIニュースを短くURLつきで整理して" &gt; "$HOME/ai-news/news/$(date '+%Y-%m-%d_%H%M')_ai-news.md"</string>
    </array>

    <key>RunAtLoad</key>
    <true/>

    <key>StartCalendarInterval</key>
    <dict>
      <key>Hour</key>
      <integer>7</integer>
      <key>Minute</key>
      <integer>0</integer>
    </dict>
  </dict>
</plist>
```

XML の中なので、`&&` は `&amp;&amp;`、`>` は `&gt;` にしています。

`claude -p` を使う場合は、コマンド部分をたとえばこう置き換えます。

```bash
claude -p "24時間以内に発表されたAIニュースを短くURLつきで整理して"
```

# プロンプトを育てる

最初は「24時間以内のAIニュースを短くURLつきで整理して」くらいで十分だと思います。

ただ、自分の場合はしばらく試して、こういう方向に寄せたくなりました。

- URL は本文中に散らさず、最後にまとめる
- テーマごとに分ける
- 「事実」と「見られる意見・論点」を分ける
- 初回や前回実行時刻が不明な場合は、過去1週間を上限にする
- `news/` にある過去の md ファイル名や timestamp も見て、前回実行を推定する

ニュースアプリや SNS は、基本的には向こうの都合で並びます。ここを自分の読みたい形に寄せていけるのが、この運用のよさだと思っています。

# 登録する

保存先を作ります。

```bash
mkdir -p ~/Library/LaunchAgents ~/ai-news/news
```

plist を配置して登録します。

```bash
cp ~/ai-news/launchd/com.example.ai-news.plist ~/Library/LaunchAgents/
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.example.ai-news.plist
launchctl enable gui/$(id -u)/com.example.ai-news
```

すぐ試すなら `kickstart` します。

```bash
launchctl kickstart -k gui/$(id -u)/com.example.ai-news
```

`Could not find service "com.example.ai-news"` が出る場合は、まだ `bootstrap` されていません。先に登録が必要です。

# 確認する

生成されるファイルはこの形です。

```text
~/ai-news/news/YYYY-MM-DD_HHMM_ai-news.md
```

登録状態はこれで確認できます。

```bash
launchctl print gui/$(id -u)/com.example.ai-news
```

標準の GUI で LaunchAgent 一覧をきれいに見る画面は、あまりなさそうでした。システム設定のログイン項目に一部出ることはありますが、確実に見るなら CLI がよさそうです。

# 止める・消す

止めるだけなら `bootout` します。

```bash
launchctl bootout gui/$(id -u) ~/Library/LaunchAgents/com.example.ai-news.plist
```

削除する場合は、止めてから plist を消します。

```bash
launchctl bootout gui/$(id -u) ~/Library/LaunchAgents/com.example.ai-news.plist
rm ~/Library/LaunchAgents/com.example.ai-news.plist
```

# おわりに

まだ運用し始めたばかりですが、「ニュースを見に行く」のではなく「自分向けに整形されたニュースだけ読む」形にすると、だらだら情報を浴びる時間を減らせそうな気がしています。

もちろん、これで網羅的に追えるわけではありません。多少の重複や漏れはあります。ただ、自分の場合は完璧なニュース収集よりも、受け身で SNS を見続けないことのほうが大事でした。

しばらく使ってみて、プロンプトや出力形式を調整していくつもりです。
