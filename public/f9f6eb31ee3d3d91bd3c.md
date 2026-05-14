---
title: Raspberry PiでChromiumOS
tags:
  - RaspberryPi
  - Chromium
  - ChromeOS
private: false
updated_at: '2023-09-27T09:04:08+09:00'
id: f9f6eb31ee3d3d91bd3c
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに
がまんできなくなって、Raspberry PiにChromiumOS入れてみた。

# 調べもの
調べてると、FlintOSてのがChromiumOSのクローン？みたいだけど、買収されたみたいで別のgithubアカウントで公開されてた。買収したneverwareってとこのcloudreadyってのも使えるのかもしんないけど、情報が少ないので、githubに公開されているFlintOSが派生？したFydeOSを使ってみることにした。2019/02/13現在で用意されてる新しいイメージを使った。ビルド不要。以下、いろいろリンク先。

[Flint OS - Your future operating system](https://flintos.io/)
[Neverware to Acquire Flint Innovations, Creators of Flint OS — Neverware](https://www.neverware.com/pressrelease-03-06-2018)
[Neverware](https://www.neverware.com/#cloudready-introduction)
[Chrome OS（Chromium OS）を使ってみた - uepon日々の備忘録](https://uepon.hatenadiary.com/entry/2018/08/15/000024)
[GitHub - flintinnovations/overlay-rpi: Chromium OS portage overlay for Raspberry Pi](https://github.com/flintinnovations/overlay-rpi)
[GitHub - FydeOS/chromium_os_for_raspberry_pi: Build your Chromium OS for Raspberry Pi 3B/3B+](https://github.com/FydeOS/chromium_os_for_raspberry_pi)

# したこと
下記サイトにしたがって、

1. imageのダウンロード
2. 7-zipインストール
3. 解凍
4. sdカードのフォーマット
5. 書き込み
6. 電源ON・設定
7. なんどか、電源ON/OFF

をした。
わりとすんなり、動いたけれど、なぜか設定してしばらくは安定せずに電源ON/OFFを繰り返した。

[Installing operating system images - Raspberry Pi Documentation](https://www.raspberrypi.org/documentation/installation/installing-images/)
[7-Zip](https://www.7-zip.org/)
[SDメモリカードフォーマッター for Windows Download - SD Association](https://www.sdcard.org/jp/downloads/formatter_4/eula_windows/index.html)
[balenaEtcher - Home](https://www.balena.io/etcher/)

# うごいているところ
こんな感じ。
![image.png](https://qiita-image-store.s3.amazonaws.com/0/184221/18fb39dd-2fa2-5f65-f532-e9a1af413c88.png)
![image.png](https://qiita-image-store.s3.amazonaws.com/0/184221/b2a1aa14-000f-394e-648b-4537770a9cc4.png)

## うごいたもの
- youtube (ちょっと止まったりした。WiFiのせいかもしれん。)
- play music
- google photo

ほかにも、ブラウザで動く系は大丈夫そう。

## うごかなかったもの
- crostini
- Play Store

がんばったら動くのかもしれないけども。

# おわりに
ずっとやりたかったけど、動いても使わんやろなと思ってたので、すっきりした。たぶん、使わないやろうけども・・・。
Chromebookも持ってるけど、遜色ない。
タブ、どれくらい開けるかは見てないけども。
ChromeOS検討してて、ラズパイ持ってる人は、試してもいいかも。

