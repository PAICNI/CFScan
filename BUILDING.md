# 构建 CFScan 1.0

显示版本固定为 `1.0`。Android 包名为 `com.cfscan.app`，Java 源码位于 `app/src/main/java/com/cfscan/app/`。请在源码根目录执行本文命令，Go 命令明确注明目录时除外。

## 工具版本

| 工具 | 本次验证版本 | 用途 |
| --- | --- | --- |
| Go | 1.26.2 | 主程序、测试及所有平台后端 |
| PowerShell | Windows PowerShell 5.1 / PowerShell 7 语法 | Windows 构建脚本；本次实际使用 PowerShell 7 |
| JDK | 17 | Android Gradle 构建及签名 |
| Gradle | 8.9，Wrapper 固定下载及 SHA256 | Android 构建 |
| Android Gradle Plugin | 8.7.3 | `build.gradle` 中固定 |
| Android SDK | platform android-35、build-tools 35.0.0、platform-tools | APK、签名与可选 ADB 验证 |
| Android NDK | 28.2.13676358 / r28c | ARMv7 Go 后端及 16 KB ELF 页对齐 |
| Node | 22.12.0 以上，本次 24.22.1 | 仅图标再生成和可选开发验证 |
| Node 开发依赖 | JSZip 3.10.1、Playwright 1.62.1、Sharp 0.35.5 | `package-lock.json` 固定依赖树 |

Go 应用依赖已纳入 `combined_refactor/vendor/`，并保留 `go.mod` / `go.sum`。未包含 Go / JDK / Android SDK / 浏览器的完整安装包。首次运行 Gradle Wrapper、Android 插件和 `rsrc` 资源生成工具需要联网下载，或预先准备相应缓存。图标成品已包含，正常编译不需要安装 Node。

## Windows EXE

安装 Go 1.26.2 并加入 PATH；也可以显式指定：

```powershell
$env:GO_EXE = 'D:\Tools\go\bin\go.exe'
.\scripts\build-release.ps1 -WindowsOnly
```

脚本会生成 Windows `.syso` 资源、运行 `go test ./...` 和 `go vet ./...`，再生成 `release_assets/1.0/CFScan.exe` 与相同内容的 `CFScan-windows-amd64.exe`。另外生成 Linux 测试文件 `cfdata-test`。

WebView2Loader 已由 Go 依赖嵌入；最终 EXE 使用系统 Microsoft WebView2 Runtime，不附带外部浏览器窗口或完整浏览器安装包。图标和清单的源文件在 `assets/`，`.syso` 是构建产物。

## Android V7a / V8a

配置 JDK 17 和 Android SDK 路径；Android Studio 安装的 SDK 同样可用：

```powershell
$env:JAVA_HOME = 'D:\Tools\jdk-17'
$env:ANDROID_HOME = 'D:\Android\Sdk'
```

通过 SDK Manager 安装：

```powershell
sdkmanager.bat 'platform-tools' 'platforms;android-35' 'build-tools;35.0.0' 'ndk;28.2.13676358'
```

若 NDK 不在 SDK 默认目录，设置 `ANDROID_NDK_HOME`。脚本优先使用项目 `gradlew.bat`；可用 `GRADLE_EXE` 指定已有的 Gradle 8.9。Wrapper 在 `gradle/wrapper/gradle-wrapper.properties` 中固定官方分发文件校验值。

### 准备自己的签名

