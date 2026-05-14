---
title: HTML+JavaScript on Chromeでtoggl APIを叩く
tags:
  - HTML
  - JavaScript
  - WebAPI
  - toggl
  - Mesh
private: false
updated_at: '2023-09-27T08:59:46+09:00'
id: d35dadee9d05bd95d35f
organization_url_name: null
slide: false
ignorePublish: false
---
# 概要
MESHでtoggl APIを叩きたかったんだけど、デバッグが大変やったので、ローカルでHTMLとJavaScriptに処理書いてChrome使って確認した。
Allow-Control-Allow-Originっての設定するのと、ajaxの細かいお作法？があったので、結構つまづいた。試行錯誤しながらがんばった。

# toggl API
toggl APIの詳細は下記サイト参照。詳しく書いてある。
[toggl_api_docs/toggl_api.md at master · toggl/toggl_api_docs · GitHub](https://github.com/toggl/toggl_api_docs/blob/master/toggl_api.md)

tokenはtogglの設定から見れる。一番した。プロジェクトのIDは下の画面みたいなところでURLみたらわかる。
![Desktop screenshot.png](https://qiita-image-store.s3.amazonaws.com/0/184221/770b12c6-95cf-f7fb-2bfb-f3d3f0543209.png)



## curlで確認
こんな感じで確認。
hogehogeのところにapi_tokenの文字列入れてfugafugaのところにプロジェクトIDの数字をいれる。

```
curl -u hogehoge:api_token -H "Content-Type: application/json" -d '{"time_entry":{"description":"Meeting with possible clients","tags":[],"pid":fugafuga,"created_with":"curl"}}' -X POST https://www.toggl.com/api/v8/time_entries/start
```

## 事前準備
ローカルでやると、「デベロッパーツール」に
No 'Access-Control-Allow-Origin' header is present on chrome
っていうエラーが出て動かない。

ここ見ると、
[jquery - Why does my JavaScript get a "No 'Access-Control-Allow-Origin' header is present on the requested resource" error when Postman does not? - Stack Overflow](https://stackoverflow.com/questions/20035101/why-does-my-javascript-get-a-no-access-control-allow-origin-header-is-present)

ローカルでやるためには、こんなんを入れてあげないといけないっぽい。
[Allow-Control-Allow-Origin: * - Chrome ウェブストア](https://chrome.google.com/webstore/detail/allow-control-allow-origi/nlfbmbojpeacfghkpbjhddihlkkiljbi)

## HTML+JavaScript
こんな感じ。これをローカルにおいてChromeで開くだけで、togglのタイマーがスタート。凄くシンプル。ベーシック認証やら、JSON.Stringifyやら、色々調べて試行錯誤した結果こうなった。調べると他にも方法ありそうやけど、なんか試した感じだとこれ以外動かなかった。
console.logをlogに置き換えて、$.ajaxをajaxにしてMESHで動かした。

```html:ajax_simple.html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>jQuery test</title>
  </head>
  <body>
    Body!!
    <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1/jquery.min.js"></script>
    <script type="text/javascript">
      var username = 'hogehoge';
      var password = 'api_token';

      function make_base_auth(user, password) {
      var tok = user + ':' + password;
      var hash = btoa(tok);
      return "Basic " + hash;
      }

      $.ajax
      ({
      type: "POST",
      url : "https://www.toggl.com/api/v8/time_entries/start",
      contentType : "application/json",
      data : JSON.stringify({
      "time_entry" : {
      "description" : "description",
      "tags" : [],
      "pid" : fugafuga,
      "created_with" : "test",
      }
      }),
      beforeSend: function (xhr){ 
      xhr.setRequestHeader("Authorization", make_base_auth(username, password)); 
      },
      timeout : 5000,
      success: function (){
      console.log("Post success"); 
      },
      error : function ( request, errorMessage ) {
      console.log("Network error");
      }
      });
    </script>
  </body>
</html>
```

API叩いたときの返事は、Chromeの「デベロッパーツール」の「Network」の「Response」を見たら帰ってきたjson形式のデータを見れる。デバッグもデベロッパーツール使えば、MESH使うより色々情報見れる。
こんな感じ。
![ajax.png](https://qiita-image-store.s3.amazonaws.com/0/184221/f5e98e01-8db2-755a-bd6d-9c0513f6ef2e.png)

# MESH
こっち参照。
[MESH動きタグとtogglで時間管理 - Qiita](https://qiita.com/iton/items/30a517eae3da41d58023)
