# 修改记录

## 1.0 — 2026-11-03

显示版本固定为 1.0；Android 内部安装序号独立维护。

- Windows 内置 WebView2 窗口、主题标题栏和高 DPI 支持。
- 安卓 V7a / V8a 独立安装包；新 applicationId 为 `com.cfscan.app`，可与 CFData Web 并存，使用独立数据目录。
- 安卓本地服务端口改为 13337，避开旧 CFData Web 的 13335。
- 统一生成 `CFScan-config.json`。
- CF EDT 多源汇聚、地区全量列表拉取、失败源统计。
- CFNext 默认追加汇聚，适配表单 / JSON、独立登录及规范 API 路径、config / bootstrap；53 份历史脚本本地验证通过。
- 官方模块增加自定义 CIDR 输入、全量扫描、归一化及重叠去重；输入框随主题着色。
- Windows 后台托盘、安卓前台服务和通知；扫描进度、DNS 准备及 ASN 非阻塞加载修复。
- 补齐开源构建文档、Go vendor、Gradle Wrapper、Node 锁文件、许可通知和白名单源码打包工具。

详细验证边界见 `docs/VERIFICATION.md`。Windows 托盘恢复和安卓新增后台功能仍有实际交互 / 真机验证待完成，不能视为已覆盖全部设备。
