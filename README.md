# BlenderによるCGデータセット生成ツール
paper 人工知能全国大会(2021): https://www.jstage.jst.go.jp/article/pjsai/JSAI2021/0/JSAI2021_3G4GS2i02/_article/-char/ja
3DCGソフトウェアであるBlenderを自動化したインスタンスセグメンテーション・キーポイント検出用のCGを用いた魚向けデータ拡張・生成ツールです。

![render_results](https://user-images.githubusercontent.com/104173409/165160796-8c83ae61-075f-4483-abcd-3a50761129e4.png)

## アプローチ概要
本ツールは、魚の切り抜き画像を張り付けた長方形のオブジェクトからオブジェクトのランダマイズとシーンへの配置を行い、レンダリングを行うという生成フローを自動化するものです。
これによりCGデータ画像に加え、セグメンテーションマップやキーポイントといったアノテーションデータを自動生成します。  

レンダリングするBlender上のシーンは以下のような構成となっています。

![ponti](https://user-images.githubusercontent.com/104173409/165161224-a0fa92cc-8393-4c0f-af0f-f52fa02132dc.png)

### 処理フロー
本ツールでデータセットを生成するフローは以下の手順で実行されます。
1. ベースとなるオブジェクトの準備
2. オブジェクトの複製とランダム化
3. オブジェクトの配置
4. 学習用データとセグメンテーションマップのレンダリングとキーポイント座標の出力
5. セグメンテーションマップをアノテーションデータに変換する後処理

### オブジェクトのランダマイズ

魚オブジェクトは回転・スケーリング・フリップに加え、ランダムなポーズ変形が行われます。  
ランダムに変形したオブジェクトは自由落下をシミュレートすることでシーンに配置されます。 

![sampling_set](https://user-images.githubusercontent.com/104173409/165196122-0622bed3-f2ce-4a3d-b588-e460cfd6c0fd.png)

### 不自然なオブジェクトの除去

自由落下で配置したオブジェクトは不自然な見た目になる可能性があるため、以下のようなオブジェクトを配置後に取り除きます。

![image](https://user-images.githubusercontent.com/104173409/165196210-9c057c9f-3394-49e0-9add-a39bf2104757.png)

### ランダムな影の生成

本ツールではランダムな影を生成するデータ拡張も自動的に行います。

![スクリーンショット 2022-04-26 093755](https://user-images.githubusercontent.com/104173409/165196348-d748a643-0731-43a2-b472-2c4d943bf72c.png)

### セグメンテーションマップ

セグメンテーションマップは、以下のようにクラス数と最大インスタンス数に合わせて色相と明度を離散化したものを利用します。  
これらのマテリアルは必要に合わせて動的にマテリアルが生成されます。  
生成したマテリアルはオブジェクトのクラスとインスタンスIDに合わせて表の上から順に適用されます。

![color](https://user-images.githubusercontent.com/104173409/165187693-edaa6c51-4737-4bd0-ad31-0fa88ead6f9b.png)



- - -

# 利用方法
リポジトリに含まれるdata_Render.blendをBlender2.8(or later)で開きます。  
Scriptsタブに切り替え、データ生成の設定用の変数を確認します。

![スクリーンショット 2022-04-26 072221](https://user-images.githubusercontent.com/104173409/165185739-ef5f7072-6aa6-4c1e-a8b7-993a2b6f5852.png)

以下の設定を変更して作業を行うことで、レンダリングまでの一連の処理フローを行わなくとも、ランダマイズと配置を行った結果を確認できます。
```bash
# オブジェクト配置・レンダリング後にシーンをリセットするかどうか。
is_reset = False

# 物理演算配置したオブジェクトから不自然な見た目のオブジェクトを取り除くかどうか。
is_check = True　

# オブジェクト配置後にレンダリングしてデータを書き出すかどうか。
is_render = False

# 何枚の画像を生成するか。
render_num = 6000

# 一枚の画像に最大何匹の魚を配置するか。
max_fish_num = 6

#初期seed
random.seed(0)

#レンダリングした画像・セグメンテーションマップ・レンダリング後の画像上のキーポイント座標の保存先
save_path = 'D:/Blender/Render_Results/'
```

実際にレンダリングを行ってデータを生成する際は以下のように設定します。
```bash
is_reset = True
is_check = True　
is_render =True
render_num = 6000
max_fish_num = 6
random.seed(0)
save_path = 'D:/Blender/Render_Results/'
```

設定を確認後、スクリプト実行のボタンを押すことで自動的にデータが生成され、アノテーション付きでsave_pathに保存されます。

# 独自のオブジェクトの追加

## ベースとなるオブジェクトの準備
魚オブジェクトはあらかじめアーマチュアやキーポイントとなるオブジェクトをペアリングし、ノーマルマップなどを設定したマテリアルなどを設定しておきます。

![スクリーンショット 2022-04-26 060932](https://user-images.githubusercontent.com/104173409/165175495-69ca3d82-e339-4453-a675-b2b812303438.png)

前準備が完了したオブジェクトの情報をFishDataクラスのインスタンスとしてリストに追加することで、自動生成に利用できます。
```
fish_data_base = FishData(bone_name='aji_bone_key',
                            mesh_name='aji_mesh_key',
                            keys_name_list = ['mouth_aji',
                                                'eye_aji',
                                                'pectoral_fin_aji',
                                                'pelvic_fin_aji',
                                                'dorsal_fin_aji',
                                                'tail_fin_root_aji',
                                                'tail_fin_top_aji',
                                                'tail_fin_center_aji',
                                                'tail_fin_bottom_aji'],
                            texture_material_name='aji',
                            texture_image_name='aji.png',
                            class_color='blue',
                            class_id = 6,
                            instance_id=0)
fish_data_base.hide_render = True
fish_data_base.set_rigidbody_type(rigidbody_type=1)

base_fish_list.append(fish_data_base)
```
