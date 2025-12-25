# felica-reader-rcs620s-pico

<img width="600" src="https://dl.dropboxusercontent.com/s/kfclvg1qqregvnt/IMG_9299.png">

FeliCa リーダー・ライターを使用したSuica/PASMO履歴リーダです。

USBケーブルでPaspberry Pi picoとパソコンと接続し、履歴データを表示させることができます。I2C接続LCDを付ければ、現在の残高を表示します。残高表示は、以下のFelica対応ICカードに対応しています。

* Suica
    * ICOCA
    * TOICA
    * Kitaca
    * はやかけん
    * AOPASS
    * iGUCA
    * Iwate Green Pass
    * odeca
    * SUGOCA
    * cherica
    * totra
    * manaca
    * SAPICA
    * Welcome Suica
* PASMO
* Edy
* nanaco
* won
* nimoca
* ecomayca
* PiTaPa
* PASPY

また、AS-289R2プリンタシールドを接続すると、Suica/PASMO履歴データを印字することができます。

# 使用した機材

* Paspberry Pi pico  
https://www.switch-science.com/catalog/6900/
* PIMORONI Tiny 2040
https://www.switch-science.com/products/7615
* FeliCa リーダー・ライター RC-S620S  
https://www.switch-science.com/catalog/353/
* FeliCa RC-S620S/RC-S730 ピッチ変換基板のセット(フラットケーブル付き)  
https://www.switch-science.com/catalog/1029/
* I2C接続の小型LCD搭載ボード(3.3V版)  
https://www.switch-science.com/catalog/1405/
* AS-289R2プリンタシールド  
https://www.switch-science.com/catalog/2553/

# デバイスとの接続

Pasberry Pi picoと他の部品は以下のように接続してください。

|Rasberry Pi pico|Tiny 2040|RC-S620/S|I2C LCD|AS-289R2|
|---|---|---|---|---|
|GPIO4 (pin 6)|GPIO4 (pin 12)|||RxD1 (D1)|
|VSYS (pin 39)|VBUS (pin 1)|||5V|
|3V3(OUT) (pin 36)|3V3(OUT) (pin 3)|VDD (pin 1)|VDD (pin 2)||
|GND (pin 38)|GND (pin 8)|GND (pin 4, 6)|GND (pin 3)|GND|
|GPIO16 (pin 21)|GPIO0 (pin 16)|RXD (pin 2)|||
|GPIO17 (pin 22)|GPIO1 (pin 15)|TXD (pin 3)|||
|GPIO14 (pin 19)|GPIO26 (pin 7)||SDA (pin 4)||
|GPIO15 (pin 20)|GPIO27 (ping 6)||SCL (pin 5)||

# 必要なツールのインストール

## ツールチェインのインストール
以下の情報を参照して、ビルドに使用するツールチェインをインストールしてください。
https://pip-assets.raspberrypi.com/categories/610-raspberry-pi-pico/documents/RP-008276-DS-1-getting-started-with-pico.pdf

## pico-sdk
以下のサイトを参考にしてください。  
https://github.com/raspberrypi/pico-sdk

## picotool
以下のサイトを参考にしてください。  
https://github.com/raspberrypi/picotool

# プログラムのビルドと書き込み

## コマンドライン（cmake）でビルドする
以下のコマンドでリポジトリをクローンして、ビルドします。

### リポジトリのクローン

```bash
$ git clone --recursive https://github.com/toyowata/felica-reader-rcs620s-pico
$ cd felica-reader-rcs620s-pico
$ mkdir build && cd build
```

### ビルド

PICO_SDK_PATHを設定します。

```
$ export PICO_SDK_PATH=<pico-sdk-path>
```

Raspberry Pi Pico の場合
```
$ cmake .. -GNinja -DPICO_BOARD=PICO
$ ninja
```

Raspberry Pi Pico 2 の場合
```
$ cmake .. -GNinja -DPICO_BOARD=PICO2
$ ninja
```

PIMORONI Tiny 2040 の場合
```
$ cmake .. -GNinja -DPICO_BOARD=PIMORONI_TINY2040
$ ninja
```

## Raspberry Pi pico に書き込む
BOOTSELモードに設定し（基板上のボタンを押したまま電源を入れる）、以下のコマンドを実行します。

```bash
$ picotool load ./felica-reader-rcs620s-pico.bin
```

## プログラムの実行

プログラム書き込み後、自動的にリセットされプログラムが起動します。  
TeraTerm, CoolTerm等のシリアルターミナルソフトウェアでパソコンと接続します（115200,8,N,1）。日本語を表示するので、UTF8が表示できるモードに設定してください。  
FeliCa リーダー・ライター上にSuicaなどの交通系ICカードを乗せると、履歴情報が表示されます。

履歴情報の例

```

*** RC-S620/S FeliCaリーダープログラム ***

IDm: 0101-xxxx-yyyy-zzzz

1D 01 00 02 33 09 1D 1E 1D 3C FE 37 00 01 55 00 
機種種別: 連絡改札機
利用種別: 自動改札出場
入出場種別: 出場
横浜線 町田駅 - 八高線 小川町駅
処理日付: 2025/08/09
残額: 14334円

17 01 00 02 33 09 A9 2C A9 34 F9 36 00 01 57 00 
機種種別: 簡易改札機
利用種別: 自動改札出場
入出場種別: 出場
東上本線 小川町駅 - 東上本線 寄居駅
処理日付: 2025/08/09
残額: 14073円

17 01 00 02 33 09 81 11 81 29 0B 34 00 01 59 00 
機種種別: 簡易改札機
利用種別: 自動改札出場
入出場種別: 出場
秩父本線 寄居駅 - 秩父本線 御花畑駅
処理日付: 2025/08/09
残額: 13323円

C8 46 00 00 33 09 63 0F C2 3C F7 2E 00 01 5A 00 
機種種別: 自販機
利用種別: 物販
処理日付: 2025/08/09 12:24:15
残額: 12023円

```

## 注意点と既知の問題

### リセット方法
リセット用のタクトスイッチを付けると便利です。  
https://nuneno.cocolog-nifty.com/blog/2021/03/post-c5ccb6.html

### サイバネコード
駅データのサイバネコードは、以下のサイトのデータを使用させていただきました。  
https://github.com/MasanoriYONO/StationCode
https://ja.ysrl.org/atc/station-code.html

このデータから必要な項目だけを抽出し、csv形式からバイナリ形式に変更を行っています。変換用のツールは以下に公開しました。  
https://github.com/toyowata/csv2bin

### 制約事項
* 誤動作を防ぐために、同じカードを連続して読み込むことはできません。同じカードを読み込む場合は、リセットを行ってください。
