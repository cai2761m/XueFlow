<p align="center">
  <img src="src-tauri/icons/icon.png" width="112" alt="XueFlow 图标">
</p>

<h1 align="center">XueFlow</h1>

<p align="center">
  <strong>学习通课程自动化辅助客户端</strong><br>
  课程任务推进 · 章节进度展示 · 混淆字体还原 · AI 辅助答题
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Tauri-2-24C8DB?style=flat-square&logo=tauri&logoColor=white" alt="Tauri 2">
  <img src="https://img.shields.io/badge/Vue-3-42B883?style=flat-square&logo=vuedotjs&logoColor=white" alt="Vue 3">
  <img src="https://img.shields.io/badge/Rust-DEA584?style=flat-square&logo=rust&logoColor=white" alt="Rust">
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-GPL--3.0-64748B?style=flat-square" alt="GPL-3.0"></a>
</p>

<p align="center">
  <a href="#快速开始">快速开始</a> ·
  <a href="https://github.com/cai2761m/XueFlow/releases">查看发布</a> ·
  <a href="docs/usage/desktop.md">使用指南</a> ·
  <a href="docs/backend-free.md">独立脚本</a> ·
  <a href="https://github.com/cai2761m/XueFlow/issues">反馈问题</a>
</p>

XueFlow 将学习通课程页面、任务进度和模型配置集中在一个桌面窗口中。客户端通过内嵌 WebView 打开课程，在识别到支持的章节页面后注入自动化脚本；确认启动后，按课程目录处理任务并同步进度。

项目使用 **Rust + Tauri 2 + Vue 3 + TypeScript**，同时保留可直接复制到浏览器控制台运行的单文件脚本。桌面端使用本地 Rust 后端处理字体与模型请求，无需另行部署应用服务。

## 界面预览

<p align="center">
  <img src="docs/assets/overview.webp" width="960" alt="XueFlow 课程看板、配置面板与内嵌课程页面预览">
</p>

## 主要功能

| 功能         | 说明                                                                       |
| ------------ | -------------------------------------------------------------------------- |
| 课程任务推进 | 识别章节、页签与任务点，跳过已完成内容，继续处理未完成章节                 |
| 视频任务     | 自动播放、静音、倍速锁定与暂停恢复；处理脚本可识别的视频内互动题           |
| PDF 阅读     | 自动滚动内嵌阅读页面，并等待任务点状态更新                                 |
| 章节测验     | 提取题目、请求模型答案，支持单选、多选、判断、填空和简答题的自动填答与提交 |
| 混淆字体还原 | 根据 TTF 字形特征还原 `font-cxsecret` 题目文字，再交给模型处理             |
| 课程看板     | 展示课程信息、当前章节、任务状态与章节完成进度                             |
| 页面控制     | 在客户端内后退、前进、刷新、返回首页与调整页面缩放                         |

任务处理依赖学习通页面结构，目前覆盖脚本已适配的视频、PDF 和章节测验。特殊课程、其他任务类型及页面改版可能需要进一步适配。

## 快速开始

### 获取客户端

