**[English](README.md)** | 简体中文

# os-findmy-e — 增强版

基于 [sacca97/os-findmy-e](https://github.com/sacca97/os-findmy-e)（GPL-2.0）增强的
Android Find My 协议客户端，全部改动已在真机（Android 14，arm64）上部署验证，并已
向上游提交 [PR #2](https://github.com/sacca97/os-findmy-e/pull/2)。

### 本分支新增

- **静态单钥 Tag 全链路支持**：固定 P-224 钥的第三方 Tag——原生 `fetch_key_reports`
  / `static_keys_info` 命令、独立加密钥文件、安全导入流程、与滚动钥 Tag 共存互不影响
- **厂商 BLE 协议（第二轮）**：响铃（`PL`/`ST`）、电量查询（`GVBAT`）带常驻状态行
  （本地持久化样本、超 12 小时静默重查），以及**只读换钥检测**——从广播帧还原公钥
  并与导入钥比对，可发现自己的 Tag 被第三方覆盖密钥。App 内**不存在任何写钥命令**
- **登录加固**：验证码短信优先；Apple 401 先自动续会话再提示重新登录
- **每周 WorkManager 拉取**：报告落盘持久化、通知不含精确坐标
- **离线底图**：样式模板内置 `file://` 字形/精灵与 `pmtiles://` 占位符，瓦片包自带、
  免重编译换包

### 真机踩坑记录（详见代码注释）

部分 OEM 蓝牙栈必须 `SCAN_MODE_LOW_LATENCY`；硬件厂商数据过滤器会漏掉 scan
response 里的负载；API 33 的 `writeDescriptor`/`writeCharacteristic` 新重载在
discovery 刚结束时同步拒绝（旧 Boolean 重载稳定）；部分 Tag 的 GATT 连接只保持
约 800ms——CCCD 设置需走缓存服务列表并对重复回调幂等。

### 许可

GPL-2.0-only（与上游一致）。本增强版与 Apple 及 Tag 厂商无隶属或背书关系。
