# Open MPW programの概要

- Googleのページ: https://developers.google.com/silicon
- efablessのページ: https://efabless.com/open_shuttle_program

# caravelを利用した設計の一連の流れについて

より正確な記述については[公式リポジトリ](https://github.com/efabless/caravel_user_project)の説明

- https://github.com/efabless/caravel_user_project/blob/main/docs/source/index.rst#section-quickstart

を参照のこと

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


