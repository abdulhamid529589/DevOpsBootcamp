# Linux File System Structure - Complete Guide

## Table of Contents
- [Introduction](#introduction)
- [What is a File System?](#what-is-a-file-system)
- [Types of File Systems](#types-of-file-systems)
- [Checking Your File System](#checking-your-file-system)
- [Understanding Inodes](#understanding-inodes)
- [Root Directory Structure](#root-directory-structure)
- [Best Practices](#best-practices)

---

## Introduction

The Linux file system follows a hierarchical structure starting from the root directory (`/`). Unlike Windows with drive letters (C:, D:), Linux organizes everything under a single root directory. This guide explores the Linux file system structure and explains the purpose of each major directory.

---

## What is a File System?

A **file system** is a structured way to store and manage data on a machine or server, including:
- Files
- Directories
- Related metadata (creation time, size, permissions, etc.)

### Analogy

Imagine 10,000 books scattered on a floor - finding one specific book would be extremely difficult. But if you organize them in a library with proper sections and categories, accessing any book becomes easy. Similarly, a file system organizes data in a structured manner for efficient access and management.

**Key Point:** Linux uses a hierarchical structure, unlike Windows which uses drive letters (C:, D:, E:).

---

## Types of File Systems

Different file systems manage data differently based on performance, scalability, and use case requirements.

### Common Linux File Systems

| File System | Description | Best For |
|-------------|-------------|----------|
| **ext3** | Extended File System 3 | General purpose, stable |
| **ext4** | Extended File System 4 | Most common, balanced performance |
| **XFS** | Extended File System | Large files, high throughput, parallel processing |
| **Btrfs** | B-tree File System | Advanced features, snapshots |

### Distribution Examples

- **CentOS/RHEL:** Commonly uses XFS
- **Ubuntu/Debian:** Typically uses ext4

### File System Comparison

**XFS:**
- Optimized for large files and volumes
- Superior performance and scalability
- Best for high throughput and extensive parallel processing
- Industry standard for enterprise environments (Red Hat-based systems)

**ext4:**
- Performs well across various file sizes
- Good for general-purpose use
- Less efficient with extremely large files compared to XFS

---

## Checking Your File System

### View File System Information

Use any of these commands to check your file system type:

```bash
# Method 1: Using df command
df -Th

# Method 2: Using lsblk
lsblk -f

# Method 3: Using mount
mount | grep "^/dev"
```

### Example Output (df -Th)

```bash
Filesystem     Type      Size  Used Avail Use% Mounted on
/dev/sda1      ext4       18G   5G   12G  30% /
/dev/sda2      xfs        47G  10G   37G  22% /home
tmpfs          tmpfs     2.0G     0  2.0G   0% /dev/shm
```

**Understanding the output:**
- **Filesystem:** Partition identifier
- **Type:** File system type (ext4, xfs, tmpfs, etc.)
- **Size:** Total partition size
- **Used/Avail:** Used and available space
- **Mounted on:** Where the partition is mounted

**Note:** `tmpfs` (temporary file system) is used by the OS for processing and temporary operations.

---

## Understanding Inodes

### What is an Inode?

An **inode** (index node) is a unique number associated with every file and directory in Linux. It's essentially a memory block that points to the actual location on disk where file data is stored.

### Viewing Inode Numbers

```bash
# List files with inode numbers
ls -i

# Detailed listing with inodes
ls -li
```

**Example output:**
```bash
1234567 -rw-r--r-- 1 user group 1024 Dec 19 10:00 file1.txt
7654321 drwxr-xr-x 2 user group 4096 Dec 19 10:00 directory1
```

### How Inodes Work

When you create a file:
1. Data is stored in memory blocks on disk
2. An inode is created with a unique number
3. The inode contains metadata and pointers to data blocks
4. The file system uses inode numbers to track file locations

**Example:**
```bash
# Create a file with data
cat > file1.txt
This is data
^D

# The data "This is data" is stored in a memory block
# The inode contains the pointer to this memory block location
```

**Important:** Inodes store file metadata (size, permissions, timestamps) and pointers to data blocks, NOT the actual file data.

---

## Root Directory Structure

The root directory (`/`) is the starting point of the Linux file system hierarchy. Let's explore each major directory:

### Directory Overview

```bash
# Navigate to root and list contents
cd /
ls
```

---

## 1. `/bin` - Essential User Binaries

**Purpose:** Contains essential command binaries (executable files) needed by all users.

**Contents:** Basic commands like `ls`, `cd`, `cat`, `cp`, `mv`, etc.

### Examples

```bash
# Navigate to /bin
cd /bin
ls

# Search for specific commands
ls | grep cd    # Shows cd-related commands
ls | grep ls    # Shows ls-related commands
```

**View command location:**
```bash
which ls
# Output: /bin/ls
```

**Note:** These are binary executable files. Attempting to read them as text will show unreadable binary data:
```bash
cat /bin/ls
# Output: Binary file (shows garbled text)
```

---

## 2. `/etc` - Configuration Files

**Purpose:** Contains system-wide configuration files for networking, user management, system settings, and installed packages.

**Analogy:** Similar to `Program Files` in Windows C: drive.

### Common Configuration Files

```bash
cd /etc
ls
```

#### User Management

**View all system users:**
```bash
cat /etc/passwd
```

**Example output:**
```bash
root:x:0:0:root:/root:/bin/bash
pal:x:1000:1000:Pal:/home/pal:/bin/bash
alex:x:1001:1001:Alex:/home/alex:/bin/bash
victor:x:1002:1002:Victor:/home/victor:/bin/bash
```

#### Network Configuration

**DNS resolver configuration:**
```bash
cat /etc/resolv.conf
```

**Hostname configuration:**
```bash
cat /etc/hostname
```

**Key Point:** When you need to modify system settings, network configurations, or service configurations, look in `/etc` for the relevant configuration file.

---

## 3. `/home` - User Home Directories

**Purpose:** Contains personal directories for all regular (non-root) users.

### Structure

```bash
cd /home
ls
# Output: alex  pal  victor
```

Each user has their own directory where they can:
- Create files and folders
- Store personal data
- Run scripts
- Manage personal configurations

### User Isolation

```bash
# As user 'pal', try to access another user's directory
cd /home/alex
# Output: Permission denied
```

**Security:** Users cannot access other users' home directories without proper permissions.

---

## 4. `/root` - Root User Home Directory

**Purpose:** Home directory for the root (superuser) account.

**Why separate?** Root is a special administrative user with elevated privileges, so it has its own dedicated home directory outside `/home`.

### Accessing Root Home

```bash
# Regular users cannot access
ls /root
# Output: Permission denied

# Login as root user
sudo su -
pwd
# Output: /root
```

---

## 5. `/var` - Variable Data

**Purpose:** Contains variable data files that change frequently during system operation.

**Critical for:** Logs, caches, temporary files, and application data.

### Important Subdirectories

```bash
cd /var
ls
# Shows: cache  db  log  mail  spool  tmp
```

#### `/var/log` - System Logs

**Most Important:** Log files for troubleshooting and monitoring.

```bash
cd /var/log
ls
```

**Common log files:**

| Log File | Purpose |
|----------|---------|
| `boot.log` | Boot process logs |
| `messages` | General system logs (most common) |
| `secure` | Authentication and security logs |
| `maillog` | Mail server logs |
| `httpd/` | Web server logs (if Apache is installed) |
| `samba/` | Samba server logs |

**Example - Reading system logs:**
```bash
# View general system messages
cat /var/log/messages

# View boot logs
cat /var/log/boot.log

# View security/authentication logs
cat /var/log/secure
```

**Use Case:** When troubleshooting issues, always check relevant log files in `/var/log`.

---

## 6. `/boot` - Boot Files

**Purpose:** Contains files necessary for system boot process.

**Contents:**
- Kernel images
- Bootloader configuration
- Initial RAM disk images

```bash
cd /boot
ls
```

**Note:** Critical system directory - modifying files here incorrectly can prevent system boot.

---

## 7. `/dev` - Device Files

**Purpose:** Contains device files representing hardware devices attached to the system.

**Examples:** Hard drives, USB devices, terminals, etc.

### Viewing Devices

```bash
cd /dev
ls

# View disk partitions
df -h
# Shows: /dev/sda1, /dev/sda2, etc.
```

**Understanding device names:**
- `sda`, `sdb`: Hard drives (SCSI/SATA)
- `sda1`, `sda2`: Partitions on drive sda
- `tty`: Terminal devices

---

## 8. `/media` - Removable Media

**Purpose:** Mount point for removable media devices.

**Examples:** USB drives, CD-ROMs, external hard drives

```bash
cd /media
ls
# Shows mounted removable devices (empty if none attached)
```

**Behavior:** When you plug in a USB drive, it automatically mounts under `/media/[device-name]`.

---

## 9. `/mnt` - Temporary Mount Point

**Purpose:** Temporary mount point for manually mounting file systems.

**Difference from `/media`:**
- `/media` → Automatic mounting (removable devices)
- `/mnt` → Manual mounting (temporary tasks)

### Use Case

```bash
# Temporarily mount an external hard drive for backup
sudo mount /dev/sdb1 /mnt
# Perform backup operations
# Unmount when done
sudo umount /mnt
```

---

## 10. `/opt` - Optional Software

**Purpose:** Directory for third-party or optional software packages.

**Use Case:** When you install applications that are not part of your distribution's default packages.

```bash
cd /opt
ls
# Shows third-party applications
```

---

## 11. `/proc` - Process Information

**Type:** Virtual and dynamic file system

**Purpose:** Provides an interface to kernel data structures and running processes.

### Key Features

**Not actual files:** These are virtual files generated on-the-fly by the kernel.

```bash
cd /proc
ls
# Shows: numbered directories (PIDs) and system information files
```

### Useful Examples

#### CPU Information

```bash
cat /proc/cpuinfo
```

**Output includes:**
- Processor model
- CPU cores
- CPU frequency
- Cache size

#### Memory Information

```bash
cat /proc/meminfo
```

**Output includes:**
- Total memory
- Free memory
- Available memory
- Swap usage

#### Process Information

```bash
# List all process directories (PIDs)
ls /proc | grep "^[0-9]"

# View information about a specific process
cat /proc/1234/status  # Replace 1234 with actual PID
```

---

## 12. `/run` - Runtime Data

**Purpose:** Stores transient runtime data.

**Contents:**
- Process IDs (PIDs)
- Lock files
- State information for running processes

```bash
cd /run
ls
```

**Cleared on boot:** Contents are temporary and do not persist across reboots.

---

## 13. `/sbin` - System Binaries

**Purpose:** Contains essential system administration commands.

**Difference from `/bin`:**
- `/bin` → Commands for all users
- `/sbin` → Commands primarily for system administrators

### Examples

```bash
cd /sbin
ls | grep fdisk  # Disk partitioning tool (admin only)
```

**Key commands in `/sbin`:**
- `fdisk` - Partition management
- `mkfs` - Create file systems
- `shutdown` - System shutdown
- `reboot` - System reboot
- `iptables` - Firewall configuration

**Permission check:**
```bash
# fdisk exists in /sbin (admin tool)
ls /sbin | grep fdisk
# Output: fdisk

# Not in /bin (regular user commands)
ls /bin | grep fdisk
# Output: (empty)
```

---

## 14. `/srv` - Service Data

**Purpose:** Contains data served by system services.

**Use Cases:**
- FTP server data
- Web server files
- Data shared over network services

```bash
cd /srv
ls
```

**Example:**
If running a web server, you might store website files in `/srv/www` or `/srv/http`.

---

## 15. `/sys` - System Information

**Type:** Virtual file system (similar to `/proc`)

**Purpose:** Provides interface to kernel and system devices.

**Contains:** Information about devices, drivers, and kernel features.

### Examples

```bash
cd /sys

# View block device information
cat /sys/class/block/sda/size
# Output: Shows disk size in blocks

# View specific partition size
cat /sys/class/block/sda1/size
# Output: Partition size
```

**Use Case:** Useful for getting detailed, specific hardware and device information.

---

## 16. `/tmp` - Temporary Files

**Purpose:** Stores temporary files created by applications and users.

**Characteristics:**
- World-writable (any user can create files)
- Cleared on reboot (on most systems)
- Used for short-term storage

```bash
cd /tmp
ls
```

**Common use:** Applications store temporary data here during operation.

---

## 17. `/usr` - User System Resources

**Full Form:** Unix System Resources (NOT "user")

**Purpose:** Secondary hierarchy for user-level programs and data.

### Important Subdirectories

```bash
cd /usr
ls
# Shows: bin  lib  lib64  sbin  include  local  share
```

| Directory | Purpose |
|-----------|---------|
| `/usr/bin` | User command binaries |
| `/usr/sbin` | System administration binaries |
| `/usr/lib` | Shared libraries |
| `/usr/lib64` | 64-bit shared libraries |
| `/usr/include` | C/C++ header files |
| `/usr/local` | Locally installed software |
| `/usr/share` | Architecture-independent data |

### Why `/usr` is Important

**Separation of concerns:**
- System operates from base directories (`/bin`, `/lib`, `/sbin`)
- User-level operations happen in `/usr` hierarchy
- Changes at user level don't directly impact core system

**Benefits:**
1. System stability - core system files remain protected
2. Easy organization - user programs separated from system programs
3. Network mounting - `/usr` can be mounted from network share

### Example: Header Files

```bash
cd /usr/include
ls
# Shows: stdio.h, stdlib.h, math.h, etc.
# C/C++ header files for development
```

---

## File System Hierarchy Visualization

```
/                           (Root Directory)
├── bin                     (Essential user commands)
├── boot                    (Boot loader files)
├── dev                     (Device files)
├── etc                     (Configuration files)
├── home                    (User home directories)
│   ├── user1
│   ├── user2
│   └── user3
├── lib, lib64              (Shared libraries)
├── media                   (Removable media mount points)
├── mnt                     (Temporary mount points)
├── opt                     (Optional/third-party software)
├── proc                    (Process information - virtual)
├── root                    (Root user home directory)
├── run                     (Runtime data)
├── sbin                    (System administration commands)
├── srv                     (Service data)
├── sys                     (System information - virtual)
├── tmp                     (Temporary files)
├── usr                     (User programs and data)
│   ├── bin
│   ├── lib
│   ├── sbin
│   ├── include
│   ├── local
│   └── share
└── var                     (Variable data)
    ├── cache
    ├── log
    ├── mail
    └── tmp
```

---

## Best Practices

### 1. Know Where Things Belong

- **User data** → `/home/username`
- **System configs** → `/etc`
- **Logs** → `/var/log`
- **Temporary files** → `/tmp`
- **Third-party apps** → `/opt`

### 2. Troubleshooting

Always check logs first:
```bash
# General system issues
tail -f /var/log/messages

# Authentication problems
tail -f /var/log/secure

# Boot issues
cat /var/log/boot.log
```

### 3. System Safety

- **Never modify** `/proc` or `/sys` files directly
- **Be careful** with `/boot` - incorrect changes prevent booting
- **Use `/mnt` or `/media`** for temporary mounts, not random directories

### 4. Understanding File Locations

```bash
# Find where a command is located
which command_name

# Find all locations of a file
locate filename

# Search for files
find / -name "filename"
```

---

## Quick Reference

### Essential Directories

| Directory | Purpose | User Access |
|-----------|---------|-------------|
| `/bin` | Essential commands | All users |
| `/etc` | Configuration files | Read: All, Write: Root |
| `/home` | User directories | Owner only |
| `/root` | Root home | Root only |
| `/var/log` | System logs | Read: Root (mostly) |
| `/tmp` | Temporary files | All users |
| `/usr` | User programs | All users |

### Common Tasks

```bash
# Check disk usage
df -h

# Check file system type
df -Th

# View system logs
tail -f /var/log/messages

# Check CPU info
cat /proc/cpuinfo

# Check memory info
cat /proc/meminfo

# List users
cat /etc/passwd

# Navigate to root directory
cd /

# Go to home directory
cd ~
# or
cd
```

---

## Conclusion

Understanding the Linux file system structure is fundamental for effective system administration and troubleshooting. Each directory has a specific purpose:

- **System operation** → `/bin`, `/sbin`, `/lib`, `/boot`
- **Configuration** → `/etc`
- **User data** → `/home`, `/root`
- **Variable data** → `/var` (especially logs)
- **Device files** → `/dev`
- **Virtual/dynamic** → `/proc`, `/sys`
- **Temporary** → `/tmp`, `/run`
- **User programs** → `/usr`

By knowing what each directory contains and its purpose, you can:
- Navigate the system efficiently
- Troubleshoot issues quickly
- Perform administrative tasks correctly
- Organize your files appropriately

**Remember:** The Linux file system is hierarchical, logical, and designed for efficiency. Take time to explore and understand it - this knowledge will serve you throughout your Linux journey! 🐧