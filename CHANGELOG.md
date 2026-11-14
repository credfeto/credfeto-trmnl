# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

<!--
Please ADD ALL Changes to the UNRELEASED SECTION and not a specific release
-->

## [Unreleased]
### Security
### Added
- larapaper-restart.timer, installed and enabled by ./update: restarts larapaper every 6 hours (00:17/06:17/12:17/18:17) because its chromium renderer leaks processes and memory until it thrashes inside the container memory limit (786 PIDs, 1.45 of 1.5 GiB, ~60 cgroup OOM kills an hour, swap full on 2026-11-13)
- LaraPaper TRMNL server stack (docker-compose, watchtower, local TRMNL framework asset mirror, firewalld rules for the Traefik hosts)
- ai/global github-cli instructions: `cfwf issue view --comments` (issue plus comments in one read) and `cfwf comment view --comment-id` (one comment by id)
### Fixed
- larapaper has mem_limit/memswap_limit 1536m and pids_limit 256 (overridable with LARAPAPER_MEM_LIMIT / LARAPAPER_PIDS_LIMIT) so a chromium leak can no longer OOM-thrash the host
- mirror-assets makes the asset mirror world-readable so nginx can serve it under a 027 umask
### Changed
- SDK - Updated DotNet SDK to 11.0.100
### Deprecated
### Removed
### Deployment Changes

<!--
Releases that have at least been deployed to staging, BUT NOT necessarily released to live.  Changes should be moved from [Unreleased] into here as they are merged into the appropriate release branch
-->

## [0.0.0] - Project created