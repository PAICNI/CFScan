

> **CFScan** 是基于 Go 的 Cloudflare IP 优选工具。修改自[CFData-Web](https://github.com/PoemMisty/CFData-WEB)，支持官方地址库抽样、自定义 CIDR 网段全量扫描，以及第三方 IP / 域名与端口测试；将延迟检测、下载测速、链路复验和结果筛选整合到同一界面，优选结果可复制、导出，或追加上报到 CFNext 的「自定义订阅（支持汇聚）」。
>
> **[✈ Telegram 交流群](https://t.me/SZ_PAI)**
> **[✈ Telegram 发布频道](https://t.me/PaiCNX)**
> **[📺️ YouTube 频道](https://www.youtube.com/@PAI_CN)**
> **[▶️ 3.0 新版本部署教程](https://www.youtube.com/watch?v=CQ8zvWXkZzA)**
> **[▶️ 2.0 Worker模式视频使用教程](https://youtu.be/1GCZgdGFsk0)**



## 一、项目概览

| 项目 | 说明 |
| --- | --- |
| 软件名称 | CFScan |
| 显示版本 | **1.0** |
| 主要平台 | Windows x64、Android ARMv7 / ARM64 |
| 核心语言 | Go |
| 界面 | 内置图形界面，支持深色 / 浅色主题 |
| 检测方式 | TCPing / HTTPing、详细延迟测试、下载测速、链路复验 |
| 输入方式 | 官方地址库、自定义 CIDR、本地 TXT / CSV、网络 URL |
| 结果处理 | 筛选、排序、复制、自定义字段、CSV / TXT 导出 |
| CFNext 对接 | Worker 模式下的自定义订阅，默认追加汇聚 |
| 安卓包名 | `com.cfscan.app` |
| 开源许可 | GNU GPL v3 或更新版本 |

## 二、主要功能

| 功能 | 能力与说明 |
| --- | --- |
| 🌐 官方优选 | 从官方地址库抽样检测 IPv4 / IPv6，按数据中心继续详细测试 |
| 🧩 自定义网段 | 输入 CIDR 后全量扫描指定范围，自动归一化并去除重叠地址 |
| 📄 第三方优选 | 从 TXT、CSV 或网络 URL 载入 IP / 域名，支持自定义端口 |
| 📥 CF EDT 汇聚 | 读取导航中的优选域名、IP、地区和运营商来源，合并去重后载入测试 |
| ⚡ 下载测速 | 支持单点、批量测速、测速地址选择和合格速度筛选，速度单位为 MB/s |
| 🔗 链路复验 | 对候选执行链路检测，支持复验后进一步筛选 |
| 🔎 结果处理 | 按条件筛选排序、一键复制、自定义输出字段及 CSV / TXT 导出 |
| ☁️ CFNext 上报 | 手动上报或扫描后自动上报，默认追加汇聚，提交后回读确认 |
| 🖥️ 后台运行 | Windows 隐藏到系统托盘；安卓通过前台服务和通知继续任务 |
| 🛠️ CLI | 提供交互配置、扫描、测速和导出；Linux 可选 systemd 定时任务 |

**扫描范围说明：**默认官方扫描采用分片抽样，IPv4 按 `/24`、IPv6 按 `/48` 抽取候选；自定义 CIDR 输入才采用指定网段的全量扫描。

**CF EDT 拉取说明：**地区使用各地区的 `all.txt`，运营商按接口实际返回结果汇聚。运营商每源请求 10000 条，实际数量受上游限制；失效来源会显示失败记录，不能保证取得失效源或上游未公开的数据。


## 三、下载与安装

### 3.1 选择安装文件

从项目发布文件中选择适合设备的文件：

| 文件 | 适用设备 | 使用方式 |
| --- | --- | --- |
| `CFScan.exe` | Windows 64 位电脑 | 双击运行 |
| `CFScan-android-arm64-v8a.apk` | 64 位 ARM 安卓设备 | 传到手机后安装 |
| `CFScan-android-armeabi-v7a.apk` | 32 位 ARM 安卓设备 | 传到手机后安装 |

- **Windows**：需要系统具备 Microsoft WebView2 Runtime，软件通过内置窗口运行。
- **Android**：支持 Android 8.0 / API 26 及以上；按设备架构选择一种 APK。


## 四、快速上手

### 4.1 官方优选

1. 打开软件，进入 **官方优选**。
2. 选择 IPv4 / IPv6、测试端口、并发数量和延迟阈值。
3. 按需设置测速地址、合格速度、测速结果数量，并在开始前选择是否启用自动测速。
4. 点击 **开始扫描与测试**；启用自动测速时自动继续，否则按数据中心手动继续测试。
5. 在结果区筛选、复制、导出，或上报 CFNext。

### 4.2 自定义 IP 段

在官方优选的 **自定义 IP 段（可选）** 中填写 CIDR。支持逗号、分号或换行分隔，例如：

```text
192.0.2.0/24
198.51.100.0/25
```

以上仅展示输入格式，使用时替换成实际需要测试的网段。

- 留空时使用官方地址库。
- 填写后全量扫描自定义范围，自动归一化主机位并去除重叠地址。
- 网段需与上方选择的 IPv4 / IPv6 一致。
- 单次最多 **16,777,216 个地址**、1024 个输入段，输入文本最多 64 KB。
- 仍按 Cloudflare 节点检测流程筛选；普通可达地址也可能没有合格结果。

### 4.3 第三方优选与 CF EDT 汇聚

进入 **第三方优选**，选择本地 TXT / CSV 文件，或填写网络 URL。也可以使用 **CF EDT 一键拉取**，勾选需要的来源后载入候选。

输入示例：

```text
192.0.2.10:443#示例节点
[2001:db8::10]:8443#IPv6 示例
edge.example.com:2053#域名示例
```

示例地址仅用于说明格式。载入候选后，按需选择 TCPing / HTTPing、TLS、链路复验和下载测速，再开始测试。


## 五、上报 CFNext

### 5.1 手动上报

1. 如 CFNext 面板提供运行模式选择，请使用 **Worker 模式**；没有此选项的旧版可直接继续。
2. 在 CFScan 中完成扫描与筛选，打开 **上报 CFNext** 或结果区的上报入口。
3. 填写面板的**完整地址**和登录密码；前后端分离部署填写**前端 Pages 面板地址**。
4. 选择结果范围，保持默认的 **追加汇聚**，提交上报。
5. CFScan 写入自定义订阅配置并回读，确认上报结果。

| 上报方式 | 行为 |
| --- | --- |
| **追加汇聚（默认）** | 保留原有自定义地址，将新结果合并、去重后追加 |
| 覆盖 | 用本次结果替换现有自定义地址列表，需手动选择 |

### 5.2 自动上报

保存面板信息后，在对应优选模块选择自动上报面板。任务结束时，由内置页面触发上报。

**请保持软件及内置页面运行，直到上报完成。**后台扫描可在页面断开后继续，但无人连接时不会自动触发页面上的上报流程。

### 5.3 历史版本兼容

覆盖CFNext 1.x / 2.x / 3.x 的明文、混淆、小说、Pages 及前后端分离形态，适配登录格式、入口路径和配置接口差异。

## 六、常见问题

<details>
<summary><strong>为什么扫描结束没有合格结果？</strong></summary>

请检查输入地址、端口、TLS 设置和延迟阈值。CFScan 按 Cloudflare 节点检测流程筛选，普通网站或仅 TCP 可达的地址不一定通过。示例中的保留地址用于展示格式，应替换成实际目标。

</details>

<details>
<summary><strong>为什么 CF EDT 拉取数量与导航显示不同？</strong></summary>

地区直接读取 `all.txt`，运营商按上游接口返回的数据汇聚。来源更新、失效、服务端返回数量限制及跨来源去重都会影响最终条数；请同时查看成功 / 失败来源统计。

</details>

<details>
<summary><strong>CFNext 密码正确，为什么仍可能上报失败？</strong></summary>

先检查是否填写了完整面板入口，分离部署是否使用前端 Pages 地址。登录、读取、写入和回读属于不同步骤，请结合软件显示的错误阶段定位。兼容清单之外的新接口、站点访问限制或配置读取异常，也可能影响上报。

</details>

<details>
<summary><strong>如何恢复后台窗口？</strong></summary>

Windows 点击系统托盘里的 CFScan 图标，或使用托盘右键菜单显示窗口。安卓通过任务通知或应用图标返回。后台运行仍受操作系统管理；退出程序进程或强制停止应用会结束任务。

</details>

<details>
<summary><strong>配置保存在哪里？</strong></summary>

运行配置文件名为 `CFScan-config.json`。Windows 界面偏好保存在 `%APPDATA%/CFScan`，安卓保存在应用私有目录。分享文件、截图或反馈日志前，请先移除面板密码和其他私密信息。

</details>

<details>
<summary><strong>CLI 是否包含全部图形界面功能？</strong></summary>

CLI 提供交互配置、扫描、测速和导出。自定义 CIDR 输入、CF EDT 一键拉取及 CFNext 上报目前通过图形界面使用，未提供相应 CLI 参数。

</details>


## 七、源码与构建

```text
CFScan/
├─ combined_refactor/   Go 主程序、嵌入界面、测试与 vendor 依赖
├─ app/                 Android 窗口、后台服务及资源
├─ assets/              图标源文件与 Windows 清单
├─ licenses/            第三方许可与通知原文
└─ LICENSE              项目许可证
```

**实际 Go 模块位于 `combined_refactor/`。**Windows、Android、签名及依赖版本的完整说明见 [BUILDING.md](BUILDING.md)。

Windows 构建：

```powershell
.\scripts\build-release.ps1 -WindowsOnly
```

完整构建需先配置自己的 Android 签名，再运行：

```powershell
.\scripts\build-release.ps1
```

Linux CLI 构建与启动示例：

```sh
cd combined_refactor
go build -o cfscan .
./cfscan -cli
# 后续按已保存配置启动：
./cfscan -cli qs
```

首次直接使用 CLI 时可能先生成配置模板并退出，填写配置后再次运行即可。开源包不包含签名私钥、用户面板密码、本地配置、缓存或历史 CFNext 原始软件。
Windows 托盘恢复及安卓新增后台生命周期仍有实际交互 / 真机验证待完成；具体测试范围以上述验证记录为准。

## 八、开源许可

本项目采用 **GNU GPL v3 或更新版本**，详见 [LICENSE](LICENSE)。第三方组件及嵌入资源分别适用其自身许可，原文见 [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) 和 `licenses/`。

Copyright (C) 2026 PoemMisty。

CFScan 在已有软件基础上修改了界面、Windows / Android 集成、CF EDT 拉取、CFNext 上报兼容及自定义网段等功能，保留原有版权和第三方许可声明。软件按许可证原文提供，不作适用性或其他保证。
