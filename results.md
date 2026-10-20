# Project 12 audit evidence

## Baseline

- Completed on: 2026-10-20 (VM local time, UTC+08:00)
- Lynis version: 3.0.9
- Lynis completed successfully after an initial interrupted attempt.
- Warning: PKGS-7392 — vulnerable packages were available from the noble-security repository.
- Packages flagged in /var/log/lynis.log: cifs-utils, git, git-man, libxtst6, and python3-jwt.
- Baseline hardening index: 61

## Change preview

APT simulation reported:

- 5 packages upgraded
- 0 packages newly installed
- 0 packages removed
- 6 other packages left not upgraded

The preview showed the five selected updates coming from Ubuntu:24.04/noble-security.

## Remediation

The five packages were upgraded to the versions recorded in README.md. APT completed setup for all five packages.

## Verification

The follow-up Lynis run ended successfully. The warning[] query returned no entries, and the hardening index remained 61.

## Scope notes

The system scan also emitted hardening suggestions. No suggestion was applied in this project. In particular, the partial first run's ecryptfs-migrate-home suggestion was not executed.

