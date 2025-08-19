title: Windows (MSYS2) weight: 13 summary: 使用 MSYS2 编译 KiCad 的指南
tags: \["windows"\] ---

# 使用 MSYS2 编译

## 设置

[MSYS2](https://www.msys2.org/) 项目为编译 KiCad
所需的所有依赖项提供了包。 要安装
MSYS2，请执行以下操作编译环境，下载并运行 msys2 主页提供的 **MSYS2 64
位安装程序**。 安装完成后，运行 MSYS2 安装路径中的 `msys2_shell.cmd`
文件，并运行命令 `pacman-Syu`， 更新到最新的软件包版本。如果更新了
`msys2-runtime` 包，请关闭 shell 并运行 `msys2_shell.cmd`。

## 编译

以下命令假定您是为 64 位 Windows 编译的，并且您的主目录中名为
`kicad-source` 的文件夹中已经有 KiCad 源代码。 如果您需要编译 32
位版本，请参见下面的更改。在命令提示符下运行以下命令：

``` bash
pacman -S base-devel \
            git \
            mingw-w64-ucrt-x86_64-cmake \
            mingw-w64-ucrt-x86_64-doxygen \
            mingw-w64-ucrt-x86_64-gcc \
            mingw-w64-ucrt-x86_64-python \
            mingw-w64-ucrt-x86_64-pkg-config \
            mingw-w64-ucrt-x86_64-swig \
            mingw-w64-ucrt-x86_64-boost \
            mingw-w64-ucrt-x86_64-cairo \
            mingw-w64-ucrt-x86_64-glew \
            mingw-w64-ucrt-x86_64-curl \
            mingw-w64-ucrt-x86_64-wxPython \
            mingw-w64-ucrt-x86_64-toolchain \
            mingw-w64-ucrt-x86_64-glm \
            mingw-w64-ucrt-x86_64-opencascade \
            mingw-w64-ucrt-x86_64-ngspice \
            mingw-w64-ucrt-x86_64-zlib \
            mingw-w64-ucrt-x86_64-libgit2 \
            mingw-w64-ucrt-x86_64-wxwidgets3.2-msw \
            mingw-w64-ucrt-x86_64-protobuf \
            mingw-w64-ucrt-x86_64-nng
cd kicad-source
mkdir -p build/release
mkdir build/debug               # 对于调试版本是可选的。
cd build/release
cmake -DCMAKE_BUILD_TYPE=Release \
        -G "MSYS Makefiles" \
        -DCMAKE_PREFIX_PATH=/ucrt64 \
        -DCMAKE_INSTALL_PREFIX=/ucrt64 \
        -DDEFAULT_INSTALL_PATH=/ucrt64 \
        -DOCC_INCLUDE_DIR=/ucrt64/include/opencascade \
        ../../
make -j N install   # 其中 N 是系统可以处理的并发线程数
```

<div class="note">

因为 `nng` 仅为 `CLANG64` 、 `CLANGARM64` 和 `UCRT64` 环境提供了安装包,
因此本文推荐使用 `UCRT64` 作为构建环境。

</div>

对于 32 位版本，运行 `mingw32.exe`，将包名中的 `ucrt-x86_64` 改为
`i686`，并将 cmake 配置中的路径从 `/ucrt64` 改为 `/mingw32`。

<div class="note">

由于 cygwin（它的上游依赖项）取消了对 32 位支持，msys2 已经不再支持 32
位。

</div>

对于调试版本，请在 `build/debug` 文件夹中使用 `-DCMAKE_BUILD_TYPE=Debug`
运行 cmake 命令。

## 带 Clion 的 MSYS2

与 MSYS2 结合使用的 KiCad 可以配置为与 Clion 一起使用，以提供良好的 IDE
体验。

### toolchain 设置

首先，您必须将 MSYS2 注册为 toolchain，即编译器。

打开设置窗口 `Files > preferences`。

导航至 Build、Execution、Development，然后导航至 Toolchains 页面。

添加新的 toolchain，并按如下方式进行配置

- Name: `MSYS2-MinGW64`

- Environment Path: `<您的 msys2 安装文件夹>\mingw64\`

- CMake: `<您的 msys2 安装文件夹>\mingw64\bin\cmake.exe`

所有其他字段将自动填充。

### 工程设置

File \> Open 并 select 包含 kicad 源的文件夹。 Clion 可能会尝试启动
CMake 生成，但会失败，这是可以接受的。

再次打开设置窗口。 导航到 Build、Execution、Development，然后导航到
CMake 页面。 这些设置将保存到工程中。

您希望这样创建调试配置

- Name: `Debug-MSYS2`

- Build-Type: `Debug`

- Toolchain: `MSYS2-MinGW64`

- CMake 选项:

``` sh
-G "MinGW Makefiles"
-DCMAKE_PREFIX_PATH=/mingw64
-DCMAKE_INSTALL_PREFIX=/mingw64
-DDEFAULT_INSTALL_PATH=/mingw64
```

- 编译目录： `build/debug-msys2`

您现在可以触发 Clion 中的 "Reload CMake Cache" 选项来生成 cmake
工程，您应该删除 "junk" 编译文件夹(通常名为 cmake-build-debug-xxxx)，
它可能是在上面更改之前在源代码中创建的。我们更改了 build
文件夹，因为我们有一个用于 `/build` 的 gitignore

警告：收到有关 Boost 编译的警告消息是正常的。

## 已知的 MSYS2 编译问题

有一些特定于 MSYS2 的已知问题。本节提供了使用 MSYS2 编译 KiCad
时当前已知问题的列表。

### 使用 Boost 1.70 编译

使用 Boost 版本 1.70 编译 KiCad 时出现问题，原因是 CMake
在配置期间没有定义正确的链接库。 可以使用 Boost 1.70，但需要在 CMake
配置过程中添加 `-DBoost_NO_BOOST_CMAKE=ON`，以确保链接库正确生成。

``` bash
git clone https://github.com/Alexpux/MINGW-packages /c/mwp
cd /c/mwp/mingw-w64-oce
makepkg-mingw -is
```
