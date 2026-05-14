---
title: よくあるスイッチにダイソーのBluetoothボタンをつけてスマホやラズパイとBluetooth接続する方法
tags:
  - RaspberryPi
  - bluetooth
  - IoT
  - MacroDroid
private: false
updated_at: '2020-02-20T21:57:08+09:00'
id: cdd06eaf1b11f0e910cf
organization_url_name: acall
slide: false
ignorePublish: false
---
# 概要
ダイソー（100均）で買ったBluetoothボタンとコーナン（ホームセンター）で買ったスイッチを使ってIoTトグルボタンを作った。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/87b55173-9d6c-5e6d-687a-54fa5130a2ce.png)

# はじめに
押すときだけオンになるボタン型のスイッチじゃなくて、オンオフを切り替える「トグル」スイッチを使ってIoT的なことをしたいなと思って、作ってみた。具体的には、仕事とプライベートの「オン・オフ」の切り替えスイッチ（物理的なボタンでオンオフすることで気持ちを切り替えて、さらにログを取るIoTスイッチ）を作りたかったので、試してみた。

安く作りたかったのと、ESP32とかで作るのはあんまり面白くないなと思ったので、ダイソーのBluetoothを改造することにした。この改造の前に、ダイソーのBluetoothボタンをスマホとかラズパイ経由でインターネットにアクセスしてAPI叩けることは確認済み。
[IoTボタンを安く手に入れてノーコードでWeb APIを叩く方法 - Qiita](https://qiita.com/optimisuke/items/a9f4dc65559a7a5eadef)

# スイッチ
パナソニックの「両切りスイッチ」を使った。
[スイッチ | フルカラー配線器具 | スイッチ・コンセント | 電設資材 | Panasonic](https://www2.panasonic.biz/ls/densetsu/haisen/switch_concent/fullcolor/switch.html)

両切りスイッチはコンセントの2線とも別々に切ってくれる。「片切りスイッチ」（こっちのほうが安い）でできないか考えてみたけど、難しそうやった。
[【スイッチ】片切りと両切りの違いについて](https://detail-infomation.com/one-off-switch-double-pole-switch/)

# 回路
両切りスイッチは、2線分のスイッチが内蔵されているので、それを利用した。
Bluetoothボタンは、改造してる人がいたので、その情報とかを参考にした。
[ダイソーでスマホ用Bluetoothリモートシャッターを発見→分解→ちょい改造：ウェブ情報実験室 - Engadget 日本版](https://japanese.engadget.com/2017/10/26/bluetooth/?guccounter=1&guce_referrer=aHR0cHM6Ly9xaWl0YS5jb20v&guce_referrer_sig=AQAAABP0a3_MCR0sEHMQ3w_DNBbgZHHxEZ86h0M_u3LmRdhGfVxi6HxHlr8lFYC44jJWRiMgEmZyEtMofTBa-wEv6DheFYiKYkTE1JDqks-MJiJF_yXxQO0Y83cJs64sKWuwDwiOmEcQuCndrtebLXEqq8B0HSABSatECKhIjM9N-l9X)

Bluetoothボタンにはオン用の信号とオフ用の信号を入力する。
片方は、スイッチがオンのときに電圧がHIGHになるような回路、もう片方は、スイッチがオンのときに電圧がLOWになるような回路を実装。

最終的に、こんな感じ。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/93a4a672-888a-b5c0-f8bc-7c01acd0529e.png)

回路眺めたり、テスターあてたりして確認すると、もともとついてたスイッチは、押すとグランドに接続（LOW）されて、離すとオープン（HIGH）。ICの内部にプルアップ抵抗がありそう。
片方は、もとの回路と同じように、入力ピンとグランドの間にスイッチを接続。
もう片方は、入力ピンをプルダウン抵抗を挟んでグランドに接続。あと、スイッチを挟んで電源ピンに接続。抵抗は、はじめ20kΩにしてたけど、動作が安定してない気がしたので、並列にもう一つつないで、10kΩにした。20kΩから10kΩにした意味があったかは若干怪しい。

こんな感じになった。空中配線。ケースに入ってるボタン電池をそのまま使いたかったから、ちゃんと閉まるようにスペースあいてるところに実装した。配線出すところは、ケースを一部削った。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/d8f5c555-2e33-e010-5acd-bff5aa3ad643.png)

久しぶりだったのと、太めのハンダゴテしかなかったので、時間かかった。線材も硬いやつしかなかったので、扱いづらかった。もっと道具と材料ほしくなる。あと、はんだ付けスキルもっと上げたい。


# スマホとの接続
MacroDroidってのを使った。ここらへん、参照。自分で書いたやつやけども。
[IoTボタンを安く手に入れてノーコードでWeb APIを叩く方法 - Qiita](https://qiita.com/optimisuke/items/a9f4dc65559a7a5eadef)

### 注意点
押しっぱなしになるので、MacroDroid上で、普通の`Volume Up`、`Volume Down`じゃなくて、`Volume Up - Long Press`、`Volume Down - Long Press`をトリガーにして動くようにする必要がある。

それと、押しっぱなしが終わったときに認識されるので、逆の方をトリガーにする必要がある。

# ラズパイとの接続
ラズパイとつなぐときは、スイッチのオンオフの瞬間に、状態が変わったよ信号（押したよ信号と離したよ信号）が送られてるみたいなので、それを取得すれば、割と簡単にいろいろできそう。

# ケース
ダイソーでかったケースに穴をあけた。

### ピンバイスで穴をあけて
（ミニ四駆で「肉抜き」してた知識が役に立った。）
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/508e116b-df0e-5a75-d459-a245d8dc3905.png)

### ニッパーで切り取って、カッターで削って
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/813cc332-22ac-e397-32d9-1dd5092725dd.png)

### はめて
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/ec741df5-e59b-21cb-706f-a7a8c0e23515.png)

### ふたする
（あいてるスペースには、息子くんの積み木をはさんで固定。）
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/87b55173-9d6c-5e6d-687a-54fa5130a2ce.png)

# おわりに
結構めんどくさかった。ソフトウェアでやれば一瞬でできそうなことを、無駄にアナログでやった感じ。Bluetoothボタンはソフトウェア書き換えられないらしい。途中、はんだ付けがうまくいかなくて、「ESP32でやればよかったかなぁ・・・」と思った。
でも、作り終わって息子くんに試してもらったら、無限オンオフが楽しいのか、すごく喜んでくれた。とても満足。
