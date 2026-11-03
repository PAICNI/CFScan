# CFScan

基于 Go 的 Cloudflare IP 优选工具，修改自[CFData-Web](https://github.com/PoemMisty/CFData-WEB)，提供 Windows 独立窗口、Android 应用和 CLI。

## 功能

- 官方 IPv4 / IPv6 优选、数据中心筛选、详细延迟测试及下载测速。
- 可选自定义 CIDR 网段：逗号或换行分隔，全量扫描，自动归一化并去除重叠地址；单次最多 16,777,216 个地址。
- 第三方 TXT / CSV / 网络 URL 输入，支持 IP、域名及自定义端口。
- CF EDT 数据汇聚：地区读取 `all.txt`；运营商返回数量取决于上游接口，界面展示成功及失败源数。
- 链路复验、筛选排序、CSV / TXT 导出、自定义结果字段。
- CFNext 自定义订阅上报：默认追加汇聚，可手动选择覆盖；支持保存面板信息及扫描后自动上报。
- 适配不同 CFNext 登录格式、登录入口、API 路径和配置接口。历史目录的 53 份脚本已完成本地上报兼容验证，见 [兼容清单](docs/CFNEXT_COMPATIBILITY.md)。
- Windows 点击“转入后台运行”隐藏到系统托盘；安卓通过前台服务与通知继续任务。
- CLI 交互配置；Linux 可选 systemd 定时任务。

## 安装与使用

Windows 使用发布文件 `CFScan.exe`，通过内置 WebView2 独立窗口运行，需要系统安装 Microsoft WebView2 Runtime。

安卓 Android 8.0 / API 26 起，按设备选择 `arm64-v8a` 或 `armeabi-v7a` APK。包名为 **`com.cfscan.app`**，与 CFData Web 的 `com.cfdata.web` 可以同时安装，数据相互独立，新包不会自动读取旧包设置。CFScan 安卓本地后端使用 13337 端口，避开旧软件的 13335。对外版本为 1.0，Android 内部安装序号独立维护。

首次启动生成 `CFScan-config.json`。Windows 界面偏好保存在 `%APPDATA%/CFScan`；安卓保存在应用私有目录。

CFNext 填写完整的面板地址和密码；前后端分离部署填写前端 Pages 的面板地址。自动上报需要保持软件运行；覆盖会替换现有自定义列表。

## 源码与构建

实际 Go 模块在 **`combined_refactor/`**，根目录是 Android / 构建工程入口。源码包不包含旧的根目录 Go / HTML 实现。

- [构建说明](BUILDING.md)：Go、Windows、Android、签名、图标和验证脚本。
- [第三方许可](THIRD_PARTY_NOTICES.md)：许可证及通知原文在 `licenses/`，Go 依赖源码在 `combined_refactor/vendor/`。
- [验证记录](docs/VERIFICATION.md)：已经验证的功能与尚未完成的真机验证。
- [修改记录](CHANGELOG.md)、[贡献说明](CONTRIBUTING.md)、[安全问题说明](SECURITY.md)。

只构建 Windows，在项目根目录的 Windows PowerShell 中运行：

```powershell
.\scripts\build-release.ps1 -WindowsOnly
```

构建所有发布文件前，按 BUILDING.md 安装 JDK / Android SDK / NDK，并配置自己的签名环境变量，然后运行：

```powershell
.\scripts\build-release.ps1
```

默认输出到 `release_assets/1.0/`。源码包不包含开发者的签名私钥、用户面板密码、本地配置、缓存、历史 CFNext 原始项目或工具链安装包。

## 开源许可

本项目沿用 **GNU GPL v3 或更新版本**，见 [LICENSE](LICENSE)。

Copyright (C) 2026 PoemMisty。

本次 CFScan 开发在已有软件基础上修改了界面、Windows / Android 集成、CF EDT 拉取、CFNext 上报兼容和自定义网段等功能。保留原有版权及第三方许可声明。

软件按许可原文提供，不作适用性或其他保证。发布源码或二进制时应一并保留适用的许可证、版权和第三方通知。
