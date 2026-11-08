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

**English** | [简体中文](README.zh-CN.md)
