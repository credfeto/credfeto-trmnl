# credfeto-trmnl

Self-hosted TRMNL server ([LaraPaper](https://github.com/usetrmnl/larapaper)) for a
TRMNL e-ink display, with no dependency on TRMNL's cloud. A local nginx serves a
mirror of the TRMNL framework CSS/JS/fonts so screens render without trmnl.com.

## Install

```sh
cp example.env .env   # set APP_KEY (see the comment)
sudo ./update
```

Open `APP_URL`, create the admin account, then set `REGISTRATION_ENABLED=0` and re-run `./update`.

## Device

1. Hold the back button about 5 seconds; join the `TRMNL` WiFi.
2. Advanced settings: server URL = `APP_URL` exactly (`https://trmnl.markridgwell.com`, no trailing slash).
3. Pick your WiFi and connect; approve/auto-join the device in LaraPaper.
4. On a TRMNL OG, turn off automatic firmware updates afterwards.

## Notes

- Published through the Traefik pair as `trmnl.markridgwell.com` and `trmnl-assets.markridgwell.com` (internal only).
- Host networking: firewalld rich rules (added by `./update`) let only the Traefik hosts reach the ports.
- `./mirror-assets [version]` refreshes the asset mirror; `TRMNL_FRAMEWORK_VERSION` must match.

## Host firewall

Ingress is the firewalld rich rules above: larapaper and trmnl-assets use host networking, so their ports are ordinary host ports that firewalld filters (only `ALLOWED_SOURCES`, the Traefik hosts). The device reaches LaraPaper through `trmnl.markridgwell.com`, never directly.

`update` also runs `firewall/configure`, which locks egress down with firewalld policies, the same way as the DNS servers:

| Policy              | Traffic                     | Allowed                                                                                                                                                                       |
|---------------------|-----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `trm-egress`        | host -> anywhere            | DNS to the fleet's DNS servers, NTP to the router, HTTPS to the proxies (Home Assistant, the asset mirror), journald uploads to monitoring, GitHub, ghcr.io, Docker Hub, Cloudflare, trmnl.com (`./mirror-assets`, LaraPaper's firmware and device model checks), the firmware images on S3, chaotic-aur, pkgstats, the Arch WKD, ICMP |
| `trm-egress-docker` | containers -> outside world | DNS, HTTPS to the proxies, ghcr.io, Docker Hub and Cloudflare (Watchtower)                                                                                                    |

Because LaraPaper runs on the host network, its plugin fetches are host egress: a polling plugin whose URL is not behind the proxies needs a rule in `firewall/configure`.

Two timers keep it current:

* `trmnl-egress-refresh.timer` (every 10 minutes) refreshes the firewalld ipsets for destinations without a stable address: names resolved from DNS, plus GitHub's and Cloudflare's published ranges (`firewall/github-egress-ranges` and `firewall/cloudflare-egress-ranges`).
* `trmnl-firewall-sync.timer` (every 15 minutes) pulls the checkout and re-runs `firewall/configure` when anything under `firewall/` changed since the commit it last applied. Container changes still wait for `update`.

The `Repo: Update egress ranges` workflow regenerates the two range files daily and commits any change to main, refusing a list that has shrunk by more than a fifth.

`TRMNL_FIREWALL_ENFORCE` in `.env` is the only switch. Unset or anything other than `true` is observation mode: every rule is in place, but traffic that matches none of them still flows and is logged with the prefix `trm-egress-unmatched:` or `trm-egress-docker-unmatched:` (rate-limited to 30 a minute). Run in observation mode for at least a week (the device model check is weekly) and check the journal for anything legitimate that is not covered:

```sh
sudo journalctl -k --since -7d | grep -E 'trm-egress(-docker)?-unmatched'
```

then set `TRMNL_FIREWALL_ENFORCE=true` and run `./update`.

## Build Status

| Branch  | Status                                                |
|---------|-------------------------------------------------------|
| main    | [![Build: Pre-Release][pre-release-img]][pre-release] |
| release | [![Build: Release][release-img]][release]             |

## Changelog

View [changelog][changelog].

## Contributing

See [contributing guidelines][contributing].

## Security

See [security policy][security].

## Licence

This project is licensed under the [MIT Licence][licence].

[changelog]: CHANGELOG.md
[contributing]: CONTRIBUTING.md
[licence]: LICENSE
[pre-release-img]: https://github.com/credfeto/cs-template/actions/workflows/build-and-publish-pre-release.yml/badge.svg
[pre-release]: https://github.com/credfeto/cs-template/actions/workflows/build-and-publish-pre-release.yml
[release-img]: https://github.com/credfeto/cs-template/actions/workflows/build-and-publish-release.yml/badge.svg
[release]: https://github.com/credfeto/cs-template/actions/workflows/build-and-publish-release.yml
[security]: SECURITY.md
