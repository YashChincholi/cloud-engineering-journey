# Day 02 — Linux Core Concepts 🐧

> **Learning track:** CCNA + Cloud Engineering  
> **Environment:** Ubuntu on a personal laptop  
> **Focus:** Linux hardware inspection, memory, storage, kernel logs, device events, boot process, systemd, file types, and troubleshooting.

## What I practiced

- Inspected the kernel and system architecture.
- Checked USB-related kernel messages and learned why `sudo` may be required for `dmesg`.
- Explored PCI devices, storage devices, CPU details, RAM, swap, and hardware inventory.
- Investigated `systemd` targets and the Linux boot sequence.
- Practiced symbolic-link inspection and learned how to verify configuration changes.
- Reviewed Linux file types and the filesystem hierarchy.

## 1. Essential system-inspection commands

| Command | Purpose |
|---|---|
| `uname` | Shows the kernel name. |
| `uname -r` | Shows the running kernel version. |
| `lscpu` | Displays CPU architecture, cores, threads, and CPU features. |
| `lsmem --summary` | Summarizes memory blocks; availability depends on installed utilities. |
| `free -h` | Shows RAM and swap usage in human-readable units. |
| `lsblk` | Lists block devices, partitions, and mount points. |
| `lspci` | Lists PCI/PCIe devices. |
| `sudo lshw -short` | Shows a concise hardware inventory; some details require elevated privileges. |
| `dmesg` | Reads kernel ring-buffer messages, subject to system permissions. |
| `udevadm monitor` | Watches for kernel and udev device events. |
| `systemctl` | Inspects and manages systemd units and targets. |
| `journalctl -b` | Shows journal entries from the current boot. |

### Example: inspect USB kernel messages

```bash
dmesg | grep -i usb
sudo dmesg | grep -i usb
```

If the first command returns `Operation not permitted`, the system restricts access to the kernel buffer for ordinary users. Use elevated privileges only when needed.

`grep -i usb` filters the output for “usb”, ignoring letter case.

## 2. CPU, memory, and swap

### CPU

```bash
lscpu
```

Important terms:

- **Architecture:** the instruction-set/platform architecture, such as `x86_64`.
- **Core:** a physical processing core.
- **Thread/logical CPU:** an execution unit exposed to the operating system.
- **Virtualization support:** CPU features that can help run virtual machines.

In my recorded output, `lscpu` identified an AMD Ryzen 3 5300U with Radeon Graphics, 4 cores and 8 logical CPUs.

### RAM

```bash
free -h
free -m
lsmem --summary
```

- **Total:** memory Linux accounts as available to it.
- **Used:** memory classified as in use by the command.
- **Free:** currently unused memory.
- **Buff/cache:** memory used for buffers and caches.
- **Available:** an estimate of memory available to applications without heavy swapping.

Linux uses otherwise-unused RAM for caching. Therefore, low `free` memory alone does not necessarily mean the system is running out of memory; check `available` too.

The commands can report different memory totals because installed physical RAM, online memory blocks, and memory available to Linux are not exactly the same measurement.

### Swap

```bash
swapon --show
free -h
```

Swap is storage-backed space that Linux can use for memory pages. It may be configured as a partition or a file, and is much slower than RAM. Heavy swap activity can contribute to poor performance.

## 3. Storage and device inventory

```bash
lsblk
lspci
sudo lshw -short
```

- `lsblk` is useful for understanding disks, partitions, and mount points.
- `lspci` shows devices attached through PCI/PCIe, such as controllers and network adapters.
- `lshw` gives a broader hardware inventory.

Device names such as `nvme0n1` commonly identify an NVMe disk, while `nvme0n1p1` identifies one of its partitions. Filesystems such as `ext4` and `NTFS` describe how data is organized on a volume.

The `MAJ:MIN` values displayed by `lsblk` are Linux device major/minor numbers used to identify devices and their instances.

## 4. Kernel messages and device events

```bash
sudo dmesg | grep -i usb
udevadm monitor
```

`dmesg` helps inspect kernel messages about device discovery and drivers. `udevadm monitor` waits for device events rather than printing a one-time inventory. Connect or disconnect a non-critical USB device to observe events, then stop the monitor with `Ctrl+C`.

**Troubleshooting lesson:** a command that appears to “hang” may be waiting for an event. Check what the command is designed to do before assuming it has failed.

## 5. Linux boot sequence

A simplified boot flow:

```text
UEFI firmware
    ↓
Bootloader (for example, GRUB)
    ↓
Linux kernel
    ↓
Init system (systemd on this Ubuntu system; typically PID 1)
    ↓
System services and targets
    ↓
Login / desktop or server workload
```

This sequence gives a starting point when troubleshooting a machine that will not boot or services that do not start.

Useful checks:

```bash
ps -p 1 -o pid,comm,args
journalctl -b
```

## 6. systemd and startup targets

```bash
systemctl get-default
systemctl list-units --type=target --state=active
systemctl status graphical.target
```

Targets group units and dependencies for system startup. Several targets can be active at once; they are not mutually exclusive modes.

| Target | General purpose |
|---|---|
| `default.target` | The configured default startup target, commonly a link to another target. |
| `graphical.target` | Startup with a graphical login environment. |
| `multi-user.target` | Multi-user services, typically without a graphical login. |
| `basic.target` | Basic system initialization. |
| `rescue.target` | Rescue environment. |
| `emergency.target` | Minimal emergency environment. |
| `poweroff.target` | Shut down. |
| `reboot.target` | Reboot. |

