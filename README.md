# 文档中心

本目录包含 Chromium Embedded Framework (CEF) 相关的技术文档和构建指南。

## 主题介绍

Chromium Embedded Framework (CEF) 是一个基于 Google Chromium 项目的开源框架，允许开发者在自己的应用程序中嵌入 Chromium 浏览器引擎。CEF 提供了跨平台的 C/C++ API，支持 Windows、Linux 和 macOS 等操作系统。

CEF 的主要特点：
- 基于 Chromium 最新版本，提供最新的 Web 标准支持
- 提供完整的浏览器功能，包括 HTML5、CSS3、JavaScript 等
- 支持自定义扩展和插件
- 提供详细的 API 接口，方便集成到现有应用中
- 广泛应用于桌面应用、游戏引擎、企业应用等领域

## 当前资源

### CEF Windows 构建指南

详细介绍了在 Windows 平台上从源码构建 CEF 的完整流程，包括：

- **系统要求**：硬件配置、构建时间估算
- **文件结构**：源码目录组织说明
- **资源下载**：构建所需的所有工具和依赖
- **构建步骤**：
  - 环境配置（depot_tools、Python、Git）
  - 源码下载（CEF 和 Chromium）
  - 项目文件生成
  - Ninja 编译
  - 示例应用运行
  - 发布包生成
- **特定版本构建**：如何构建指定版本的 CEF
- **常见问题**：错误处理和故障排除

适用场景：
- 需要自定义 CEF 功能的开发者
- 需要集成特定版本 CEF 的项目
- 学习 CEF 源码和构建流程的开发者

> **注意**：本指南适用于开发环境搭建和源码构建，不适用于寻找预构建二进制分发的用户。

## 文档格式

本目录提供多种格式的文档：
- Markdown (.md)：适合阅读和版本控制
- HTML (.html)：适合在浏览器中查看

## 相关资源

- [CEF 官方网站](https://bitbucket.org/chromiumembedded/cef)
- [CEF GitHub 仓库](https://github.com/chromiumembedded/cef)
- [Chromium 官方文档](https://www.chromium.org/developers)
- [CefSharp (.NET 绑定)](https://github.com/cefsharp/CefSharp)