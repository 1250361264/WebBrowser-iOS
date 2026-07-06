# WebBrowser-iOS

基于 [PakePlus-iOS](https://github.com/Sjj1024/PakePlus-iOS) 的 iOS WebView 壳工程，可将任意网页打包为 iOS 应用（IPA）。

## 功能

- SwiftUI + WKWebView 加载远程网页
- 支持自定义应用名、Bundle ID、图标、User-Agent
- 支持 GitHub Actions 云端自动打包
- 提供两种构建方式：Xcode 直接编译 / Theos 打包

## 项目结构

```
.
├── webBrowser/              # Swift 源码
├── webBrowser.xcodeproj/    # Xcode 工程
├── scripts/
│   ├── ppconfig.json        # 构建配置（应用名、URL、版本等）
│   └── ppworker.cjs         # 构建前自动写入配置
├── app-icon.png             # 应用图标源文件
├── .github/workflows/
│   ├── build.yml            # Xcode 编译 + 发布 Release
│   └── build2.yml           # Theos 编译 + 发布 Release
└── Makefile                 # Theos 构建规则（build2 使用）
```

## 配置

编辑 `scripts/ppconfig.json` 中的 `ios` 字段：

```json
{
  "ios": {
    "name": "WebBrowser",
    "showName": "WebBrowser",
    "version": "1.1.0",
    "webUrl": "https://example.com/",
    "id": "com.example.app",
    "icon": "./app-icon.png",
    "desc": "",
    "pubBody": "Release notes",
    "debug": false
  }
}
```

| 字段 | 说明 |
| --- | --- |
| `name` | 内部名称，用于 Release 文件名 |
| `showName` | 安装后显示的应用名称 |
| `version` | 版本号 |
| `webUrl` | WebView 加载的首页地址 |
| `id` | Bundle Identifier |
| `icon` | 图标路径（相对项目根目录） |
| `pubBody` | GitHub Release 说明文字 |
| `debug` | 是否开启调试模式 |

执行 `pnpm pp:worker` 会将以上配置写入 Xcode 工程，并设置 GitHub Actions 环境变量。

## 本地开发

**环境要求**

- macOS + Xcode（建议最新稳定版）
- Node.js >= 22
- pnpm 9

**安装依赖**

```bash
pnpm install
pnpm pp:worker
```

**Xcode 编译**

```bash
open webBrowser.xcodeproj
```

在 Xcode 中选择目标设备或模拟器，直接 Run 调试。

**Theos 打包（可选）**

```bash
export THEOS=~/theos
make package FINALPACKAGE=1 PACKAGE_FORMAT=ipa
```

## GitHub Actions 云端打包

1. 将仓库 Fork 或 Push 到你的 GitHub
2. 确认 `scripts/ppconfig.json` 配置正确
3. 打开 **Actions** → 选择 **Build IPA** → **Run workflow**

### 两种 Workflow

| Workflow | 文件 | 说明 |
| --- | --- | --- |
| Build IPA (Xcode) | `build.yml` | 使用 `xcodebuild` 编译，无需 Theos |
| Build IPA (Theos) | `build2.yml` | 使用 Theos 打包，适合越狱/重签名场景 |

### CI 环境

- Runner: `macos-latest`
- Xcode: `latest-stable`（自动适配 macOS 15/26 runner）
- Node.js: 22
- pnpm: 9

打包完成后，IPA 会自动上传到 GitHub Releases。

## 常见问题

**1. 安装后提示「无法验证开发者」**

本仓库 CI 产出为未签名 IPA，需自行重签名或使用 AltStore / Sideloadly 等工具安装。

**2. macOS 提示「应用已损坏」**

```bash
sudo xattr -r -d com.apple.quarantine /Applications/YourApp.app
```

**3. CI 中 Xcode 版本找不到**

GitHub 正在将 `macos-latest` 从 macOS 15 迁移到 macOS 26，旧版 Xcode（如 16.x）在新 runner 上不可用。本项目已使用 `latest-stable` 自动选择当前 runner 支持的最新稳定版。

## 致谢

- 基于 [PakePlus](https://github.com/Sjj1024/PakePlus) / [PakePlus-iOS](https://github.com/Sjj1024/PakePlus-iOS)
- 作者：1024小神

## License

[MIT](LICENSE)
