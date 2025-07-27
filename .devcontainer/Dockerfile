FROM mcr.microsoft.com/devcontainers/cpp:1-debian-11

ARG REINSTALL_CMAKE_VERSION_FROM_SOURCE="none"

# Optionally install the cmake for vcpkg
COPY ./reinstall-cmake.sh /tmp/

RUN if [ "${REINSTALL_CMAKE_VERSION_FROM_SOURCE}" != "none" ]; then \
        chmod +x /tmp/reinstall-cmake.sh && /tmp/reinstall-cmake.sh ${REINSTALL_CMAKE_VERSION_FROM_SOURCE}; \
    fi \
    && rm -f /tmp/reinstall-cmake.sh

# [Optional] Uncomment this section to install additional vcpkg ports.
# RUN su vscode -c "${VCPKG_ROOT}/vcpkg install <your-port-name-here>"

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
    # 組み込み開発に必須のツール
    gcc-arm-none-eabi \
    openocd \
    # その他のC++開発ツール
    gdb \
    clang-format \
    ninja-build \
    && apt-get -y install --no-install-recommends \
    git wget libncurses-dev flex bison gperf python3 python3-pip python3-setuptools python3-wheel libtool-bin \
    && rm -rf /var/lib/apt/lists/*

    # ESP-IDFのクローンとセットアップ
# ※バージョンは開発したいESP-IDFの安定版に合わせてください (例: v5.2.1, v5.1.2 など)
ARG ESPIDF_VERSION="v5.2.1" # ここでバージョンを指定
#ESPをどこにインストールするかきめる住所のようなものを設定する
ENV IDF_PATH="/opt/esp-idf" 
# ESP-IDFのインストールパス

# ESP-IDFを/opt/esp-idfにクローン

RUN git clone --recursive https://github.com/espressif/esp-idf.git ${IDF_PATH} \
    && cd ${IDF_PATH} \
    && git checkout ${ESPIDF_VERSION} \
    && git submodule update --init --recursive

# ESP-IDFのツールチェインとPython環境をインストール
# vscodeユーザーとして実行（環境変数が必要なため）
# 'install.sh'は通常rootで実行しないので、su vscode -c でユーザーを切り替える
RUN su vscode -c "cd ${IDF_PATH} && ./install.sh"

# ESP-IDFの環境変数を設定するためのスクリプトを、コンテナ起動時に読み込まれるようにする
# bashrc に追記することで、ターミナルを開くたびに環境がセットアップされる
RUN echo "source ${IDF_PATH}/export.sh" >> /home/vscode/.bashrc