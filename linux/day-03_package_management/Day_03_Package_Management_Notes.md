# Day 03 — Linux Package Management

**Learning path:** Linux / Cloud Engineering Journey  
**Lab date:** 21 October 2026  
**Environment:** Ubuntu 26.04.1 LTS (Resolute Raccoon), `amd64`

## Objectives

- Understand Linux packages, repositories, APT, and DPKG.
- Refresh package metadata and search for software.
- Install, inspect, and remove the `tree` utility.
- Download and inspect a local `.deb` archive.
- Troubleshoot a package-query syntax error and a filesystem permission error.

## 1. Linux packages and package managers

A **package** is an archive containing application files and metadata. A **package manager** helps install, upgrade, remove, search for, and track software while managing dependencies.

| Distribution family | Common package formats/tools | Examples |
| --- | --- | --- |
| Debian-based | `.deb`, `apt`, `dpkg` | Ubuntu, Debian, Linux Mint |
| RPM-based | `.rpm`, `dnf`, `rpm` | Fedora, RHEL, CentOS Stream |

This lab used Ubuntu, so the main tools were APT and DPKG.

## 2. Check the operating system and package tools

```bash
cat /etc/os-release
apt --version
dpkg --version
```

Lab observations:

- OS: Ubuntu 26.04.1 LTS, codename `resolute`.
- APT: `3.2.0 (amd64)`.
- DPKG: `1.23.7 (amd64)`.

## 3. APT vs. DPKG

### APT — higher-level package management

APT works with configured repositories, refreshes package indexes, resolves dependencies, and coordinates package installation and removal.

```bash
sudo apt update
apt search '^tree$'
sudo apt install tree
sudo apt upgrade
sudo apt remove tree
```

- `apt update` refreshes package information; it does **not** upgrade installed packages.
- `apt upgrade` installs available upgrades for installed packages.
- Search results depend on the package indexes currently available on the machine.

### DPKG — Debian package database and package operations

DPKG provides lower-level operations for Debian packages. It does not automatically download and resolve missing dependencies in the same way APT does.

```bash
dpkg -s tree              # Show package status/details
dpkg -L tree              # List files installed by the package
dpkg -S /usr/bin/tree     # Find the package that owns a file
```

**Remember:** APT manages repositories and the higher-level workflow; DPKG handles lower-level Debian package operations and package database queries.

## 4. Repositories and refreshing package information

A repository supplies packages and package metadata. The lab inspected Ubuntu sources using:

```bash
cat /etc/apt/sources.list.d/ubuntu.sources
sudo apt update
```

The Ubuntu configuration included the `resolute`, `resolute-updates`, `resolute-backports`, and `resolute-security` suites. The update output also showed a configured Microsoft Visual Studio Code repository. APT reported **28 packages not upgraded / available for upgrade information** in the lab output; check the current list with:

```bash
apt list --upgradable
```

Do not confuse refreshing the package index with applying upgrades.

## 5. Install and inspect `tree`

`tree` prints directories and files in a tree-like hierarchy. It was used to inspect the project directory.

```bash
# Check package status (the package was initially not installed)
dpkg -s tree

# Search the package index
apt search '^tree$'

# Install from configured repositories
sudo apt install tree

# Verify status and locate the executable
dpkg -s tree
which tree

# Inspect a directory
tree -L 2 ~/projects/cloud-engineering-journey/
```

On a successful installation, `dpkg -s tree` includes a status similar to:

```text
Status: install ok installed
```

The executable was located at `/usr/bin/tree`. `-L 2` limits the displayed directory depth to two levels.

> Note: some lines in the raw terminal transcript around the first install/update sequence appear interleaved. For a reliable package state, use a fresh verification command such as `dpkg -s tree` rather than relying on a confusing section of terminal output.

## 6. Inspect installed package files

```bash
dpkg -L tree
dpkg -S /usr/bin/tree
which tree
man tree
```

Example paths from the lab included:

```text
/usr/bin/tree
/usr/share/doc/tree
/usr/share/man/man1/tree.1.gz
```

| Command | Starts with | Purpose |
| --- | --- | --- |
| `dpkg -L tree` | Package name | Lists files recorded for an installed package |
| `dpkg -S /usr/bin/tree` | File path | Identifies the package that owns a path |
| `which tree` | Executable name | Prints the executable found through `PATH` |
| `man tree` | Command name | Opens the command's manual page |

## 7. Download and inspect a local `.deb` package

A separate folder was created inside the project, which keeps practice files out of system directories.

```bash
mkdir -p ~/projects/cloud-engineering-journey/linux/day-03_package_management/package-lab
cd ~/projects/cloud-engineering-journey/linux/day-03_package_management/package-lab

# Download the archive without installing it
apt download tree
ls -lh

# Inspect package metadata and archive contents
dpkg-deb -I tree_*.deb
dpkg-deb -c tree_*.deb

# Ask APT to install the local package path (and handle dependencies)
sudo apt install ./tree_*.deb

# Verify the installed package
dpkg -s tree
```

Lab archive details:

