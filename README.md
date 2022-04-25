# BlenderによるCGデータセット生成ツール
3DCGソフトウェアであるBlenderを自動化したインスタンスセグメンテーション・キーポイント検出用のCGを用いた魚向けデータ拡張・生成ツールです。

![render_results](https://user-images.githubusercontent.com/104173409/165160796-8c83ae61-075f-4483-abcd-3a50761129e4.png)

## アプローチ概要
本ツールは、魚の切り抜き画像を張り付けた長方形のオブジェクトからオブジェクトのランダマイズとシーンへの配置を行い、レンダリングを行うという生成フローを自動化するものです。
これによりCGデータ画像に加え、セグメンテーションマップやキーポイントといったアノテーションデータを自動生成します。  

レンダリングするBlender上のシーンは以下のような構成となっています。

![ponti](https://user-images.githubusercontent.com/104173409/165161224-a0fa92cc-8393-4c0f-af0f-f52fa02132dc.png)

### 処理フロー
1.   ベースとなるオブジェクトの準備
2.   手作業による魚オブジェクトの準備
3.   オブジェクトの複製とランダム化
4.   オブジェクトの配置
5.   学習用データとセグメンテーションマップのレンダリング
6.   セグメンテーションマップをアノテーションデータに変換する後処理

## 前準備
魚オブジェクトはあらかじめアーマチュアやキーポイントとなるオブジェクトをペアリングし、マテリアルなどに利用するノーマルマップなどのアセットを設定しておきます。

![スクリーンショット 2022-04-26 060932](https://user-images.githubusercontent.com/104173409/165175495-69ca3d82-e339-4453-a675-b2b812303438.png)

