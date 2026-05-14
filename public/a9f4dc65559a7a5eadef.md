---
title: 330円から始めるIoTを使ってノーコードでWeb APIを叩く方法
tags:
  - bluetooth
  - IoT
  - toggl
  - integromat
  - MacroDroid
private: false
updated_at: '2023-07-04T15:54:02+09:00'
id: a9f4dc65559a7a5eadef
organization_url_name: null
slide: false
ignorePublish: false
---
# 概要
ダイソーのBluetoothボタンを改造して、スマホ経由でWeb APIを叩く方法を試してみた。ボタンを押したら時間の計測を開始して別のボタン押したら時間の計測を停止するのを作った。スマホにストラップにつけた。MacroDroidってアプリとIntegromatってサービスを使ってTogglで時間トラッキングしてる。結果、動いた。スクリーンオフのときの挙動に不満は残る。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/8f4b064a-6c87-fd9b-31e0-8f630e4cac59.png)


# はじめに
家事とか育児してて、物理的なスイッチを使って時間管理したいと思った。仕事だと、目の前にPCがあるので、それなりに時間管理できるけど、家事とか育児してるといつの間にか時間たつ。スマホでやってもいいのかもやけど、スマホ出すとツイッターとか立ち上げちゃうので、微妙。家事やと手が濡れてたり、子どもいるとスマホとられたりするし。余計時間なくなる。ペンとノート使ってアナログでやってもいいけど、それなりにめんどい。記憶するのは無理。

以前MESHとtogglってサービス使って試しに作ったことあるけど、通信があんまり安定してなくて使わなくなった。MESHは、子どもが食べたりボタン押しまくったりするし。
[MESH動きタグとtogglで時間管理 - Qiita](https://qiita.com/iton/items/30a517eae3da41d58023)

他のIoTボタンも、それなりに値段が高いので買う気にならなかった。

ESP32とかで作ってもいいけど、基板むき出しやと子供いてあぶないし（食べるorなめる）、ケース作るのめんどい。バッテリーも悩ましい。

そんなことを思いながら、先日、ダイソー（100均）で別のもの探してたら、330円でBluetoothリモートシャッターなるものを見つけた。それで何か作れないか試すことにした。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/86ad187f-9acf-e9cb-0653-c2d913deb49f.png)

# 仕様
仕様考えてみた。

