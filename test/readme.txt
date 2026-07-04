AlphaMovie プラグイン 実機デコードテスト
========================================

startup.tjs は krkrz64.exe（または krkrz.exe）で自動実行される起動スクリプトです。
AlphaMovie.dll を読み込み、このフォルダに置かれた検証用 .amv を開いて各種プロパティを
検証し、フレームをデコードして Layer へ描画、結果を PNG として保存します。

検証内容:
  - Plugins.link による AlphaMovie.dll 読込
  - open / numOfFrame / screenWidth / screenHeight / FPSRate / FPSScale
  - showNextImage による Layer へのデコード描画（アルファ込み）
  - frame プロパティによるシーク
  - デコード結果を amv_<name>.png に保存（目視でデコードの正しさを確認）

準備:
  検証用の .amv を **このフォルダ(test)に配置** してください（リポジトリには
  同梱していません）。startup.tjs は以下の名前を探します（無い物はスキップ）:
    explosion.amv / particle.amv / water.amv / maho2.amv
  (AlphaMovie 配布サンプルの data/video/*.amv がそのまま使えます。
   explosion/particle/maho2 は JPEG-alpha 経路、water は zlib-alpha 経路の検証になります。)

実行手順:
  1. AlphaMovie.dll をビルドし、krkrz64.exe と同じフォルダにコピーする
     （Plugins.link は exe と同じディレクトリを基準に解決する）。
       例) build/x64-windows/core/plugins/AlphaMovie/Release/AlphaMovie.dll を
           build/x64-windows/core/Release/ へコピー
  2. このフォルダ（test）を引数に krkrz64.exe を起動する:
       krkrz64.exe "<このフォルダの絶対パス>" > out.txt 2>&1
     コンソールから起動すれば Debug.message の出力が stdout に出る。

判定:
  - 出力の "OK   :" / "FAIL :" 行で判定する（FAIL が無ければ全成功）。
  - amv_*.png を開き、映像（火球/煙/水滴/魔法陣 等）が色・アルファともに正しく
    デコードされているか目視確認する。
  - 終了コードは SDL/エンジンの shutdown 経路により System.exit の値通りに
    ならないことがあるため、出力行で判断すること。
  - SDL ビルドは描画しないので ANGLE(libEGL/libGLESv2) 無しでも起動する。
