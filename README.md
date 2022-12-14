# Open MPW programの概要

- Googleのページ: https://developers.google.com/silicon
- efablessのページ: https://efabless.com/open_shuttle_program

# caravelを利用した設計の一連の流れについて

より正確な記述については[公式リポジトリ](https://github.com/efabless/caravel_user_project)の説明

- https://github.com/efabless/caravel_user_project/blob/main/docs/source/index.rst#section-quickstart

を参照のこと。

## 概要

[公式リポジトリ](https://github.com/efabless/caravel_user_project)には、caravelに基づくチップのユーザースペースを利用するためのサンプルプロジェクトが含まれています。IOパッド、ロジックアナライザプローブ、wishboneポートといったcaravelのユーザースペースに存在する機能を利用する方法が示されています。また、このリポジトリは、Open MPW program提出の際推奨される構造になっています。

## 準備
Docker: [Linux](https://hub.docker.com/search?q=&type=edition&offering=community&operating_system=linux&utm_source=docker&utm_medium=webreferral&utm_campaign=dd-smartbutton&utm_location=header) || [Windows](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe?utm_source=docker&utm_medium=webreferral&utm_campaign=dd-smartbutton&utm_location=header) || [Mac with Intel Chip](https://desktop.docker.com/mac/main/amd64/Docker.dmg?utm_source=docker&utm_medium=webreferral&utm_campaign=dd-smartbutton&utm_location=header) || [Mac with M1 Chip](https://desktop.docker.com/mac/main/arm64/Docker.dmg?utm_source=docker&utm_medium=webreferral&utm_campaign=dd-smartbutton&utm_location=header)

Python 3.6+ with PIP

## クイックスタート

はじめに、新規リポジトリを caravel_user_project のテンプレートにしたがって作成します。このときリポジトリは public に設定し、 README を含むようにします。

* https://github.com/efabless/caravel_user_project/generate から新規リポジトリを作成
* 下記のコマンドでリポジトリをclone

```
git clone <your github repo URL>
```

また、環境設定のため下記のコマンドを実行します。

```
cd <project_name> # project_name is the name of your repo

mkdir dependencies

export OPENLANE_ROOT=$(pwd)/dependencies/openlane_src # you need to export this whenever you start a new shell

export PDK_ROOT=$(pwd)/dependencies/pdks # you need to export this whenever you start a new shell

# export the PDK variant depending on your shuttle, if you don't know leave it to the default

# for sky130 MPW shuttles....
export PDK=sky130A

# for the gf180 GFMPW shuttles...
export PDK=gf180mcuC

make setup
```

このコマンドによって

- caravel_lite (caravelの小型版)
- シミュレーション用マネジメントコア
- デザインを実装するための openlane
- pdk

がインストールされます。

### 1. 設計の開始

* プロジェクトの実装を始めるにあたって、OpenLaneに入力するためのRTLのVerilog記述が必要になります。各マクロ向けのサブディレクトリは openlane/ 以下に作り、それぞれのディレクトリには各マクロに向けたOpenLaneの設定ファイルを配置します。

```
make <module_name>
```

### 2. user_project_wrapper へのモジュールの統合

* openlane/user_project_wrapper/config.tcl にある環境変数 VERILOG_FILES_BLACKBOX, EXTRA_LEFS, EXTRA_GDS_FILES をそれぞれ作成したモジュールを指すよう変更します。
* verilog/rtl/user_project_wrapper.v 内でモジュールを呼び出します。
* 作成したモジュールを含む user_project_wrapper を実装するため、下記のコマンドを実行します。

```
make user_project_wrapper
```

### 3. シミュレーションの実行

RTL、ゲートレベル、ゲートレベル+sdf fileを verilog/includes/includes.<rtl/gl/gl+sdf>.caravel_user_project 以下に用意します。注意点として、Verilogコード中にファイルを含まないようにする必要があります。

```
# you can then run RTL simulations using
make verify-<testbench-name>-rtl

# OR GL simulation using
make verify-<testbench-name>-gl

# OR for GL+SDF simulation using
# sdf annotated simulation is slow
make verify-<testbench-name>-gl-sdf

# for example
make verify-io_ports-rtl
```

### 4. opensta の実行

user_project_wrapper や、その内部のマクロについて spef を抽出します。

```
make extract-parasitics
```

モジュールのインスタンス名とspefファイルを結びつけるためのspefマッピングファイルを生成します。

```
make create-spef-mapping
```

opensta を実行します。

```
make caravel-sta
```

注意: タイミングスクリプトを更新するためには、 ```make setup-timing-scripts``` を実行します。

### 5. 事前チェックの実行

```
make precheck
make run-precheck
```

これで設計フローは完了です。 https://efabless.com/open_shuttle_program/ からプロジェクトを提出できます。

## 提出時チェックリスト

✔️ プロジェクトの構造が[公式リポジトリ](https://github.com/efabless/caravel_user_project)と一致している

✔️ プロジェクトのルートにinfo.yamlが含まれている

✔️ トップレベルマクロの名前がuser_project_wrapperになっている

✔️ RTL/ゲートレベルフルチップシミュレーションが通っている

✔️ 実装したマクロがLVS/DRCクリーンである

✔️ user_project_wrapperのゲートレベルネットリストがverilog/gl/user_project_wrapper.vというかたちで含まれている

✔️ 実装したuser_project_wrapperがpin_orderで定義されたピン配置にしたがっている

✔️ 実装したuser_project_wrapperがfixed_wrapper_cfgsで定義された設定にしたがっている

✔️ XORチェックが通過している

✔️ Openlaneのサマリーレポートが./signoff/に格納されている

✔️ デザインがmpw-precheckを通過している


