# Blender_Augmentation
Blenderを自動化したインスタンスセグメンテーション・キーポイント検出用のCGを用いた魚向けデータ拡張・生成ツールです。

![render_results](https://user-images.githubusercontent.com/104173409/165160796-8c83ae61-075f-4483-abcd-3a50761129e4.png)

## アプローチ概要
本ツールは、魚の切り抜き画像を張り付けた長方形のオブジェクトから、オブジェクトのランダマイズと自動的にシーンへの配置を行い、自動的にレンダリングします。
これにより、CGデータ画像に加え、セグメンテーションマップやキーポイントといったアノテーションデータを自動生成します。  

レンダリングするBlender上のシーンは以下のような構成となっています。

![ponti](https://user-images.githubusercontent.com/104173409/165161224-a0fa92cc-8393-4c0f-af0f-f52fa02132dc.png)



