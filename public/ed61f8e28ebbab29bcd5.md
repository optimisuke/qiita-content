---
title: ChromeOSでArduino
tags:
  - Arduino
  - ChromeOS
  - Chromebook
private: false
updated_at: '2023-09-27T08:58:25+09:00'
id: ed61f8e28ebbab29bcd5
organization_url_name: null
slide: false
ignorePublish: false
---
普段、家ではChromeOSを使ってる。
ちょっとしたプログラミングや電子工作にWindows PCを出すのがめんどくさくて、ChromeOSで環境を整えた。
特に特別なことはしてないけども。

# 事前調査
ChromeOSでは、ローカルでArduino環境つくれなさそう。
Linux入れたらできるんやろけども。ChromeOSにLinux入れたら負けやと思ってる。

# Arduino Create
いつのまにか、Arduinoもクラウド上での開発環境用意してた。
これでいっか。
[Arduino Create](https://create.arduino.cc/)

![IMG_2199.png](https://qiita-image-store.s3.amazonaws.com/0/184221/2933bccf-3f04-0ea7-7d58-2dd6d06323a5.png)

WindowsとMacとLinuxのプラグイン？があったので、Linuxのダウンロードしてみたけど、インストール難しそうやった。残念。
![Screenshot 2018-06-28 at 23.37.55.png](https://qiita-image-store.s3.amazonaws.com/0/184221/04652f65-fa53-a1b3-f4b3-04ce89bbdafe.png)

有料だけど、あきらめて、Chromeの拡張をインストール
![image.png](https://qiita-image-store.s3.amazonaws.com/0/184221/bf9ab2bb-813f-8edc-84d3-27c01f3514fa.png)

97円/月。安い。無料よりは高いけど。
<img width="375" alt="IMG_2198.png" src="https://qiita-image-store.s3.amazonaws.com/0/184221/f9d2fd5f-b9b5-2858-6e74-88aca88c9765.png">

アクセス。
![Arduino Editor.png](https://qiita-image-store.s3.amazonaws.com/0/184221/45439ad6-5dce-5047-aeeb-d46934d01c12.png)

開けた。Arduino Unoを接続。Lチカ。
![Arduino Editor (1).png](https://qiita-image-store.s3.amazonaws.com/0/184221/1139a5dc-b81b-8fe1-1f41-99d596184443.png)

シリアル通信もできた。
![Arduino Editor (2).png](https://qiita-image-store.s3.amazonaws.com/0/184221/ac373ee2-679a-79a6-e543-25625a7d3edd.png)

Fritzing的な回路図もかける。
![Arduino Editor (3).png](https://qiita-image-store.s3.amazonaws.com/0/184221/74102b49-d6a1-e373-bf77-92c3770b409c.png)


ちゃんとしたのも。
![Arduino Editor (4).png](https://qiita-image-store.s3.amazonaws.com/0/184221/8b01ca76-6b30-20fa-b701-1e496a4cb40d.png)

Shareもできる。

色も変えれる。
![Arduino Editor (5).png](https://qiita-image-store.s3.amazonaws.com/0/184221/ade57b56-809e-f765-22c0-b642fd089300.png)


# まとめ
ChromeOSすごい。Arduinoすごい。
mbedもすごいけどねー。
