---
title: golangでsliceを関数に渡すときの挙動
tags:
  - Go
  - slice
private: false
updated_at: '2021-05-30T14:53:31+09:00'
id: 4fadac6db80e027db07a
organization_url_name: acall
slide: false
ignorePublish: false
---
# はじめに
golangでserial通信のread, writeをしたくて、それのテストするために、mockを使って動きを模擬してた。

serialは、これを使った。
[tarm/serial](https://github.com/tarm/serial)

readは、適当にメモリを確保して、`[]byte`で渡して、そこにデータが書き込まれるC言語的な感じの挙動。
サンプルコードはこんな感じ。

```go
package main

import (
        "log"

        "github.com/tarm/serial"
)

func main() {
        c := &serial.Config{Name: "COM45", Baud: 115200}
        s, err := serial.OpenPort(c)
        if err != nil {
                log.Fatal(err)
        }
        
        n, err := s.Write([]byte("test"))
        if err != nil {
                log.Fatal(err)
        }
        
        buf := make([]byte, 128)
        n, err = s.Read(buf)
        if err != nil {
                log.Fatal(err)
        }
        log.Printf("%q", buf[:n])
}
```

どうやってmock作ったらいいんやろうと思って、いろいろ試してた。mock内でもらったsliceが指してるアドレスの値を書き換えたい。sliceを理解しないと前に進めないことがわかったので、調べつつ、手を動かして挙動を確認した。

# sliceを関数にわたすときの挙動
sliceをわたして、1. `=`でまるっと代入する場合と、2. 要素ごとに代入する場合と、3. `copy()`使う場合を確認した。2. 要素ごとに代入する場合と、3. `copy()`を使う場合に関数呼び出す側のsliceも上書きされた。
わからんでもない挙動。sliceは実体はarrayでそれを参照してるから、まるっと代入するときは、あたらしくarrayを用意して参照先をかえちゃってる感じか（アドレスを渡してる感じ）。要素ごとに代入するときと`copy()`を使うときは参照先のarrayに値を入れてる感じか。

https://play.golang.org/p/WFZJaxomXqg

```go
package main

import (
	"fmt"
)

func test1(hoge []int) {
	foo := []int{-1,-2,-3}
	hoge = foo
	fmt.Printf("hoge1: %v\n", hoge)
}

func test2(hoge []int) {
	hoge[0] = -10
	hoge[1] = -20
	hoge[2] = -30
	fmt.Printf("hoge2: %v\n", hoge)
}

func test3(hoge []int) {
	foo := []int{-100,-200,-300}
	copy(hoge, foo)
	fmt.Printf("hoge3: %v\n", hoge)
}

func main() {
	fuga := []int{1, 2, 3}
	fmt.Printf("fuga0: %v\n", fuga)
	test1(fuga)
	fmt.Printf("fuga1: %v\n", fuga)
	test2(fuga)
	fmt.Printf("fuga2: %v\n", fuga)
	test3(fuga)
	fmt.Printf("fuga3: %v\n", fuga)
}
```

```
fuga0: [1 2 3]
hoge1: [-1 -2 -3]
fuga1: [1 2 3]
hoge2: [-10 -20 -30]
fuga2: [-10 -20 -30]
hoge3: [-100 -200 -300]
fuga3: [-100 -200 -300]
```

# おわりに
あんまり、深入りしたくないけど、やりたいことはできるようになった。sliceあなおそろしや。

# 参考
ここが詳しかった。日本語情報ありがたや。
[GolangのSliceを関数の引数に渡した時の挙動 - Carpe Diem](https://christina04.hatenablog.com/entry/2017/09/26/190000)
