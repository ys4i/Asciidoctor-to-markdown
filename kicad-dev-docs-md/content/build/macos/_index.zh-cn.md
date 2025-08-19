title: macOS weight: 12 summary: macOS 使用 cmake 和 clang 的说明 tags:
\["macos"\] ---

# 在 macOS 上编译 KiCad

从 V5 开始，macOS 的编译和打包可以使用
[kicad-mac-builder](https://gitlab.com/kicad/packaging/kicad-mac-builder)，
来完成，它可以为 macOS
下载、修复、编译和打包。它用于创建正式版本和夜间版本，并将编译环境设置的复杂性降低为一两个命令。
kicad-mac-builder 的用法在其网站上有详细介绍。

如果您希望在不使用 kicad-mac-builder
的情况下进行编译，请使用以下内容及其源代码作为参考。 在 macOS
上编译需要编译依赖库，这些依赖库需要打补丁才能正常工作。

在以下命令集中，将 macOS 版本号(即
10.11)替换为所需的最低版本。为您正在运行的同一版本编译可能是最简单的。

KiCad 目前不能与可由 MacPorts 或 Homebrew 等包管理器下载或安装的
wxWidget 的现成版本一起使用。 为了避免处理补丁，GitHub 上维护了 [KiCad
的 wxWidget 分支](https://github.com/KiCad/wxWidgets)。
所有需要的补丁和其他一些修复/改进都包含在 `kicad/macos-wx-3.0` 分支中。

要执行 wxWidgets 编译，请执行以下命令：

    cd <您的 wxWidgets 编译文件夹>
    git clone -b kicad/macos-wx-3.0 https://gitlab.com/kicad/code/wxWidgets.git
    mkdir wx-build
    cd wx-build
    ../wxWidgets/configure \
        --prefix=`pwd`/../wx-bin \
        --with-opengl \
        --enable-aui \
        --enable-html \
        --enable-stl \
        --enable-richtext \
        --with-libjpeg=builtin \
        --with-libpng=builtin \
        --with-regex=builtin \
        --with-libtiff=builtin \
        --with-zlib=builtin \
        --with-expat=builtin \
        --without-liblzma \
        --with-macosx-version-min=10.11 \
        --enable-universal-binary=i386,x86_64 \
        CC=clang \
        CXX=clang++
    make
    make install

如果一切正常，您可以在 `<您的 wxWidgets 编译文件夹>/wx-bin` 中找到
wxWidgets 二进制文件。 现在，使用以下命令编译一个不带 Python 脚本的基本
KiCad：

    cd <你的 kicad 源文件镜像>
    mkdir -p build/release
    mkdir build/debug               # 对于调试版本是可选的。
    cd build/release
    cmake -DCMAKE_C_COMPILER=clang \
            -DCMAKE_CXX_COMPILER=clang++ \
            -DCMAKE_OSX_DEPLOYMENT_TARGET=10.11 \
            -DwxWidgets_CONFIG_EXECUTABLE=<your wxWidgets build folder>/wx-bin/bin/wx-config \
            -DKICAD_SCRIPTING=OFF \
            -DKICAD_SCRIPTING_MODULES=OFF \
            -DKICAD_SCRIPTING_WXPYTHON=OFF \
            -DCMAKE_INSTALL_PREFIX=../bin \
            -DCMAKE_BUILD_TYPE=Release \
            ../../
    make
    make install

如果 CMake
配置失败，请确定缺少的依赖项并将其安装在系统上，或者禁用相应的 KiCad
功能。 如果一切正常，您将在 `build/bin` 文件夹中获得自包含的应用程序包。

使用 Python 脚本编译 KiCad 更加复杂，这里不再详细介绍。 您将不得不针对
KiCad 分支的 wxWidgets 源代码编译 wxPython 可能与 wxPython
包捆绑在一起的现有 wxWidget 将无法工作。 请参阅 wxPython 文档。或者
\[macOS 打包编译脚本\]\[\] (`Compile_wx.sh`) 了解具体操作方法。
然后，使用 CMake 配置，如下所示将其指向您自己的 wxWidgets/wxPython：

    cmake -DCMAKE_C_COMPILER=clang \
            -DCMAKE_CXX_COMPILER=clang++ \
            -DCMAKE_OSX_DEPLOYMENT_TARGET=10.9 \
            -DwxWidgets_CONFIG_EXECUTABLE=<your wxWidgets build folder>/wx-bin/bin/wx-config \
            -DPYTHON_EXECUTABLE=<path-to-python-exe>/python \
            -DPYTHON_SITE_PACKAGE_PATH=<your wxWidgets build folder>/wx-bin/lib/python2.7/site-packages \
            -DCMAKE_INSTALL_PREFIX=../bin \
            -DCMAKE_BUILD_TYPE=Release \
            ../../
