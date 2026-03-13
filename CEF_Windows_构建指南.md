# CEF Windows 构建指南

## 目录

- [概述](#概述)
- [系统要求](#系统要求)
- [文件结构](#文件结构)
- [资源下载](#资源下载)
- [Windows 构建步骤](#windows-构建步骤)
- [构建特定版本分支](#构建特定版本分支)
- [参考文档](#参考文档)

## 概述

本指南用于设置最小开发环境并构建 Chromium/CEF 的 master 分支进行开发。

> **注意：** 本指南不适用于：
> - 寻找预构建二进制分发用于第三方应用的用户
> - 希望以完全自动化方式构建二进制分发的用户

## 系统要求

| 项目 | 要求 |
|------|------|
| 硬件要求 | 至少 16GB RAM（推荐 32GB+），150GB 可用磁盘空间（用于 Debug 构建） |
| 构建时间 | 约 4 小时（使用快速网络连接 100Mbps 和快速构建机器 2.4GHz，16 逻辑核心，SSD） |
| 开发环境 | 可使用专用硬件或 VMware、Parallels、VirtualBox 虚拟机 |

## 文件结构

```
~/code/
  automate/
    automate-git.py   <-- CEF 构建脚本
  chromium_git/
    cef/              <-- CEF 源码检出
    chromium/
      src/            <-- Chromium 源码检出
    update.[bat|sh]   <-- automate-git.py 的引导脚本
  depot_tools/        <-- Chromium 构建工具
```

> **重要提示：**
> - 路径中不要包含空格或特殊字符
> - 路径总长度应小于 35 个字符
> - 只使用 ASCII 字符

## 资源下载

以下是构建 CEF 所需的所有资源下载地址：

| 资源名称 | 下载地址 | 说明 |
|----------|----------|------|
| depot_tools | [https://storage.googleapis.com/chrome-infra/depot_tools.zip](https://storage.googleapis.com/chrome-infra/depot_tools.zip) | Chromium 构建工具包，包含 Python、Git、gclient 等工具 |
| automate-git.py | [https://bitbucket.org/chromiumembedded/cef/raw/master/tools/automate-git.py](https://bitbucket.org/chromiumembedded/cef/raw/master/tools/automate-git.py) | CEF 自动化构建脚本，用于下载和配置源码 |
| CEF 源码仓库 | [https://github.com/chromiumembedded/cef.git](https://github.com/chromiumembedded/cef.git) | CEF 官方 Git 仓库 |
| Chromium 源码仓库 | [https://chromium.googlesource.com/chromium/src.git](https://chromium.googlesource.com/chromium/src.git) | Chromium 官方 Git 仓库 |
| Visual Studio | [https://visualstudio.microsoft.com/downloads/](https://visualstudio.microsoft.com/downloads/) | Visual Studio 2022（推荐）或 2019 |
| Windows SDK | [https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/](https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/) | Windows SDK（需要安装特定版本） |
| 7-Zip | [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) | 推荐用于解压 depot_tools.zip |

> **下载提示：**
> - depot_tools.zip 文件较大（约 200MB），请确保网络连接稳定
> - automate-git.py 脚本会自动下载 CEF 和 Chromium 源码，总大小可能超过 100GB
> - 建议使用下载管理器或代理工具加速下载
> - 如果下载速度较慢，可以考虑使用镜像或代理

## Windows 构建步骤

### 1. 创建目录

```
c:\code\automate
c:\code\chromium_git
```

### 2. 下载 depot_tools

- 下载 depot_tools.zip 并解压到 `c:\code\depot_tools`

> **重要：** 不要使用拖放或复制粘贴方式解压，这不会提取隐藏的 ".git" 文件夹。建议使用右键菜单的"Extract all…"或 7-zip。

### 3. 配置环境变量

将 `c:\code\depot_tools` 添加到系统 PATH：

1. 运行 `SystemPropertiesAdvanced` 命令
2. 点击"Environment Variables…"按钮
3. 双击"System variables"下的"Path"进行编辑

> **临时设置（仅当前命令行窗口有效）：**
> ```bash
> set PATH=D:\code\depot_tools;%PATH%
> ```
> 如果不想修改系统环境变量，可以在每次打开新的命令行窗口时运行上述命令来临时设置 PATH。

### 4. 安装 Python 和 Git

```bash
cd c:\code\depot_tools
update_depot_tools.bat
```

> **说明：**
> - 如果脚本执行失败，可以手动安装 Git 最新版本
> - 从 [https://git-scm.com/downloads](https://git-scm.com/downloads) 下载并安装 Git
> - 安装完成后，将 Git 的安装路径（如 `C:\Program Files\Git\cmd`）添加到系统 PATH 环境变量中
> - 重启命令行窗口，运行 `git --version` 验证安装是否成功

### 5. 下载构建脚本

下载 automate-git.py 脚本到 `c:\code\automate\automate-git.py`

### 6. 创建并运行更新脚本

创建 `c:\code\chromium_git\update.bat`：

```batch
set http_proxy=http://192.1.255.23:7890
set https_proxy=http://192.1.255.23:7890


set DEPOT_TOOLS_WIN_TOOLCHAIN=0
set DEPOT_TOOLS_UPDATE=0
set GN_ARGUMENTS=--ide=vs2022 --sln=cef --filters=//cef/*
set CEF_VCVARS=C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Auxiliary\Build\vcvars64.bat
set GYP_MSVS_VERSION=2022
set GYP_MSVS_OVERRIDE_PATH=C:\Program Files\Microsoft Visual Studio\2022\Professional
set WINDOWSSDKDIR=C:\Program Files (x86)\Windows Kits\10
set CEF_ARCHIVE_FORMAT=tar.bz2
set GN_DEFINES=ffmpeg_branding=Chrome proprietary_codecs=true is_official_build=true use_sysroot=true symbol_level=1 is_cfi=false use_thin_lto=false use_vaapi=false chrome_pgo_phase=0 is_component_build=false
set GN_ARGUMENTS=--ide=vs2022 --sln=cef --filters=//cef/*
::python3 D:\code\automate\automate-git.py --chromium-url=https://github.com/chromium/chromium.git --download-dir=D:\code\chromium_git --depot-tools-dir=D:\code\depot_tools --verbose-build --x64-build --no-debug-build --no-build --force-clean
python ..\automate\automate-git.py --download-dir=d:\code\chromium_git --depot-tools-dir=d:\code\depot_tools --branch=7632 --chromium-checkout=refs/tags/145.0.7632.110 --checkout=6ed7554 --no-distrib --no-build --no-chromium-history --force-update
```

运行脚本：

```bash
cd c:\code\chromium_git
update.bat
```

这将下载：

- CEF 源码到 `c:\code\chromium_git\cef`
- Chromium 源码到 `c:\code\chromium_git\chromium\src`
- 完成后将 CEF 源码复制到 `c:\code\chromium_git\chromium\src\cef`

> **脚本说明：**
> - 代理设置：配置 HTTP/HTTPS 代理，用于加速下载
> - 环境变量：配置 Visual Studio 路径、Windows SDK 路径和构建参数
> - GN_DEFINES：启用 H.264 编解码支持（proprietary_codecs=true）
> - `--branch=7632`：指定 CEF 分支号
> - `--chromium-checkout=refs/tags/145.0.7632.110`：指定 Chromium 标签版本
> - `--checkout=6ed7554`：指定 CEF 提交哈希
> - `--force-update`：强制更新（首次构建可使用 `--force-clean`）

> **重要提示 - CefSharp 版本匹配：**
> 如果你使用 [CefSharp](https://github.com/cefsharp/CefSharp)（.NET 绑定），必须确保 CEF 版本与 CefSharp 版本严格一致。版本不匹配会导致运行时错误和崩溃。
> 
> **如何查看版本对应关系：**
> - 查看 [CefSharp GitHub](https://github.com/cefsharp/CefSharp) 仓库的 Release Branches 表格
> - 查看 [CEF Bitbucket](https://bitbucket.org/chromiumembedded/cef/branches/) 的分支列表
> - 例如：CefSharp 143 分支对应 CEF 7499 版本
> 
> **建议：**
> - 在构建 CEF 之前，先确定你要使用的 CefSharp 版本
> - 根据 CefSharp 版本选择对应的 CEF 分支进行构建
> - 使用 `--branch=XXXX` 参数指定 CEF 分支号

> **错误处理 - UnicodeDecodeError：**
> 如果在运行 update.bat 时遇到以下错误：
> ```
> UnicodeDecodeError: 'gbk' codec can't decode byte 0x90 in position 2: illegal multibyte sequence
> ```
> 需要修改 `d:\code\depot_tools\git_common.py` 文件：
> 1. 打开文件 `d:\code\depot_tools\git_common.py`
> 2. 找到第 89 行左右的 `with open(path, 'r') as f:`
> 3. 修改为：`with open(path, 'r', encoding='utf-8', errors='ignore') as f:`
> 4. 保存文件后重新运行 update.bat

### 7. 拉取项目依赖

运行 `gclient runhooks` 来下载和安装所有项目依赖：

```bash
cd c:\code\chromium_git\chromium
gclient runhooks
```

> **重要说明：**
> - 此步骤会下载大量的依赖库和工具，可能需要 30 分钟到 2 小时
> - 首次运行时会下载所有依赖，后续运行会更快
> - 确保网络连接稳定，如果中断可以重新运行该命令
> - 此步骤会自动下载和配置构建工具（如 gn、ninja 等）

> **错误处理 - Python 下载失败：**
> 如果 `gclient sync` 失败，或者卡在 `cef_create_projects.bat` 的报错上，说明 depot_tools 的自动下载机制失败了。最直接的"暴力"解法是：
> 1. 在 `D:\code\depot_tools` 目录下新建一个文件夹，命名为 `python3_bin_reldir`
> 2. 将你系统中现有的 `python.exe` 所在的文件夹内容全部复制到这个 `python3_bin_reldir` 文件夹里
> 3. 在 `D:\code\depot_tools` 根目录下新建文件 `python3_bin_reldir.txt`
> 4. 在 `python3_bin_reldir.txt` 文件中填入以下内容：
> ```
> python3_bin_reldir
> ```
> 完成后重新运行 `gclient runhooks` 命令。

### 8. 配置 H.264 支持（已废弃）

> **此步骤已废弃：**
> H.264 支持现在通过 GN_DEFINES 参数在步骤 6 和步骤 9 中启用，不再需要手动修改配置文件。请跳过此步骤。

为了支持 H.264 视频编解码，需要创建配置文件并运行更新脚本：

创建 `c:\code\chromium_git\h264_config.txt` 文件：

```c
#define CONFIG_FLV_DECODER 1
#define CONFIG_H263_DECODER 1
#define CONFIG_H263I_DECODER 1
#define CONFIG_MPEG4_DECODER 1
#define CONFIG_MPEGVIDEO_DECODER 1
#define CONFIG_MSMPEG4V1_DECODER 1
#define CONFIG_MSMPEG4V2_DECODER 1
#define CONFIG_MSMPEG4V3_DECODER 1
#define CONFIG_RV10_DECODER 1
#define CONFIG_RV20_DECODER 1
#define CONFIG_RV30_DECODER 1
#define CONFIG_RV40_DECODER 1
#define CONFIG_AC3_DECODER 1
#define CONFIG_AMRNB_DECODER 1
#define CONFIG_AMRWB_DECODER 1
#define CONFIG_COOK_DECODER 1
#define CONFIG_SIPR_DECODER 1
#define CONFIG_FLV_ENCODER 1
#define CONFIG_H263_ENCODER 1
#define CONFIG_MPEG4_ENCODER 1
#define CONFIG_MSMPEG4V2_ENCODER 1
#define CONFIG_MSMPEG4V3_ENCODER 1
#define CONFIG_RV10_ENCODER 1
#define CONFIG_RV20_ENCODER 1
#define CONFIG_AAC_ENCODER 1
#define CONFIG_AC3_ENCODER 1
#define CONFIG_AC3_PARSER 1
#define CONFIG_COOK_PARSER 1
#define CONFIG_H263_PARSER 1
#define CONFIG_MPEG4VIDEO_PARSER 1
#define CONFIG_MPEGVIDEO_PARSER 1
#define CONFIG_RV30_PARSER 1
#define CONFIG_RV40_PARSER 1
#define CONFIG_SIPR_PARSER 1
#define CONFIG_AC3_DEMUXER 1
#define CONFIG_AMR_DEMUXER 1
#define CONFIG_AMRNB_DEMUXER 1
#define CONFIG_AMRWB_DEMUXER 1
#define CONFIG_AVI_DEMUXER 1
#define CONFIG_AVISYNTH_DEMUXER 1
#define CONFIG_FLV_DEMUXER 1
#define CONFIG_H263_DEMUXER 1
#define CONFIG_H264_DEMUXER 1
#define CONFIG_MPEGTS_DEMUXER 1
#define CONFIG_MPEGTSRAW_DEMUXER 1
#define CONFIG_MPEGVIDEO_DEMUXER 1
#define CONFIG_RM_DEMUXER 1
#define CONFIG_AC3_MUXER 1
#define CONFIG_AMR_MUXER 1
#define CONFIG_AVI_MUXER 1
#define CONFIG_FLV_MUXER 1
#define CONFIG_H263_MUXER 1
#define CONFIG_H264_MUXER 1
#define CONFIG_MPEGTS_MUXER 1
#define CONFIG_RM_MUXER 1
```

创建 `c:\code\chromium_git\chromium\src\cef\tools\update_h264.py` 脚本：

```python
import sys
import shutil
import re
import os

def Replace(change,content):
  str_array = re.findall("#define\s\w+\s",change)
  str_replace =str_array[0]
  str_replace+="0"
  str_dest =str_array[0]
  str_dest+="1"
  return content.replace(str_replace,str_dest)

if len(sys.argv) > 2 :
  src_file_name =sys.argv[1]
  dest_file_name=sys.argv[2]
else:
  src_file_name =raw_input("Please input src file path name:").replace("\r","")
  dest_file_name =raw_input("Please input dest file path name:").replace("\r","")

print(src_file_name)
print(dest_file_name)

file_src_handle = open(src_file_name,"r")
file_src_lines = file_src_handle.readlines()
file_src_handle.close()
file_dest_handle = open(dest_file_name,"r")
dest_file_content = file_dest_handle.read()
file_dest_handle.close()

for line in file_src_lines:
  dest_file_content = Replace(line,dest_file_content)

write_file_path = os.getcwd()+"\\"+ os.path.basename(dest_file_name)
ready_copy = open(write_file_path,"w")
ready_copy.write(dest_file_content)
ready_copy.close()

shutil.copy(write_file_path,dest_file_name)
os.remove(write_file_path)
print("Support h264 Success!!!")
```

创建 `c:\code\chromium_git\update_h264.bat` 脚本：

```batch
python update_h264.py D:\code\chromium_git/h264_config.txt D:\code\chromium_git\chromium\src\third_party\ffmpeg\chromium\config\Chrome\win\ia32/config.h
python update_h264.py D:\code\chromium_git/h264_config.txt D:\code\chromium_git\chromium\src\third_party\ffmpeg\chromium\config\Chrome\win\x64/config.h
pause
```

运行更新脚本：

```bash
cd c:\code\chromium_git
update_h264.bat
```

> **说明：**
> - 此步骤会修改 Chromium 源码以支持 H.264 编解码
> - H.264 是广泛使用的视频编码格式，支持它能播放更多视频内容
> - 配置完成后，生成的 CEF 将支持 H.264 视频播放
> - 如果不需要 H.264 支持，可以跳过此步骤

### 9. 生成项目文件

创建 `c:\code\chromium_git\chromium\src\cef\create.bat`：

```batch
set DEPOT_TOOLS_WIN_TOOLCHAIN=0
set GN_DEFINES=is_component_build=true proprietary_codecs=true ffmpeg_branding=Chrome
set GN_ARGUMENTS=--ide=vs2022 --sln=cef --filters=//cef/*
call cef_create_projects.bat
```

> **注意：** 如果使用 Visual Studio Code 而非 Visual Studio，请删除 `GN_ARGUMENTS` 行。

运行脚本：

```bash
cd c:\code\chromium_git\chromium\src\cef
create.bat
```

这将生成 `c:\code\chromium_git\chromium\src\out\Debug_GN_x86\cef.sln` 文件，可在 Visual Studio 中加载进行调试和编译单个文件。将路径中的 "x86" 替换为 "x64" 可进行 64 位构建。始终使用 Ninja 构建完整项目。

### 10. 使用 Ninja 构建

构建 Debug 版本（32 位）：

```bash
cd c:\code\chromium_git\chromium\src
ninja -C out\Debug_GN_x86 cef
```

构建 Release 版本（64 位）：

```bash
cd c:\code\chromium_git\chromium\src
ninja -C out\Release_GN_x64 cef
```

> **说明：**
> - 将 "Debug" 替换为 "Release" 可生成 Release 构建
> - 将 "x86" 替换为 "x64" 可生成 64 位构建
> - 使用 `ninja` 而非 `autoninja` 命令

### 11. 运行示例应用

```bash
cd c:\code\chromium_git\chromium\src
out\Debug_GN_x86\cefclient.exe
```

> **错误处理 - cefclient.exe 没有窗口：**
> 如果执行 cefclient.exe 时没有窗口显示，这通常是一个 Web App 数据库版本冲突。简单来说，你当前的 cefclient.exe（版本 6）试图读取一个由更高版本 Chromium（版本 7）生成的 User Data（用户数据目录）。这通常是因为你之前运行过其他版本（或更高版本）的 Chrome/CEF，残留的配置文件导致了初始化失败。
> 
> **解决方法：**
> 
> **1. 清理缓存目录（最推荐）**
> CEF 默认会在用户的 AppData 下生成数据。请删除以下目录，强制程序重新生成纯净的数据：
> 1. 按下 Win + R，输入 `%LOCALAPPDATA%` 并回车
> 2. 找到并删除名为 CEF 的文件夹（或者如果你在代码里指定了 cache_path，请清理对应的位置）
> 
> **2. 使用临时测试目录运行**
> 你可以通过命令行指定一个不存在的临时目录，看看程序能否正常启动：
> ```bash
> .\cefclient.exe --no-sandbox --user-data-dir="D:\cef_test_data"
> ```
> 
> **为什么会发生这种情况？**
> - **版本回退：**你可能在本地切换过 Chromium 的分支（例如从 main 切回了某个 branch），但数据目录没有同步清理
> - **共享路径：**如果你在开发多个基于 CEF 的项目，它们可能都在共用同一个默认的 AppData\Local\CEF 路径

### 12. 生成发布包

构建完成后，可以使用 make_distrib.bat 脚本生成发布包：

```bash
d:\code\chromium_git\chromium\src\cef\tools\make_distrib.bat --ninja-build --x64-build --allow-partial
```

> **说明：**
> - `--ninja-build`：使用 Ninja 构建系统
> - `--x64-build`：生成 64 位版本
> - `--allow-partial`：允许部分构建，即使某些组件构建失败也继续
> - 发布包将生成在 `c:\code\chromium_git\chromium\src\cef\binary_distrib` 目录下

## 构建特定版本分支

要构建最新的发布分支而非 master 分支，在 automate-git.py 命令中添加 `--branch=XXXX` 参数，其中 "XXXX" 是要构建的分支号。

> **注意：** Chromium 构建要求会随时间变化，在尝试构建发布分支前，请先查看 Branches And Building 页面列出的构建要求。

## 参考文档

- [Chromium Windows 构建说明](https://chromium.googlesource.com/chromium/src/+/master/docs/windows_build_instructions.md#Check-python-install) - 官方 Chromium Windows 构建指南

---

*CEF Windows 构建指南 | 基于 Chromium Embedded Framework 官方文档*