源码包和发布合集**不包含发布签名私钥**。请将自己的密钥放在工程目录之外。以下命令通过交互方式输入密码：

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE/.cfscan-keys" | Out-Null
& "$env:JAVA_HOME/bin/keytool.exe" -genkeypair -keystore "$env:USERPROFILE/.cfscan-keys/release.jks" -alias cfscan -keyalg RSA -keysize 3072 -validity 10000
$env:ANDROID_KEYSTORE_FILE = "$env:USERPROFILE/.cfscan-keys/release.jks"
$env:ANDROID_KEY_ALIAS = 'cfscan'
$signingSecret = Read-Host '签名库密码' -AsSecureString
$env:ANDROID_KEYSTORE_PASSWORD = [Net.NetworkCredential]::new('', $signingSecret).Password
$env:ANDROID_KEY_PASSWORD = $env:ANDROID_KEYSTORE_PASSWORD
```

如果设置了独立的 key 密码，请另行填写 `ANDROID_KEY_PASSWORD`。妥善保存自己的签名，后续更新需要保持一致。自己签名的 APK 与官方发布包的签名不同，不能直接覆盖安装官方包；不要将私钥或密码提交到公开仓库。

```powershell
.\scripts\build-release.ps1
# 或仅构建 Android：
.\scripts\build-release.ps1 -AndroidOnly
```

输出：`CFScan-android-arm64-v8a.apk`、`CFScan-android-armeabi-v7a.apk`。最低 API 26，target / compile API 35；`versionName` 固定 1.0，内部 `versionCode` 为 5。改变 applicationId 会产生独立应用，无法自动继承其他包的私有数据。

调试 WebView / ADB 时，可在生成 Go 后端后执行 `./gradlew.bat assembleDiagnostic`。诊断变体允许调试；不要将它当作正式发布包。正式 `release` 不开启 WebView 远程调试。

## Go 测试与 Linux CLI

```powershell
Set-Location combined_refactor
go test ./...
go vet ./...
$env:CGO_ENABLED = '0'
$env:GOOS = 'linux'
$env:GOARCH = 'amd64'
go build -trimpath -ldflags '-s -w -X main.appVersion=1.0' -o ../release_assets/cfdata-test .
Remove-Item Env:GOOS, Env:GOARCH, Env:CGO_ENABLED
```

Linux / macOS 可在 `combined_refactor/` 中使用原生 `go test ./...`；Linux CLI 的构建使用上面等价的环境变量和 `go build`。完整 Android / Windows 发布脚本在 Windows 上验证。

Windows 生成的源码 ZIP 不保留 Unix 可执行位。Linux / macOS 如需运行 Gradle Wrapper，先执行 `chmod +x gradlew`，或使用 `sh ./gradlew --version`。

## 图标与开发验证

```powershell
npm ci
npm run icons
```

输入是 `assets/icon.svg`，输出是 ICO、页面 favicon 和 Android 各密度 PNG。该命令会覆盖这些生成资产。

Web UI 验证脚本需要 Chrome。对一个独立测试服务设置 `CFSCAN_URL` 后，可执行 `npm run verify:ui`、`verify:live`、`verify:workflows`；部分命令会真实扫描、测速和操作本地测试配置。请先阅读各脚本开头，避免连接日常使用的实例。

`verify:live` / `verify:workflows` 只有显式提供 `CFNEXT_HOST` 和 `CFNEXT_PASSWORD` 才会执行可选线上追加测试，不要在公开终端记录或文件里写真实密码。

CFNext 历史兼容复验：

```powershell
npm run verify:cfnext -- 'D:\PrivateTests\CFNext-history'
```

历史源码并非本项目构建依赖，不包含在公开源码包中；需自行准备有权使用的样本。脚本本地执行这些 Worker，模拟 KV 及平台 API，并阻止 Worker 请求外部网络。已有 53 份脚本的结果和哈希在 `docs/CFNEXT_COMPATIBILITY.md`。

安卓验证需要已授权 ADB 设备和诊断变体的 WebView CDP 转发；使用 `npm run verify:android` 前按具体设备设置转发。新包名为 `com.cfscan.app`，安卓后端和页面使用 `127.0.0.1:13337`；可通过 `CFSCAN_ANDROID_URL` 指定验证页面地址。

## 打包开源材料

```powershell
.\scripts\package-source.ps1
```

输出为 `release_assets/CFScan-1.0-source.zip` 及包含源码和正式安装包的 `CFScan-1.0-open-source-release.zip`。采用白名单收集文件，并生成逐文件 SHA256 清单；不读取或分发私钥、用户配置、缓存、Git 历史或其他项目的源码。发布合集需要先准备好 `release_assets/1.0/` 中的正式 EXE 和两个 APK。
