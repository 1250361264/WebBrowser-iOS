# WebBrowser-iOS

基于 PakePlus-iOS 思路的 iOS WebView 壳工程，可将任意网页打包为 iOS 应用（IPA）。

## 功能

- SwiftUI + WKWebView 加载远程网页
- 支持自定义应用名、Bundle ID、图标、User-Agent
- 支持本地 Xcode 编译与 Theos 打包

## 项目结构

```
.
├── webBrowser/              # Swift 源码
├── webBrowser.xcodeproj/    # Xcode 工程
├── scripts/
│   ├── ppconfig.json        # 构建配置（应用名、URL、版本等）
│   └── ppworker.cjs         # 构建前自动写入配置
├── app-icon.png             # 应用图标源文件
└── Makefile                 # Theos 构建规则
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
| `name` | 内部名称 |
| `showName` | 安装后显示的应用名称 |
| `version` | 版本号 |
| `webUrl` | WebView 加载的首页地址 |
| `id` | Bundle Identifier |
| `icon` | 图标路径（相对项目根目录） |
| `pubBody` | 发布说明文字 |
| `debug` | 是否开启调试模式 |

执行 `pnpm pp:worker` 会将以上配置写入 Xcode 工程。

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

## 常见问题

**1. 安装后提示「无法验证开发者」**

未签名 IPA 需自行重签名或使用 AltStore / Sideloadly 等工具安装。

**2. macOS 提示「应用已损坏」**

```bash
sudo xattr -r -d com.apple.quarantine /Applications/YourApp.app
```

## 致谢

本项目参考了 PakePlus-iOS 的 WebView 壳实现思路。

## License

[MIT](LICENSE)
