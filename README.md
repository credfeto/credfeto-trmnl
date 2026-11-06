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
