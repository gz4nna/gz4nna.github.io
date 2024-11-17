# 安装 Aseprite

`Aseprite` 支持用户自行编译使用, 仓库位于[这里](https://github.com/aseprite/aseprite)
INSTALL.md 中有基本的安装指南, 以下是在win10上的实践

# 平台

我使用的是 Windows 10, 编译的 `Aseprite` 版本为 1.3.9

# 依赖

使用 Windows 编译 `Aseprite` 需要:

- [Visual Studio](https://visualstudio.microsoft.com/downloads/)
	VisualStudio 不是必须的, 使用 VisualStudio 是为了选择 `Visual Studio installer` 中的工作负荷 `使用c++的桌面开发`, 这个工作负荷会帮助安装好
	
	- 3.16以上版本的 [CMake](https://cmake.org)
	- [Ninja](https://ninja-build.org)
	
	这两个才是必要的, 可以自行安装, 如果使用 VS 的话, 可以在安装目录的 `Common7\IDE\CommonExtensions\Microsoft\CMake` 这个路径下找到这两个工具
	
- 编译后的 [Skia 库](https://github.com/aseprite/skia/releases)
	去看仓库中的 README 文件里推荐使用哪个 branch, 我在安装时使用了 m102
	下载发行版可以省去编译的环节,注意需要和平台对应, 我选择的是x64

确保拥有以上内容后, 可以在系统环境变量中将 cmake 和 ninja 添加到 Path 中, 遇到添加后不生效的情况可以关闭所有 cmd 后使用以下命令来刷新验证是否成功添加(或者重启来刷新):

```shell
echo %PATH%
```

# 编译

`Visual Studio` 的编译器可能无法直接被找到, 因此, 需要使用 `Developer Command Prompt for VS` 运行编译的指令

> **注意**

 在开始菜单中的 `Developer Command Prompt for VS` 默认是32位, 如果编译目标为64位, 需要使用 `Common7\Tools\VsDevCmd.bat` 这个批处理
  
```shell
call "VsDevCmd.bat" -arch=x64
```

在 cmd 中运行以上命令后, 便进入了 `Developer Command Prompt for VS` 工具

由于 `Aseprite` 中 CMakeList 文件使用的编译器名称和 VS 中不同, 此时还需要进一步设定来帮助 CMake 指定编译器:

```shell
set CC=cl
set CXX=cl
```

现在, 可以在想要的输出目录下运行以下命令(根据自己的变量名替换):

```shell
cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo -DLAF_BACKEND=skia -DSKIA_DIR=C:/deps/skia -DSKIA_LIBRARY_DIR=C:/deps/skia/out/Release-x64 -DSKIA_LIBRARY=C:/deps/skia/out/Release-x64/skia.lib -G Ninja ..
```

成功后, 运行:

```shell
ninja aseprite
```

生成的可执行文件在输出目录的 `\bin` 下