- ボタン押したら、時間測り始める。
- もう一つのボタン押したら、時間測るの止める。
（ボタン一つでON/OFFしてもいいけど、状態がわからないので、ボタン二つ使うことにした。）
- インターネットにつないで[Toggl](https://toggl.com/)（タイムトラッキングサービス）とかグーグルカレンダーにログを残す。
- できたら、複数のボタンにそれぞれ仕事内容（育児とか家事とか趣味とか）を割り当てる。

# 調査
2年くらい前に流行ってたらしい。そういえば見たことあったかも。いろんな記事を発見。どう実装するのが良いか調査した。

## Bluetoothリモートシャッター（ダイソー）
### もとの仕様
Bluetoothでペアリングして、離れたところからカメラアプリのシャッターを切るためのもの。
ボタン2つあって、iOS用とAndroid用のボタン。ボタン自体はボリュームアップに対応してるらしい。Androidの方は、ボリュームアップに加えてエンターキー？の信号を送ってるらしい。なぜか。Android用のボタンが使いにくい。

### 回路
ボリュームダウンのピンを見つけてる。すごい。同じように改造したら、動いた。
これで、Android用のボタンも自然に使える。

[ダイソーでスマホ用Bluetoothリモートシャッターを発見→分解→ちょい改造：ウェブ情報実験室 - Engadget 日本版](https://japanese.engadget.com/2017/10/26/bluetooth/)

### ラズパイ（bluebutton）
ラズパイとつないで遊ぶには、Rubyで書かれてるbluebuttonっての使う方法が手っ取り早そう。試してみたら、すんなり動いた。

[わずか300円でIoTボタンを作る方法 - Qiita](https://qiita.com/vimyum/items/8b7548ca8cf45383c5b0)
[Bluetoothシャットダウンボタンを作る #300円でIoTボタン - Qiita](https://qiita.com/nori-dev-akg/items/96584d9591d329f9dcb2)
[raspberry pi と AB Shutter3(bluetoothボタン) の連携 - フクロウ好きなエンジニアのブログ](https://miya15.hatenablog.com/entry/2018/11/04/145905)

### ラズパイ（evdev）
evdevって、HIDデバイスの入力を使うライブラリ使ってる。こっちだと複数のボタンに対応しやすそう。こっちも試してみたらすんなり動いた。

[100均Bluetoothボタンをラズパイに活用](http://jellyware.jp/kurage/raspi/daiso_btshutter.html)

### ESP32
他にも、ESP32につないで、ゲートウェイとして（？）使う人もいるっぽい。結構、難易度高そう。こっちは試す元気なかった。今回の場合、ESP32を使うなら、はじめからESP32で作ったらいい気がする。

[M5StickC(ESP32)でダイソーのBluetoothシャッターを操作 – Lang-ship](https://lang-ship.com/blog/?p=704)
[ダイソー BLE リモートシャッター で SwitchBot を操る - ブログ／こばさんの wakwak 山歩き](http://wakwak-koba.hatenadiary.jp/entry/20181114/p1)

### BLE
BLEのPythonライブラリbluepy使ってBLEとして認識させて動かしてみたかったけど、HID over GATTを自分でなんとかするのは難易度高そうやった。適当なUUIDにWriteしてNotificationを待ってたらいいのかなと思ったけど、できなかった。残念。

[bluepy - a Bluetooth LE interface for Python — bluepy 0.9.11 documentation](https://ianharvey.github.io/bluepy-doc/index.html)

## Android連携
ラズパイでやってもよかったんやけど、屋外でも使いたいし、スマホで楽にできないかなと調べてみた。ラズパイやとなぜかペアリングが安定しなかった。解決難しそうやった。ただ、アプリ作るほどのモチベーションもなかったので、IFTTT的なサービスを探した。

### IFTTT
「Bluetoothデバイスが接続したら」ってトリガーはあるけど、特定のデバイスは無理そうやった。残念。
[Recipe for connecting to specific Bluetooth device? : ifttt](https://www.reddit.com/r/ifttt/comments/2c01cu/recipe_for_connecting_to_specific_bluetooth_device/)

### Tasker
やりたいことできそうやったけど、適当にさわってもうまく使えなかったのであきらめた。
使い方調べるほどの気力がなかった。残念。
[Tasker for Android](https://tasker.joaoapps.com/)

### MacroDroid
Androidの自動化をいろいろできそうなアプリ。特定のBluetoothがつながった場合に、〇〇するって動作を実現できる。ボリュームボタンもトリガーにできる。Triggers、Actions、Constraintsってのがあって、IFTTTよりも柔軟。Constraintsがある分、玄人向けなんかも。サービス連携はあまりなさそう。ツイッターくらい。有料やともっとあるんかな。詳しくは調査してない。これ使うことにした。
[MacroDroid - デバイス自動化 - Google Play のアプリ](https://play.google.com/store/apps/details?id=com.arlosoft.macrodroid)

## Toggl連携
### Zapier
IFTTT的なサービス。Toggl連携あり。ただ、無料枠の制限がきつい。
[Zapier | The easiest way to automate your work](https://zapier.com/home)

### Integromat
Zapierとほぼ同じサービス。Toggl連携あり。無料枠（今のところ）大きめ。これも使うことにした。
[Integromat - The glue of the internet](https://www.integromat.com/en/)

# 構成
こんな感じで接続。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/81735a88-4c27-2bf6-11d8-b3a69bb8cbeb.png)

MacroDroidから、HTTP POSTできなかったので、Toggl APIを直接叩けなかった。
Togglと連携できるのは、調べた感じZapierとIntegromat。IFTTTはできない。
GASとか使ってやる方法もあるやろけど、既存のサービス使ってノーコードでやった。
MacroDroidからIntegromatにはHTTP GET使った。メールとかを経由してやってもできそうやったけど、HTTP GETの方が簡単そうやったので、HTTP GETした。

# 設定
## Bluetoothリモートシャッター（ダイソー）
下記サイトを参考に回路を修正。
[ダイソーでスマホ用Bluetoothリモートシャッターを発見→分解→ちょい改造：ウェブ情報実験室 - Engadget 日本版](https://japanese.engadget.com/2017/10/26/bluetooth/)

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/52b3239d-882e-7db2-2c2b-020156232144.png)


## MacroDroid
Bluetoothボタンが接続したら、スクリーンをオンにする。スクリーンがオフやとボリュームボタンを認識してくれないので、接続時にオンになるようにした。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/3c71a0eb-2ade-6b48-6755-b68dd16af96a.png)

Bluetoothボタンが接続されているときに、ボリュームボタンが押されたらIntegromatにHTTP GETする。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/fe703cc0-e78f-dbb7-f964-2c2f984f2c8d.png)

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/33a5c7de-add3-f32d-5130-3582d82b473a.png)

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/e7566b3b-f31f-eacb-80ec-3bc802a72734.png)

## Integromat
WebhooksにTogglつないだら動く。設定はすごくシンプルなので省略。HELP結構充実してるのでそんなに迷わずできた。

### Togglのエントリーをスタート
ほぼ、つなぐだけ。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/6e7bd537-944c-2a14-d757-4f1291364fa6.png)

### Togglのエントリーをストップ
ストップするときは、カレントエントリーの情報を取得してストップする。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/48e738d2-b492-36f3-85cf-2b67dfbb5301.png)

デフォルトの設定だとエントリーがスタートしてないときストップすると、カレントエントリーがなくてエラーで止まる。なので、エラー無視する。もうちょい、スマートに通知できるといいんやろうけど。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/85dca6b9-f169-b630-62aa-a80075da6703.png)

### その他API
HTTP POSTはこんな感じでできる。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/1dcb2c52-aa04-69a5-e3a7-1a12502a441c.png)

認証のためのブロックもいくつか用意されてる。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/a9a32db9-47c2-a289-ea15-893f7881f4d7.png)

## Toggl
"Profile Settings"ってところに、API Key書かれてる。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/366ad7f4-47cc-160d-9885-c7f93e0e68a9.png)

# 動作手順
こんな感じ。スタートもストップも再接続に5秒くらいかかる。微妙やけど、改善できなそう。

## スタート時
1. いずれかのボタンを押してBluetooth接続→スクリーンオンになることを確認
2. ボタン1を押してTogglスタート

## ストップ時
1. いずれかのボタンを押してBluetooth接続→スクリーンオンになることを確認
2. ボタン2を押してTogglストップ

## 制約
接続が切れていないとき（ボタン押して30秒くらい？）は、スクリーンを自分でオンにしてボタンを押さないと動作しない。微妙やけど、そんなに早く作業を止めない前提。

# おわりに
とりあえず、動いてよかった。他にも応用できそう。改善の余地は大いにあるけど、僕のスキル的に、これ以上は難しそう。

スマホスクリーンオフだと反応しないのは残念。まあ、接続時にオンになるようにできたので、よしとした。

あと、ダイソーのボタンはしばらくするとスリープモードに入る。ボタンを押すとBluetooth接続しなおす。5秒くらいはかかる。それも微妙。再接続後に勝手に信号送信してくれたらいいけど、やってくれない。ボタン内のマイコンのプログラム書き換えれないっぽいので無理そう。ペアリング先は覚えてくれてるけども。すでに売られている製品はそこらへん、うまいことしてんのかな。


# 補足
終盤に、致命的なことに気付いた。スマホスクリーンがオフのとき、反応しない。もっと早く確認すべきやった。検証項目漏れ。HUAWEI P20liteやからかも。残念。Bluetoothの接続はするけど、そのあとトリガーがかからない。なので、接続時にスクリーンオンになるようにした。ちょっと微妙。Androidの設定をいくつか確認したけど、断念。

いちおう、こんなんは見つけた。
[Volume Button trigger when Screen Off - MacroDroid Forums](https://www.tapatalk.com/groups/macrodroid/volume-button-trigger-when-screen-off-t934.html)

ちなみに、スクリーンオフで音楽かけてたら動作した。音楽かかってるときは、ボリュームアップダウンを認識してくれるみたい。でも、ずっと音楽聴くわけにもいかんしなぁ。