- Filename: `tree_2.3.1-1_amd64.deb`
- Package version: `2.3.1-1`
- Architecture: `amd64`
- Declared dependency: `libc6 (>= 2.38)`
- Reported download size: approximately 53.5 kB

Useful distinction:

- `dpkg-deb -I package.deb` displays control metadata.
- `dpkg-deb -c package.deb` lists files inside the archive without installing it.
- `dpkg -L package-name` lists files recorded for an installed package.

In this lab, APT's output for the local-file install showed it selected the `tree` package and fetched the same version from the configured repository. The important syntax for a local package path is the `./` prefix.

## 8. Remove and verify

```bash
sudo apt remove tree
dpkg -s tree
which tree
```

After removal, `dpkg -s tree` reported that the package was not installed and `which tree` produced no path. This completed the install → inspect → remove → verify lifecycle.

- `sudo apt remove tree` removes the package but generally leaves package-managed configuration files, where applicable.
- `sudo apt purge tree` also removes package-managed configuration files.
- Neither command guarantees removal of files created separately by users or applications.

## 9. Troubleshooting notes from this lab

### Issue A — Permission denied creating a directory at `/`

Command and error:

```bash
mkdir -p /package-lab
# mkdir: Permission denied
```

**Why it happened:** `/package-lab` is directly under the filesystem root (`/`). A normal user generally cannot create entries there.

**Resolution:** create the lab folder in a directory you own:

```bash
mkdir -p package-lab
```

Prefer a project-local directory for practice files. Do not use `sudo` automatically just to bypass a path or permissions error.

### Issue B — Used a file path with `dpkg -s`

Incorrect query:

```bash
dpkg -s /usr/bin/tree
```

DPKG returned an invalid package-name error because `-s` expects a **package name**, not an executable path.

Use the command that matches the question:

```bash
dpkg -s tree              # Is package tree installed? Show package status
dpkg -S /usr/bin/tree     # Which package owns this path?
dpkg -L tree              # Which files belong to the installed package?
```

This was a command-usage error, not evidence that the package database itself was broken.

## 10. Quick command reference

| Task | Command |
| --- | --- |
| Identify OS | `cat /etc/os-release` |
| Check APT / DPKG versions | `apt --version` / `dpkg --version` |
| Refresh package indexes | `sudo apt update` |
| List available upgrades | `apt list --upgradable` |
| Search for a package | `apt search '^tree$'` |
| Install a package | `sudo apt install tree` |
| Upgrade installed packages | `sudo apt upgrade` |
| Upgrade one package | `sudo apt install --only-upgrade tree` |
| Remove / purge a package | `sudo apt remove tree` / `sudo apt purge tree` |
| Show package status | `dpkg -s tree` |
| List installed package files | `dpkg -L tree` |
| Identify file owner | `dpkg -S /usr/bin/tree` |
| Locate executable in `PATH` | `which tree` |
| Download a package archive | `apt download tree` |
| Inspect `.deb` metadata | `dpkg-deb -I tree_*.deb` |
| List `.deb` archive contents | `dpkg-deb -c tree_*.deb` |
| Install a local `.deb` with APT | `sudo apt install ./package.deb` |
| Install a `.deb` with DPKG | `sudo dpkg -i package.deb` |
| Attempt to repair dependency issues | `sudo apt --fix-broken install` |

Replace `tree` with the package you are actually managing. Review the proposed package changes before confirming an install, removal, or upgrade.

## 11. Interview practice

**What is the difference between APT and DPKG?**  
APT is the higher-level tool for repositories and dependency-aware package workflows. DPKG manages Debian package operations and queries the local package database.

**What is the difference between `apt update` and `apt upgrade`?**  
`apt update` refreshes available package metadata; `apt upgrade` applies eligible upgrades to installed packages.

**How do you find which package owns `/usr/bin/tree`?**  
Use `dpkg -S /usr/bin/tree`.

**How can you inspect a `.deb` before installing it?**  
Use `dpkg-deb -I package.deb` for metadata and `dpkg-deb -c package.deb` for the archive's file list.

**How do you install a local `.deb` and allow APT to resolve dependencies?**  
Use `sudo apt install ./package.deb` from the directory containing the archive, or supply its correct path.

## 12. Evidence captured

The lab screenshots are stored in this folder:

- `images/01_os_and_apt_system_info.png` — OS, APT/DPKG versions, repositories, update.
- `images/02_apt_install_and_tree_demo.png` — search, package install, executable and directory tree.
- `images/03_tree_removal_and_permission_test.png` — package inspection/removal and root-directory permission test.
- `images/04_deb_download_inspect_and_apt_install.png` — download and inspect the `.deb` archive.
- `images/05_dpkg_query_and_file_mapping.png` — package query syntax and file ownership.
- `images/06_dpkg_list_and_final_removal.png` — installed files and final removal verification.

## Key takeaway

**APT manages repository access and the dependency-aware workflow; DPKG provides lower-level Debian package operations and package database queries.** I also learned to match each diagnostic command to the type of input it expects and to create lab files in directories I have permission to use.
