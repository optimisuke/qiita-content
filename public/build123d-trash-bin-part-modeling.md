---
title: 寸法を伝えるだけで IBM Bob が壊れたゴミ箱のパーツをモデリングしてくれた
tags:
  - Python
  - CAD
  - 3Dプリンター
  - IBMBob
  - build123d
private: false
updated_at: '2026-07-05T20:46:09+09:00'
id: 5d04b8ccd704e88f978e
organization_url_name: ibm
slide: false
ignorePublish: false
posting_campaign_uuid: 783b7a849caf11eefd91
agreed_posting_campaign_term: true
---

# はじめに

机の上に置いている小さいゴミ箱の、底を支えるパーツが壊れました。。。

![koware.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/fdaa5ca8-6c78-4fa0-987e-9aecf0a796c1.jpeg)

まだ使えるけど、パーツ単体では売っていないしなぁ、と思っていたのですが最近「これも3Dプリントできるんじゃね？」となりました。

「ハンマーを持つ人には、全てが釘に見える」と言いますが、3Dプリンターを持っていると壊れたパーツが全部「印刷できるやつ」に見えてきます。そして AI コーディングエージェント を常用していると、「これもAIでできるんじゃね？」と思うようになってきます。良いかどうかは一旦おいといて。

![holding-hammer.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/f5413f37-6f64-448f-92e1-f15b427f5274.png)

（ハンマーを持ったBobくん）

さて壊れたゴミ箱のパーツはシンプルな円盤＋円筒リングの組み合わせだったので、ノギスで寸法を測って、普段コードを書くときに使っている IBM Bob（IBM の エンタープライズ向けAIコーディング・エージェント）に伝えたら、そのままモデリングコードを出してくれました。寸法を伝えるだけで済んだのでむちゃ楽でした。あっさり完成したので、流れをまとめておきます。

# 壊れたパーツの形状

形状はシンプルです。

- 円盤（底面）：直径 125.0mm、厚さ 1.7mm
- 円筒リング：内径 101.5mm、肉厚 1.7mm、高さ 16.0mm
- 円筒は円盤の中央に同心円で乗っている

ノギスで測ってBob に伝えました

# 生成されたコード

Bob が出力した `model.py` はこんな感じです。build123dっての使ってます。

```python
from build123d import Cylinder, Pos, export_gltf, export_step, export_stl

# --- 寸法定数 ---
DISC_DIAMETER   = 125.0   # 円盤の直径 [mm]
DISC_THICKNESS  = 1.7     # 円盤の厚さ [mm]

RING_INNER_DIA  = 101.5   # 円筒の内径 [mm]
RING_WALL       = 1.7     # 円筒の肉厚 [mm]
RING_HEIGHT     = 16.0    # 円筒の高さ [mm]

DISC_RADIUS  = DISC_DIAMETER / 2
RING_INNER_R = RING_INNER_DIA / 2
RING_OUTER_R = RING_INNER_R + RING_WALL

# 円盤
disc = Pos(0, 0, DISC_THICKNESS / 2) * Cylinder(radius=DISC_RADIUS, height=DISC_THICKNESS)

# 円筒（外側ソリッド - 内側くり抜き）
ring_z = DISC_THICKNESS + RING_HEIGHT / 2
ring_outer = Pos(0, 0, ring_z) * Cylinder(radius=RING_OUTER_R, height=RING_HEIGHT)
ring_inner = Pos(0, 0, ring_z) * Cylinder(radius=RING_INNER_R, height=RING_HEIGHT + 0.01)
ring = ring_outer - ring_inner

model = disc + ring

export_stl(model, "./trash_bin_base/model.stl")
export_step(model, "./trash_bin_base/model.step")
export_gltf(model, "./trash_bin_base/model.glb", binary=True)
```

寸法が上部の定数にまとめてくれたので、「ちょっと大きめにしたい」と思ったときも数字を変えるだけです。

中空の円筒リングをブール演算（外側ソリッド - 内側くり抜き）で作っているのが3Dモデリングらしい書き方で、コードを読んでいても形状が想像しやすくて良い感じです。

https://github.com/gumyr/build123d

# 実行する

`uvx` があればいい感じに動きます。

```bash
uvx --from build123d python trash_bin_base/model.py
```

実行すると3Dプリンタ用の STL ファイルが出力されます。ビューアで開いて確認したところ、意図通りでした。

STL をスライサーに読み込んで印刷してあっという間に完成です。

![model.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/c6c7c3b6-71c8-40f6-9464-27d922575b27.png)

![3dprinted.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/b23e3bb3-dfe2-4f5b-9e17-f9c5eafbb85a.jpeg)

![use.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/043c5c69-a23f-4159-b560-e4fddbfd7e00.jpeg)

# build123d スキル

AIコーディングエージェントには「スキル」という仕組みがあります。SKILL.md というファイルに用途・使い方・注意点等を書いておくと、関連する作業のときに自動で読み込まれます。

今回は `build123d` スキルを事前に用意しておきましたが、スキルがなくても「build123d で作って」と言えばいい感じに作ってくれます。スキルに実行方法や書き方の例を入れておくと精度が上がります。3Dモデリングだけじゃないですが、一度うまくいったらその内容を抽象化してスキルに落とし込んでおくのが良いと思います。かなり時短になります。

スキルはプロジェクトルートに `.bob/skills/` フォルダを作って SKILL.md を置くだけで使えます。

# おわりに

「壊れたパーツを測って寸法を伝える」だけでモデリングとコード生成が完結したのが、とても快適です。

ノギスと3Dプリンターがある人は、ぜひ AI コーディングエージェントと組み合わせて試してみてください。

---

_この記事も IBM Bob と一緒に書きました。_
