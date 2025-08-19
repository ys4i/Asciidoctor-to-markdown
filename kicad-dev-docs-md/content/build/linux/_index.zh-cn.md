title: Linux weight: 11 summary: 使用 gcc 的 Linux 使用说明 tags:
\["linux"\] ---

# 在 Linux 上编译 KiCad

要在 Linux 上执行完整编译，请运行以下命令：

    cd <你的 kicad 源镜像文件夹>
    mkdir -p build/release
    mkdir build/debug               # 对于调试版本是可选的。
    cd build/release
    cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo \
            ../../
    make
    sudo make install

如果 CMake 配置失败，请确定缺少的依赖项并将其安装在您的系统上。
默认情况下，CMake 会将 Linux 上的安装路径设置为 `/usr/local`。 使用
`CMAKE_INSTALL_PREFIX` 选项指定不同的安装路径。

我们建议对个人版本使用 `RelWithDebInfo` 编译类型，因为这将包括调试符号，
以便在遇到崩溃时提供更有用的堆栈跟踪。

对于调试版本，请将 `RelWithDebInfo` 替换为 `Debug`。

## 小贴士和技巧

### Ninja

KiCad 使用 Ninja 编译系统代替 `make` 编译速度更快。要使用 Ninja ，可以在
CMake 命令行中指定 Ninja 输出：

    cmake -G Ninja -DCMAKE_BUILD_TYPE=RelWithDebInfo ../../
    ninja
    sudo ninja install
