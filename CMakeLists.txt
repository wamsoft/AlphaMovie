# AlphaMovie プラグイン
#   .amv (Alpha Movie) 再生プラグイン。
#   JPEG 展開は自作ベースラインデコーダ (非標準 DC 予測の再現のため)。
#   zlib は zlib-alpha 経路の alpha 展開にのみ使用する。

# スタンドアロン configure も可能にするため TPSTUB_DIR を既定補完する
if(NOT DEFINED TPSTUB_DIR)
    set(TPSTUB_DIR ${CMAKE_CURRENT_SOURCE_DIR}/../tp_stub CACHE PATH "Path to tp_stub directory")
endif()
include(${TPSTUB_DIR}/krkrz.cmake)

find_package(ZLIB REQUIRED)

krkrz_plugin(AlphaMovie
    NCBIND
    SOURCES
        AlphaMovie.cpp
    INCLUDES
        ${CMAKE_CURRENT_SOURCE_DIR}
    LIBRARIES
        ZLIB::ZLIB
)
