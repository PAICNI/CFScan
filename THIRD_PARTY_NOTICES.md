# 第三方组件与许可说明

CFScan 项目自身沿用根目录 `LICENSE` 中的 GNU GPL v3，并按项目已有声明采用 GPL-3.0-or-later。保留原有版权声明：Copyright (C) 2026 PoemMisty。第三方组件分别适用其自身许可，不因放在同一源码包中而改变许可。

下列许可证、版权和通知原文位于 `licenses/`；Go 依赖的原始声明也保留在 `combined_refactor/vendor/`。本说明区分实际应用依赖和构建、测试工具。

## 应用与嵌入资源

| 组件 | 版本 / 来源标识 | 许可 | 原文 |
| --- | --- | --- | --- |
| Go 标准库与运行时 | Go 1.26.2 | BSD-3-Clause，并附专利授权 | [LICENSE](licenses/go/LICENSE.txt)、[PATENTS](licenses/go/PATENTS.txt) |
| github.com/gorilla/websocket | v1.5.3 | BSD-2-Clause | [LICENSE](licenses/gorilla-websocket/LICENSE.txt) |
| github.com/jchv/go-webview2 | v0.0.0-20260205173254-56598839c808 | MIT | [LICENSE](licenses/go-webview2/LICENSE.txt) |
| WebView2Loader SDK | 随上述 go-webview2 提交嵌入的 SDK loader | BSD-3-Clause，Microsoft Corporation | [LICENSE](licenses/go-webview2/WebView2Loader-LICENSE.txt) |
| github.com/jchv/go-winloader | v0.0.0-20250406163304-c1995be93bd1 | ISC | [LICENSE](licenses/go-winloader/LICENSE.md) |
| github.com/oschwald/maxminddb-golang | v1.13.1 | ISC | [LICENSE](licenses/maxminddb-golang/LICENSE.txt) |
| golang.org/x/net | v0.61.0 | BSD-3-Clause，并附专利授权 | [LICENSE](licenses/golang-x-net/LICENSE.txt)、[PATENTS](licenses/golang-x-net/PATENTS.txt) |
| golang.org/x/sys | v0.49.0 | BSD-3-Clause，并附专利授权 | [LICENSE](licenses/golang-x-sys/LICENSE.txt)、[PATENTS](licenses/golang-x-sys/PATENTS.txt) |
| Mozilla CA 证书集合的 PEM 转换文件 | `combined_refactor/ca-certificates.crt`，文件头标注 Mozilla 数据日期为 2026-02-11，mk-ca-bundle.pl 1.32 | MPL-2.0 | [MPL-2.0](licenses/mozilla-ca-bundle/MPL-2.0.txt) |

CA 证书集合以可修改的 PEM 源形式直接包含在源码包中，保留其原始来源说明和证书数据；也作为系统证书不可用时的后备资源嵌入应用。curl 的[CA Extract 来源说明](https://curl.se/docs/caextract.html)明确该转换文件沿用 Mozilla 源文件的 MPL-2.0。MPL 许可原文来源为 [Mozilla 官方文本](https://www.mozilla.org/media/MPL/2.0/index.txt)。本次整理没有更新或替换证书内容。

Windows 的 Microsoft WebView2 Runtime 和安卓系统 WebView 是平台运行组件。本分发没有捆绑其完整运行时安装程序；上表单独列出的是 EXE 依赖内嵌的 WebView2Loader。

## 构建与测试工具

这些工具供构建脚本、图标生成或测试使用，不属于应用的 Go 业务运行依赖。除 Gradle Wrapper 等明确包含的文件外，完整工具分发包、Node 模块和浏览器不在源码包内；根据构建说明单独安装。

| 工具 | 版本 | 许可及通知 |
| --- | --- | --- |
| github.com/akavel/rsrc | v0.10.2 | MIT：[LICENSE](licenses/rsrc/LICENSE.txt) |
| Gradle / Gradle Wrapper | 8.9 | Apache-2.0 及原分发附列许可：[LICENSE](licenses/gradle/LICENSE.txt)、[NOTICE](licenses/gradle/NOTICE.txt) |
| Android NDK | r28c / 28.2.13676358 | 原分发组件许可合订：[NOTICE](licenses/android-ndk/NOTICE.txt)、[toolchain notice](licenses/android-ndk/NOTICE-toolchain.txt)、[LLVM notice](licenses/android-ndk/LLVM-NOTICE.txt) |
| sharp | 0.35.5 | Apache-2.0：[LICENSE](licenses/sharp/LICENSE.txt) |
| JSZip | 3.10.1 | MIT 或 GPL-3.0-or-later，按其双许可声明选择：[LICENSE](licenses/jszip/LICENSE.markdown) |
| Playwright | 1.62.1 | Apache-2.0：[LICENSE](licenses/playwright/LICENSE.txt)、[NOTICE](licenses/playwright/NOTICE.txt) |
| playwright-core | 1.62.1 | Apache-2.0：[LICENSE](licenses/playwright-core/LICENSE.txt)、[NOTICE](licenses/playwright-core/NOTICE.txt) |

NDK 的通知文件包含原分发中的 CRT、LLVM 及其他组件声明，用于保留原始工具链通知；不表示本应用使用了通知中列出的每个组件。Android Gradle Plugin、JDK、Android SDK、NDK、Node、浏览器等外部工具依其各自分发条款提供，本包没有将它们重新许可为 GPL。

## 标识与运行时数据

`assets/icon.svg` 是本次项目按用户要求采用的 CFNext 标识的矢量源；ICO、PNG 和安卓各密度图标由 `scripts/build-icons.cjs` 从该源生成。CFNext、Cloudflare、Microsoft、Mozilla 及其他名称、标识、商标和相关权益由相应权利人保留。本文件不作额外商标或品牌授权声明。

用户测速结果、个人面板配置、密码、签名私钥、ASN 数据库及运行时下载的数据不包含在开源源码包中。MaxMind DB 读取库的 ISC 许可不等于某个外部 ASN 数据库或远程数据服务的许可；使用这些外部资源时仍需遵循其各自的提供条件。
