# Project 12: Linux Security Audit and Targeted Remediation

## Goal

Use Lynis to audit a Linux Mint lab VM, identify a concrete security issue, apply a narrowly scoped package update, and rerun the audit to verify the result.

This project adds a repeatable vulnerability-management cycle to the portfolio: audit, prioritize, preview the change, remediate, and verify.

## Lab and scope

- Operating system: Linux Mint 22.3 (Zena), based on Ubuntu 24.04 (Noble)
- Audit tool: Lynis 3.0.9
- Scope: the Lynis warning PKGS-7392 for security updates available from the configured Ubuntu security repository
- No automated security remediation was enabled

Lynis also produced hardening suggestions. Those are review items, not automatic instructions; this project did not apply them.

## What happened

1. Installed Lynis from the system package manager.
2. Inspected the lynis.timer systemd unit after installation. It was enabled for daily audits, so it was disabled to keep scans manual during the project.
3. The first interactive audit was interrupted when the VM froze while Lynis was enumerating packages. After restarting the VM, the audit was rerun with --cronjob and completed successfully.
4. The completed baseline reported PKGS-7392 and identified five packages with security updates available.
5. Simulated the targeted upgrade plan. APT reported five upgrades, zero new packages, and zero removals.
6. Applied only those five package updates.
7. Reran Lynis in noninteractive mode. The final log said Lynis ended successfully; the report contained no warning[] entries. The hardening index remained 61.

## Package changes

| Package | Before | After |
|---|---|---|
| cifs-utils | 2:7.0-2ubuntu0.5 | 2:7.0-2ubuntu0.6 |
| git | 1:2.43.0-1ubuntu7.3 | 1:2.43.0-1ubuntu7.4 |
| git-man | 1:2.43.0-1ubuntu7.3 | 1:2.43.0-1ubuntu7.4 |
| libxtst6 | 2:1.2.3-1.1build1 | 2:1.2.3-1.1ubuntu0.1 |
| python3-jwt | 2.7.0-1ubuntu0.3 | 2.7.0-1ubuntu0.4 |

The APT preview showed six other packages still upgradable. They were outside this targeted remediation and were left untouched.

## Reproduce

    sudo apt update
    sudo apt-get -s install --only-upgrade cifs-utils git git-man libxtst6 python3-jwt
    sudo apt-get install --only-upgrade cifs-utils git git-man libxtst6 python3-jwt
    sudo lynis audit system --cronjob
    sudo grep -a '^warning' /var/log/lynis-report.dat
    sudo grep -a '^hardening_index' /var/log/lynis-report.dat
    sudo tail -n 3 /var/log/lynis.log

Expected verification for this run:

- No output from the warning query
- hardening_index=61
- The final log line says Lynis ended successfully.

## Notes and limits

- Lynis reported a package-security warning and named the packages. The project record does not claim a CVE-to-package mapping for each update.
- A hardening index of 61 is the result reported by this scan; it is not a guarantee that the host is secure.
- The timer remains disabled after the project so audits stay manual. To restore its daily schedule, run sudo systemctl enable --now lynis.timer.
- Lynis logs and its report file stay on the VM under /var/log; they are not included in this repository package.

## References

- [Lynis installation and audit guide](https://cisofy.com/documentation/lynis/get-started/)
- [Lynis PKGS-7392 control](https://cisofy.com/lynis/controls/PKGS-7392/)
- [Ubuntu Security Notices](https://ubuntu.com/security/notices)

