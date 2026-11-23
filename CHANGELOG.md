# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

<!--
Please ADD ALL Changes to the UNRELEASED SECTION and not a specific release
-->

## [Unreleased]
### Security
- Host egress lockdown (`firewall/configure`, run by `update`): firewalld policies limit what the host (including LaraPaper on the host network) and the Watchtower container may connect to, with CDN-fronted destinations kept in ipsets refreshed every 10 minutes. `trmnl-firewall-sync.timer` re-applies it when the firewall config changes, and the daily `Repo: Update egress ranges` workflow refreshes the GitHub and Cloudflare ranges. Observation mode (log unmatched traffic, drop nothing) until `TRMNL_FIREWALL_ENFORCE=true`
### Added
### Fixed
- `./update` now re-runs itself after `git pull`, so changes to the update script take effect on the same run instead of the next one
- Disabled core dumps for larapaper (`ulimits: core: 0`) so chromium crashes no longer fill the root filesystem with core files
### Changed
### Deprecated
### Removed
### Deployment Changes

<!--
Releases that have at least been deployed to staging, BUT NOT necessarily released to live.  Changes should be moved from [Unreleased] into here as they are merged into the appropriate release branch
-->

## [0.0.1] - 2026-11-15
### Added
- larapaper-restart.timer, installed and enabled by ./update: restarts larapaper every 6 hours (00:17/06:17/12:17/18:17) because its chromium renderer leaks processes and memory until it thrashes inside the container memory limit (786 PIDs, 1.45 of 1.5 GiB, ~60 cgroup OOM kills an hour, swap full on 2026-11-13)
- LaraPaper TRMNL server stack (docker-compose, watchtower, local TRMNL framework asset mirror, firewalld rules for the Traefik hosts)
- ai/global github-cli instructions: `cfwf issue view --comments` (issue plus comments in one read) and `cfwf comment view --comment-id` (one comment by id)
### Fixed
- larapaper has mem_limit/memswap_limit 1536m and pids_limit 256 (overridable with LARAPAPER_MEM_LIMIT / LARAPAPER_PIDS_LIMIT) so a chromium leak can no longer OOM-thrash the host
- mirror-assets makes the asset mirror world-readable so nginx can serve it under a 027 umask
### Changed
- SDK - Updated DotNet SDK to 11.0.100

## [0.0.0] - Project created