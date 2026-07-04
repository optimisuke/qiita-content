---
title: IBM BobでCOBOL TODOアプリを作ってみた
tags:
  - プログラミング
  - CLI
  - cobol
  - AI
  - IBM
private: false
updated_at: "2026-07-04T11:48:21+09:00"
id: f9e62f4ce988bf71b016
organization_url_name: ibm
slide: false
ignorePublish: false
posting_campaign_uuid: 783b7a849caf11eefd91
agreed_posting_campaign_term: true
---

## はじめに

COBOLは1959年に誕生した、**60年以上の歴史を持つプログラミング言語**です。今でも銀行や保険会社のシステムで現役で動いています。

自分で書くのは難しそうだけど、読むのはできそう。そう思って、**IBM Bob（AIコーディングアシスタント）** にCOBOLでTODOアプリを書いてもらい、実際に動かして読んでみることにしました。

## 環境構築

MacでGnuCOBOLをインストールします：

```bash
$ brew install gnu-cobol
```

GnuCOBOLは、オープンソースのCOBOLコンパイラです。無料で使えて、macOS、Linux、Windowsで動作します。

## 完成したもの

GitHubリポジトリ:

https://github.com/optimisuke/hello-cobol

シンプルなCLI TODOアプリです。メニュー形式で操作し、データは`todos.dat`ファイルに保存されます。日本語にも対応しています。

### コンパイルと実行

```bash
$ cobc -x -free TODO-APP.cob -o TODO-APP
$ ./TODO-APP
```

`-free`オプションで自由形式（列位置を気にしない）で書けます。

### 実際の使用例

```bash
$ ./TODO-APP

================================
   COBOL TODO アプリケーション
================================

--- メニュー ---
1. TODOを追加
2. TODO一覧を表示
3. TODOを完了にする
4. TODOを削除
5. 終了

選択してください (1-5): 1
TODOの内容を入力してください: 買い物に行く
TODOを追加しました！ (ID: 0001)

選択してください (1-5): 2
--- TODO一覧 ---
ID:  0001 | [未完了] | 買い物に行く
```

## IBM Bobとの開発体験

### コード生成

「COBOLでTODOアプリを作って」と依頼すると、基本構造を持ったコードを生成してくれました。

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. TODO-APP.

ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT TODO-FILE ASSIGN TO "todos.dat"
        ORGANIZATION IS LINE SEQUENTIAL
        FILE STATUS IS WS-FILE-STATUS.
```

### バグ修正のサポート

開発中、無限ループに陥るバグが発生。Bobに相談すると原因を特定してくれました：

**問題:** ファイルステータス変数がリセットされていない

**修正:**

```cobol
MOVE '00' TO WS-TEMP-STATUS  ← これを追加！
PERFORM UNTIL WS-TEMP-STATUS = '10'
    READ TEMP-FILE
        ...
END-PERFORM
```

## COBOLの特徴

### 英語に近い構文

```cobol
MOVE 'Y' TO WS-FOUND
IF TODO-ID = WS-TODO-ID-INPUT
PERFORM UNTIL WS-FILE-EOF
```

他の言語と比べて、ビジネス文書のように読めます。

### レコード指向のデータ

```cobol
01  TODO-RECORD.
    05  TODO-ID      PIC 9(4).
    05  TODO-STATUS  PIC X.
    05  TODO-TEXT    PIC X(70).
```

データ形式を明示的に定義します（`PIC 9(4)`: 4桁の数字、`PIC X(70)`: 70文字）。

### データファイル

```bash
$ cat todos.dat
0001D買い物に行く
0002Pレポートを書く
```

固定長レコード形式で保存されます。

## IBM Bobを使った感想

### 良かった点

- **古い言語でも対応**: COBOLでもコード生成可能
- **バグ診断が的確**: 無限ループの原因を正確に特定
- **日本語対応**: 日本語のドキュメントも生成可能

### 改善の余地

- 初回生成コードにバグがあった

## まとめ

IBM Bobを使って、60年前の言語COBOLでTODOアプリを作ることができました。

自分で書くのは難しそうでしたが、AIに生成してもらって読んでみると、COBOLの可読性の高さと明示的な構文が理解できました。

COBOLに良くも悪くも気になっている方は、ぜひ試してみてください。AIと一緒なら意外と面白いですよ。

## 参考

https://github.com/optimisuke/hello-cobol

https://gnucobol.sourceforge.io/

https://www.ibm.com/products/watsonx-code-assistant
