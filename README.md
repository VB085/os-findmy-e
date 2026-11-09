**English** | [简体中文](README.zh-CN.md)

# os-findmy-e — enhanced edition

Android client for Apple Find My–protocol tags, based on
[sacca97/os-findmy-e](https://github.com/sacca97/os-findmy-e) (GPL-2.0).
Core changes are proposed upstream in [PR #2](https://github.com/sacca97/os-findmy-e/pull/2).

Supports imported static P-224 tags alongside rolling-key accessories. Selected
flows have been checked on an Android 14 arm64 device with a DEONE MTAG; the
remaining validation limits are listed below.

## Current features

- **Static report queries:** static keys use `acsnservice/fetch`; rolling keys
  keep the v2 path. A same-key comparison found newer authenticated reports on
  the static endpoint while v2 kept returning older reports. Authentication and
  server errors no longer become an empty history.
- **Battery bars:** nearby `GVBAT` readings use the vendor's coarse four-bar
  status. The UI shows the sample age and marks readings older than 12 hours as
  outdated. Guess-based voltages and exact percentages are not displayed.
- **Saved trajectories:** merge and deduplicate sightings across refreshes;
  encrypt the full archive with Android Keystore; restore it after restart or a
  failed network query. Retain up to 90 days / 10,000 points per accessory, with
  the last known position retained. Map windows range from 6 hours to 90 days.
- **Background collection:** one WorkManager job attempts a query every 30
  minutes with a 5-minute flex window while connected. Power saving can defer
  it. Background collection does not connect to BLE or rotate keys.
- **Nearby tools:** ring, battery reading, read-only advertised-key verification
  and explicitly confirmed key management. Normal rotation checks an imported
  key; recovery mode requires the operator to identify their own hardware.
- **Account and map support:** SMS-first two-factor authentication, a single
  session-renewal attempt after HTTP 401, Chinese UI resources, coordinate-free
  location notifications and an optional offline basemap using a user-supplied
  PMTiles pack.

See [static-tag behaviour and storage](docs/static-tags.md) for protocol details,
privacy properties and verification scope. See [Android build instructions](app/README.md)
and [native build instructions](native/README.md) to build from source.

## Verification and limits

The current validation includes 235 Android JVM tests, 11 Rust report tests and
3 Python protocol tests. Device checks covered static report retrieval, a new
nearby battery reading, encrypted history persistence, restart restoration and
registration of the 30-minute background job.

Physical low-battery behaviour, moving/lost-item tracking over long periods,
unattended session renewal and multiple real background periods still need
validation. Refreshes read sightings supplied by the Find My network; they do
not generate a live position or reconstruct missing route segments. Nearby
rekeying is not a remote lost-tag recovery mechanism or a frequent-refresh
requirement.

## Privacy and source publication

The repository contains source and synthetic test fixtures. Real accounts,
session credentials, tag keys, coordinates, captures, screenshots, local device
state, signing material, APKs and Apple's CoreADI image are not published.
BLE diagnostics use counts and status without logging device addresses or
advertising-key identifiers. The notification worker's latest-point snapshot
remains app-private JSON; the full trajectory archive is encrypted.

## License

GPL-2.0-only, same as upstream. This fork is unrelated to and unendorsed by Apple
or the tag vendor.