在 [Releases](https://github.com/cai2761m/XueFlow/releases) 查看可用发行包与版本说明。没有适合当前系统的发行包时，可按下方步骤从源码运行。

项目主要在 Windows x64 环境使用。仓库的[发布流程](.github/workflows/release.yml)包含 Windows 便携 EXE、MSI 和 macOS ARM64 DMG 的构建；实际可下载的平台与版本以发布附件为准。

### 启动课程任务

1. 打开客户端，在内嵌课程页面登录学习通，进入目标课程的章节播放页。
2. 在 **Configuration → Course** 中设置静音、播放速度与倍速锁定，点击 **Save** 保存。
3. 如需处理章节测验，先在 **Configuration → API** 中配置模型；仅处理视频或 PDF 时可跳过。
4. 课程脚本加载后，在确认遮罩中点击 **Yes** 启动，点击 **No** 取消。
5. 在课程看板查看进度。运行中可通过课程控制栏刷新或按 <kbd>F5</kbd> 终止当前脚本；刷新后需重新确认启动。

桌面端会自动注入脚本，无需手动打开控制台。支持的课程入口及操作细节见[桌面端使用指南](docs/usage/desktop.md)和[故障排查](docs/troubleshooting.md)。

## AI 辅助答题

在 **Configuration → API** 选择 **Provider** 和 **Model**，输入对应 **API Key**；输入完成后先移开焦点，再点击 **Save**。

内置供应商配置包括 **DeepSeek、OpenAI、Google Gemini、Moonshot、智谱 BigModel、OpenRouter 和 Ollama**。模型是否可用取决于服务端及账户权限，配置中的预设列表不代表服务始终可用。

- **自定义服务：** 可新增 Provider、设置 Endpoint 并添加模型。自定义 Provider 使用 OpenAI Chat Completions 协议，支持 `/v1` 基础地址或完整接口 URL。
- **本地模型：** 内置 Ollama 预设的模型列表为空，当前界面不能为该预设添加模型。可新增自定义 Provider，使用 `http://localhost:11434/v1/chat/completions`，填写本机已安装的模型名称，API Key 留空；需先启动提供该接口的 Ollama 服务。
- **提交行为：** 章节测验在成功填答后会自动提交，正常流程不会逐题询问是否确认。模型答案可能出错，请在启动前确认是否使用该功能。

API Key 以明文保存在本地配置中；题目内容会发送到所选模型服务，非空密钥用于请求认证。请使用可信的 Endpoint，分享配置或日志前移除密钥与个人信息。完整配置说明见[智能答题指南](docs/usage/ai.md)。

## 浏览器独立脚本

不使用桌面客户端时，也可直接运行 [`core.js`](src-tauri/src/scripts/core.js)：

1. 在浏览器登录学习通，进入支持的课程章节播放页。
2. 按 <kbd>F12</kbd> 打开开发者工具，切换到 **Console**。
3. 复制 `core.js` 的全部内容，粘贴并执行，按提示确认启动。

| 能力                      | 桌面端                 | 独立脚本           |
| ------------------------- | ---------------------- | ------------------ |
| 视频、PDF 与章节推进      | 支持                   | 支持               |
| 静音与倍速配置            | 在界面设置并保存       | 修改脚本内配置     |
| 课程看板与任务状态        | 客户端界面             | 控制台日志         |
| 混淆字体还原、AI 章节测验 | 本地后端处理           | 不提供             |
| 脚本加载                  | 识别课程页面后自动注入 | 每次刷新后手动执行 |

独立脚本默认静音、关闭倍速锁定，不主动修改播放速度；开启脚本内的倍速锁定后才会应用配置倍速。遇到需要后端的章节测验时会报错，并跳过该页签剩余任务。图文步骤见[无后端模式说明](docs/backend-free.md)。

## 从源码运行与构建

准备 **Node.js、pnpm、Rust stable / Cargo**，以及对应系统的 [Tauri 2 前置依赖](https://v2.tauri.app/start/prerequisites/)。仓库 CI 使用 Node.js `22.18.0`、pnpm `12.3.4` 和 Rust stable，项目未声明最低 Rust 版本。

Windows 还需要 Microsoft C++ Build Tools、WebView2 Runtime，以及仓库配置指定的 `lld-link.exe`。当前 Windows 编译目标使用 `x86-64-v3`，运行该构建产物的处理器也需支持此指令集级别。

```bash
git clone https://github.com/cai2761m/XueFlow.git
cd XueFlow
pnpm install --frozen-lockfile
pnpm exec tauri dev
```

构建桌面发行包：

```bash
pnpm exec tauri build
```

开发检查：

```bash
pnpm test
pnpm run build
cargo test --locked --manifest-path src-tauri/Cargo.toml
cargo test --locked -p uxs_commands
```

`pnpm run dev` 仅启动 Vue 开发页面；验证 IPC、课程 WebView 和本地配置读写时，请启动完整 Tauri 客户端。工具链、打包与本地数据目录说明见[开发与构建指南](docs/development/build.md)。

## 文档导航

| 文档                                      | 内容                                          |
| ----------------------------------------- | --------------------------------------------- |
| [桌面端使用](docs/usage/desktop.md)       | 配置面板、课程启动、页面控制与进度展示        |
| [智能答题](docs/usage/ai.md)              | 模型服务、自定义 Endpoint、本地模型与字体还原 |
| [无后端模式](docs/backend-free.md)        | 浏览器控制台执行步骤与功能边界                |
| [常见问题](docs/troubleshooting.md)       | 页面识别、任务状态、模型请求与兼容性排查      |
| [开发与构建](docs/development/build.md)   | 环境准备、测试、构建、发布与配置存储          |
| [架构概览](docs/architecture/overview.md) | Vue、Rust、课程 WebView 和注入脚本的协作流程  |

## 反馈与贡献

通过 [Issues](https://github.com/cai2761m/XueFlow/issues) 反馈问题，请提供客户端版本、操作系统、课程或任务类型、复现步骤，以及脱敏后的截图和错误日志。功能改进与页面适配可通过 Pull Request 提交。

参与开发前请阅读 [AGENTS.md](AGENTS.md) 与 [编码行为准则](CODE_OF_CONDUCT.md)，并按改动范围执行对应检查。

## 项目来源与许可

XueFlow 在 [uXueScript](https://github.com/unraous/uxuescript) 基础上继续开发，原项目由 [unraous](https://github.com/unraous) 创建；浏览器独立脚本模式延续了原项目的交付方式。

核心源代码依据 [GNU GPL v3.0](LICENSE) 发布。第三方字体、字形特征哈希表与依赖库的归属和许可见 [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md)。

请按所在学校与课程的要求使用本工具。自动化仅覆盖已适配的页面与任务，模型答案和任务完成情况均应以实际课程页面为准。
