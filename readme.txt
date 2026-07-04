Title: AlphaMovie plugin
Author: T.Imoto <http://www.kaede-software.com>

●これはなに？

吉里吉里Z 用の AlphaMovie プラグインです。アルファチャンネル付きの動画
コンテナ .amv (Alpha Movie) を再生し、各フレームを対象 Layer のメイン画像
バッファへ直接（アルファ込みで）描画します。ムービーオーバーレイでは実現
できない、任意レイヤ上へのアルファ付き動画描画を目的としています。

TJS クラス AlphaMovie を登録します。詳細な API は manual.tjs を
参照してください。

なお本実装は、オリジナル版 AlphaMovie.dll がソース非公開であるため、その
バイナリの解析により .amv 形式とデコード処理を割り出し、オリジナルと同一
仕様となるよう再実装したものです。

●使い方（概略）

  var mov = new AlphaMovie();
  mov.open("video/explosion.amv");
  mov.setPosition(100, 80);      // 表示左上位置
  mov.loop = true;
  // タイマ等から毎フレーム呼ぶ:
  var no = mov.showNextImage(layer);  // 次フレームを layer に描画し番号を返す

●.amv 形式について

  ヘッダ 'AJPM' + 量子化テーブル + 'FRAM' チャンク列で構成されます。
  各フレームは以下の 2 経路のいずれかです。
    (A) JPEG-alpha : 色(YCbCr) と alpha を 4 成分 (Cb,Cr,Y,A/4:2:0) として
        1 スキャンにインターリーブしたベースライン JPEG エントロピー。3 量子化テーブル。
    (B) zlib-alpha : alpha は zlib 圧縮 8bit グレースケール、色は 3 成分 (Cb,Cr,Y)
        ベースライン JPEG。2 量子化テーブル。

  ※ 本コーデックの DC 予測は非標準で、DC 予測子を Huffman テーブル単位で共有します
     (luma 予測子 = Y と A で共有 / chroma 予測子 = Cb と Cr で共有)。標準 JPEG
     (libjpeg/turbojpeg) は成分毎に独立した予測子を用いるためそのままでは復号でき
     ません。本プラグインはこの共有予測子方式を忠実に再現する自作ベースライン
     デコーダ (標準 Huffman テーブル + IDCT) を実装しています。

●コンパイル時の注意

  vcpkg で zlib が必要です（vcpkg.json 参照。zlib-alpha 経路の alpha 展開に使用）。
  JPEG 展開は自作デコーダのため外部 JPEG ライブラリには依存しません。
  Win32 専用 API は使用しておらず、全ビルドバリアント（WIN / SDL / LIB）で
  ビルド可能です。

●ライセンス

  作者・ライセンスともに吉里吉里(krkr) 本体に準拠します。
  Copyright T.Imoto <http://www.kaede-software.com>
  詳細は吉里吉里本体のライセンス文書を参照してください。
