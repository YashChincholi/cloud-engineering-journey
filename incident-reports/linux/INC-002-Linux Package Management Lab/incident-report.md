# Incident Report — INC-002: Linux Package Management Lab

| Field | Details |
| --- | --- |
| Incident ID | `INC-002` |
| Date | 21 October 2026 |
| Environment | Ubuntu 26.04.1 LTS, local learning machine |
| Severity | Low — lab-only issue; no production impact |
| Status | Resolved |
| Category | Linux permissions and package-command usage |

## Summary

During a Linux package-management exercise, two command errors occurred: creating a directory directly under `/` was denied, and a DPKG status query was given an executable path instead of a package name. Both were resolved by using the appropriate location and command.

## Symptoms

1. `mkdir -p /package-lab` returned `Permission denied`.
2. `dpkg -s /usr/bin/tree` returned an invalid package-name error.
3. The correct package/file relationship was later confirmed with `dpkg -S /usr/bin/tree`, which returned `tree: /usr/bin/tree`.

## Root cause

- **Filesystem permissions:** `/package-lab` is located directly under the filesystem root, where a normal user typically cannot create a directory.
- **Wrong DPKG query for the input:** `dpkg -s` expects a package name; `/usr/bin/tree` is a file path.

## Troubleshooting and resolution

### 1. Use a user-owned working directory

```bash
# Unsuccessful: attempts to create a directory at filesystem root
mkdir -p /package-lab

# Resolved: create the directory in the current project directory
mkdir -p package-lab
```

The package lab was kept under `linux/day-03_package_management/package-lab/`.

### 2. Match the DPKG command to the question

```bash
# Check the package by its package name
dpkg -s tree

# Find which package owns an executable path
dpkg -S /usr/bin/tree

# List files recorded for an installed package
dpkg -L tree
```

The `dpkg -S` result confirmed that the `tree` package owned `/usr/bin/tree`. Later, the package was removed with `sudo apt remove tree`, and `dpkg -s tree` correctly reported that it was no longer installed.

## Verification

- Created the lab directory inside the project without elevated privileges.
- Downloaded and inspected `tree_2.3.1-1_amd64.deb` using `dpkg-deb -I` and `dpkg-deb -c`.
- Confirmed file ownership with `dpkg -S /usr/bin/tree` while `tree` was installed.
- Removed the package and verified its final status with `dpkg -s tree`; `which tree` no longer printed a path.

## Impact

No production system or service was affected. The errors were limited to a local learning lab and did not indicate a broken package database.

## Prevention / lessons learned

- Work in a project directory the current user owns; do not reach for `sudo` before checking whether the path is appropriate.
- Use `dpkg -s <package>` for package status and `dpkg -S <path>` for file ownership.
- Verify package state after install/removal rather than assuming a command succeeded.
- Keep downloaded `.deb` archives out of Git unless the repository has a specific reason to distribute them.

## Status

**Resolved.** The package-management exercises were completed and the command syntax and directory-permission causes were understood.
