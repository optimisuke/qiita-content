---
title: 量子コンピュータで迷子にならないために ソフトウェア編
tags:
  - 量子コンピュータ
  - 量子コンピューター
  - QISKIT
  - 量子計算
  - 量子ソフトウェア
private: false
updated_at: '2026-07-04T17:50:59+09:00'
id: 0f15c893b6c7ebbd8ba9
organization_url_name: ibm
slide: false
ignorePublish: false
posting_campaign_uuid: 783b7a849caf11eefd91
agreed_posting_campaign_term: true
---

# はじめに

前回は、量子アルゴリズムについて整理しました。

https://qiita.com/optimisuke/items/6d351c55e6a76d3fdc17

今回は第5回として、**量子ソフトウェア**を見ていきます。

とはいえ、このあたりはまだ自分も正直あまり追えていません。

なので、Qiskit の細かい API には入らずに、Hello World と、ざっくりしたアーキテクチャだけ置いておきます。ここは実際に触りながら、あとで直します。

![software.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/184221/f995f95a-ba60-42e7-8d9f-8fbf8d26b1b2.png)

# Qiskit の Hello World

Qiskit は、量子回路を書いて、最適化して、シミュレータや実機で実行するためのオープンソース SDK です。

ベル状態を作るだけなら、雰囲気はこんな感じです。

```python
from qiskit import QuantumCircuit

qc = QuantumCircuit(2)
qc.h(0)
qc.cx(0, 1)
```

これで、2 qubit の回路を作り、1つ目に H ゲートをかけて、CNOT でもつれを作るところまで書けます。

ただし、これはまだ「回路を書いた」だけです。

実際に動かすには、シミュレータで試すのか、実機に投げるのか、測定結果を見るのか、期待値を見るのか、といった話が続きます。が、ここでは省略します。

# いろんなレイヤーがある

Qiskit まわりは、量子回路だけじゃなく、いろんなレイヤーの道具があるようです。

![Qiskit のレイヤー図](https://www.ibm.com/quantum/_next/static/media/qiskit-explore-MD.11gpi31sc6ymf.png)

出典: https://www.ibm.com/quantum/qiskit

| レイヤー           | ざっくり何をするか  |
| ------------------ | ------------------- |
| 回路を書く         | `QuantumCircuit`    |
| 状態や演算子を見る | `quantum_info`      |
| 実機向けに変換する | transpiler          |
| 実行する           | primitives、Runtime |
| 上位の部品を使う   | addons、Functions   |
| GUI で触る         | Composer            |

このうち primitives は、量子回路を実行するときの入口です。

| primitive   | ざっくり何をするか                             | 例                             |
| ----------- | ---------------------------------------------- | ------------------------------ |
| `Sampler`   | 回路を何回も実行して、測定結果の分布を見る     | `00` が何回、`11` が何回出たか |
| `Estimator` | 回路で作った状態に対して、観測量の期待値を見る | エネルギーや相関の値を推定する |

最初は `Sampler = 測定結果を見る`、`Estimator = 期待値を見る` と分けます。

処理の流れとしては、公式ドキュメントの Qiskit patterns が参考になります。

```text
Map       問題を量子回路や演算子に写す
Optimize  実行に向けて最適化する
Execute   primitive で実行する
Analyze   結果を解析する
```

# 参考

https://www.ibm.com/quantum/qiskit
https://quantum.cloud.ibm.com/docs/en/guides
https://github.com/Qiskit/qiskit

# おわりに

今回は、Qiskit をかなり軽く眺めました。正直、あまり触れてないので近いうちに、もうちょい触ろうとおもいます。
次は、ビジネス編として、PoC、ポスト量子暗号、材料探索、時間軸あたりを整理する予定です。