### Symbolic links and target paths

```bash
ls -l /usr/lib/systemd/system/default.target
readlink /usr/lib/systemd/system/default.target
readlink -f /usr/lib/systemd/system/default.target
ls -l /sbin/init
```

A symbolic link points to another path. The exact location of systemd unit files varies by distribution and filesystem layout, so inspect the path on the machine rather than assuming every system is identical.

`ls -ltr` means long listing, sorted by modification time, with the order reversed (oldest first).

### Changing the default target — caution

Inspect the current setting first:

```bash
systemctl get-default
```

On a system where you intentionally want to change the default, the command is:

```bash
sudo systemctl set-default multi-user.target
systemctl get-default
```

To restore the usual graphical startup on a desktop:

```bash
sudo systemctl set-default graphical.target
systemctl get-default
```

**Caution:** Changing the default to `multi-user.target` can make a subsequent boot start without the normal graphical login. Do not change it on your primary laptop unless you understand how to restore the graphical target. Verify the result after the command. Pressing `Ctrl+C` interrupts a foreground command; it does not prove the configuration changed.

Modern Ubuntu primarily uses systemd targets. `who -r` or traditional `runlevel` tools may not provide useful output on every system.

## 7. Linux file types

The first character in a long `ls -l` listing indicates the file type.

| Symbol | Type | Description |
|---|---|---|
| `-` | Regular file | Text, images, binaries, and other ordinary file data. |
| `d` | Directory | Contains directory entries for files and subdirectories. |
| `c` | Character device | Stream-oriented device interface, such as a terminal. |
| `b` | Block device | Block-oriented storage device, such as a disk. |
| `l` | Symbolic link | A path that points to another path. |
| `s` | Socket | Local or network-style inter-process communication endpoint. |
| `p` | Named pipe (FIFO) | A pipe used for communication between processes. |

Example:

```bash
ls -l
```

The file-type character is the first character of the permissions string, for example `drwxr-xr-x` for a directory or `lrwxrwxrwx` for a symbolic link.

### Hard links vs symbolic links

- **Hard link:** another directory entry for the same inode/file data (subject to filesystem restrictions).
- **Symbolic link:** a separate link containing a path to another file or directory.

Inspect a symlink with `ls -l` or `readlink`.

## 8. Filesystem hierarchy: paths to recognize

| Path | Typical purpose |
|---|---|
| `/` | Root of the filesystem hierarchy. |
| `/etc` | System-wide configuration. |
| `/usr/lib/systemd/system` | Often contains systemd unit files supplied by packages; exact layout varies. |
| `/lib/systemd/system` | May be linked to or integrated with `/usr/lib` on merged-`/usr` systems. |
| `/dev` | Device nodes and related interfaces. |
| `/proc` | Process and kernel information exposed through a virtual filesystem. |
| `/sys` | Kernel/device information exposed through sysfs. |
| `/var/log` | Common location for log files, though many systems use the systemd journal. |

A missing file at one expected path does not automatically mean the operating system is broken. Confirm the path, package, link target, and system configuration.

## 9. Troubleshooting checklist

When a Linux command or setting does not behave as expected:

1. **Check whether the command exists:** `command -v <command>`
2. **Identify the OS:** `cat /etc/os-release`
3. **Check permissions:** does this operation legitimately require `sudo`?
4. **Verify the exact path:** `ls -l <path>`
5. **Inspect symbolic links:** `readlink <path>`
6. **Check the component’s status:** `systemctl status <unit>`
7. **Read logs:** `journalctl -b`
8. **Verify after changes:** rerun the relevant status or configuration command.

A missing command may need a package installation; `sudo` alone does not install it. On Debian/Ubuntu, package installation generally uses `sudo apt update` and `sudo apt install <package>`, after confirming the correct package.

## 10. Cloud Engineering connection

These Linux fundamentals are useful when inspecting and troubleshooting cloud VMs:

- CPU, RAM, swap, and storage checks help identify resource pressure.
- Kernel logs and device events provide clues about low-level system issues.
- `systemd` and journal logs help diagnose service startup failures.
- Filesystem and symlink knowledge helps avoid incorrect path assumptions.
- Verifying configuration after changes is a safer operational habit.

## 11. Practice tasks

- [ ] Run `uname -r` and `lscpu`.
- [ ] Inspect RAM and swap with `free -h` and `swapon --show`.
- [ ] Map disks and partitions using `lsblk`.
- [ ] Inspect PCI devices with `lspci`.
- [ ] Read USB kernel messages with `sudo dmesg | grep -i usb`.
- [ ] Run `udevadm monitor` and observe a safe device event.
- [ ] Check the default and active systemd targets.
- [ ] Inspect a symbolic link using `ls -l` and `readlink`.
- [ ] Review the current boot logs with `journalctl -b`.
- [ ] Explain the difference between regular files, directories, device nodes, links, sockets, and FIFOs.

## Evidence in this repository

Screenshots in `images/` document the hands-on commands from this session. The raw and cleaned terminal recordings are stored in `recordings/`. Keep screenshots focused on the relevant command and result, and review recordings for accidental personal information before publishing.

---

**Day 02 takeaway:** Linux troubleshooting is not just running commands. It is learning what each command observes, checking permissions and paths, interpreting output, and verifying changes before moving on.
