# os-findmy-e — enhanced edition

Enhanced Android client for Apple Find My–protocol tags, based on
[sacca97/os-findmy-e](https://github.com/sacca97/os-findmy-e) (GPL-2.0).
All changes are also proposed upstream via
[sacca97/os-findmy-e#2](https://github.com/sacca97/os-findmy-e/pull/2).

**Deployed and verified on-device** (Android 14, arm64) against a third-party
static-key Find My tag (DEONE MTAG).

## What this branch adds

- **Static single-key tags** — full support for tags with a fixed P-224 key:
  native `fetch_key_reports`/`static_keys_info` commands, separate encrypted key
  blob, safe import flow, per-tag list integration (rolling tags unaffected)
- **Vendor BLE protocol (round 2)** — ring (`PL`/`ST`), battery query (`GVBAT`)
  with a standing status line (persisted sample, silent 12 h re-query), and a
  **read-only rekey check** that reconstructs the advertised public key and
  compares it against the imported key — detects out-of-band key overwrites of
  your own tag. No key-writing frames anywhere in the app.
- **Auth hardening** — SMS-first second factor; Apple 401 auto-renews the
  session once before asking for re-login
- **Weekly WorkManager fetch** with persisted last reports and a
  coordinate-free notification
- **Offline basemap** — style template with `file://` glyphs/sprites and a
  `pmtiles://` placeholder; bring your own tile pack, no rebuild needed
- Quiet "not seen by an iPhone yet" state instead of an error banner

## Hard-won device lessons (in the code comments)

`SCAN_MODE_LOW_LATENCY` is required on some OEM stacks; hardware
manufacturer-data filters miss scan-response payloads; the API 33
`writeDescriptor`/`writeCharacteristic` overloads reject right after
discovery (the deprecated Boolean overloads queue reliably); the tag drops
GATT after ~800 ms so the CCCD setup runs off the cached service list.

## License

GPL-2.0-only, same as upstream. The enhanced fork is unrelated to and
unendorsed by Apple or the tag vendor.

---

## 中文说明

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
