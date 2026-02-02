# Linux File Permissions - Complete Guide

## Introduction

This comprehensive guide covers Linux file permissions, helping you understand and manage access control for files and directories. By the end of this tutorial, you'll learn:
- What file permissions are and how they work
- How to troubleshoot permission errors
- How to modify file permissions
- How to manage file ownership

---

## Common Permission Problems

Before diving into solutions, let's identify common permission-related issues:

### 1. Cannot Read a File
```bash
cat nfile.txt
# Error: Permission denied
```

### 2. Cannot Enter a Directory
```bash
cd ndir
# Error: Permission denied
```

### 3. Cannot Create Files in a Directory
```bash
cd rdir
touch myfile
# Error: Permission denied
```

### 4. Cannot Execute a Script
```bash
./script.sh
# Error: Permission denied
```

---

## Understanding File Permissions

### Checking File Permissions

To view file permissions, use the `ls` command with options:

```bash
ls -l
# or
ls -lh
# or
ls -la
```

### Permission Structure

When you run `ls -l`, you'll see output like this:

```
-rwxr-xr-- 1 user group 4096 Jan 28 file.txt
drwxr-xr-x 2 user group 4096 Jan 28 directory
```

**Breaking down the permission string:**

```
- rwx r-x r--
│ │   │   │
│ │   │   └── Others permissions
│ │   └────── Group permissions
│ └────────── Owner (User) permissions
└──────────── File type
```

### Permission Characters

| Character | Meaning | Description |
|-----------|---------|-------------|
| `r` | Read | File can be read |
| `w` | Write | File can be modified |
| `x` | Execute | File can be executed (for scripts/programs) |
| `-` | No permission | Permission not granted |

### File Type Indicators

| Character | Type |
|-----------|------|
| `-` | Regular file |
| `d` | Directory |
| `l` | Symbolic link |

### Three Permission Groups

1. **User (Owner)**: The person who created/owns the file
2. **Group**: Users belonging to the file's group
3. **Others**: All other users on the system

**Example:**
```
-rwxr-xr--
 ^^^       → User: read, write, execute
    ^^^    → Group: read, execute
       ^^^ → Others: read only
```

### Owner and Group Information

In the `ls -l` output:
```
-rwxr-xr-- 1 root root 4096 Jan 28 file.txt
              │    │
              │    └── Group owner
              └────── User owner
```

---

## Troubleshooting Permission Issues

### Example 1: Cannot Read File

```bash
cat nfile.txt
# Permission denied
```

**Check permissions:**
```bash
ls -l nfile.txt
-rw------- 1 root root 100 Jan 28 nfile.txt
```

**Problem:** File is owned by `root`, and other users have no read permission.

### Example 2: Cannot Edit File

```bash
vi rfile.txt
# Read-only file
```

**Check permissions:**
```bash
ls -l rfile.txt
-r--r--r-- 1 root root 100 Jan 28 rfile.txt
```

**Problem:** Others only have read (`r`) permission, no write (`w`) permission.

### Example 3: Cannot Enter Directory

```bash
cd ndir
# Permission denied
```

**Check permissions:**
```bash
ls -ld ndir
dr-------- 2 root root 4096 Jan 28 ndir
```

**Problem:** No execute (`x`) permission for others. Execute permission is required to enter a directory.

### Example 4: Can Enter but Cannot Create Files

```bash
cd rdir
touch myfile
# Permission denied
```

**Check permissions:**
```bash
ls -ld rdir
dr-xr-xr-x 2 root root 4096 Jan 28 rdir
```

**Problem:** Directory has execute permission but no write permission for others.

---

## Modifying File Permissions

### Using chmod Command

The `chmod` (change mode) command modifies file permissions.

#### Symbolic Method

**Syntax:**
```bash
chmod [who][operation][permissions] filename
```

**Who:**
- `u` = User (owner)
- `g` = Group
- `o` = Others
- `a` = All (user, group, and others)

**Operations:**
- `+` = Add permission
- `-` = Remove permission
- `=` = Set exact permission

**Permissions:**
- `r` = Read
- `w` = Write
- `x` = Execute

#### Examples

**1. Add execute permission for all:**
```bash
chmod a+x script.sh
ls -l script.sh
# -rwxr-xr-x 1 paul paul 100 Jan 28 script.sh
```

**2. Add write permission for others:**
```bash
chmod o+w rfile.txt
ls -l rfile.txt
# -rw-r--rw- 1 root root 100 Jan 28 rfile.txt
```

**3. Remove all permissions for others:**
```bash
chmod o-rwx script.sh
ls -l script.sh
# -rwxr-x--- 1 paul paul 100 Jan 28 script.sh
```

**4. Set exact permissions:**
```bash
chmod u=rwx,g=rx,o=r script.sh
ls -l script.sh
# -rwxr-xr-- 1 paul paul 100 Jan 28 script.sh
```

### Recursive Permission Changes

To change permissions for all files in a directory:

```bash
mkdir testdir
touch testdir/file1 testdir/file2 testdir/file3

# Change permissions recursively
chmod -R a+x testdir/

# Verify
ls -l testdir/
```

---

## Numeric (Octal) Permission Method

### Understanding Numeric Permissions

Permissions can be represented by three-digit octal numbers (0-7):

```
chmod 777 filename
      │││
      ││└── Others
      │└─── Group
      └──── User
```

### Permission Values

| Number | Binary | Permissions | Meaning |
|--------|--------|-------------|---------|
| 0 | 000 | `---` | No permission |
| 1 | 001 | `--x` | Execute only |
| 2 | 010 | `-w-` | Write only |
| 3 | 011 | `-wx` | Write and execute |
| 4 | 100 | `r--` | Read only |
| 5 | 101 | `r-x` | Read and execute |
| 6 | 110 | `rw-` | Read and write |
| 7 | 111 | `rwx` | Read, write, and execute |

### Calculation Method

Each permission has a value:
- Read (r) = 4
- Write (w) = 2
- Execute (x) = 1

**Example:** `rwx` = 4 + 2 + 1 = 7

### Common Permission Combinations

```bash
# No permissions
chmod 000 script.sh
ls -l script.sh
# ---------- 1 paul paul 100 Jan 28 script.sh

# Full permissions for everyone
chmod 777 script.sh
ls -l script.sh
# -rwxrwxrwx 1 paul paul 100 Jan 28 script.sh

# Owner: all, Group: read+execute, Others: read only
chmod 754 script.sh
ls -l script.sh
# -rwxr-xr-- 1 paul paul 100 Jan 28 script.sh

# Common web file permissions
chmod 644 file.txt
ls -l file.txt
# -rw-r--r-- 1 paul paul 100 Jan 28 file.txt

# Common directory permissions
chmod 755 directory/
ls -ld directory/
# drwxr-xr-x 2 paul paul 4096 Jan 28 directory/
```

---

## Managing File Ownership

### Understanding Ownership

Every file has two owners:
1. **User owner** (the file's creator)
2. **Group owner** (a group of users)

### Viewing Ownership

```bash
ls -l filename
# -rw-r--r-- 1 paul root 100 Jan 28 filename
#              │    │
#              │    └── Group owner
#              └────── User owner
```

### Changing Ownership

**Note:** You typically need root/sudo privileges to change file ownership.

#### Using chown (Change Owner)

**Syntax:**
```bash
chown user:group filename
```

**Examples:**

**1. Change both user and group:**
```bash
# Switch to root user
su

# Change ownership
chown paul:root rfile.txt
ls -l rfile.txt
# -rw-r--r-- 1 paul root 100 Jan 28 rfile.txt
```

**2. Change only user:**
```bash
chown paul rfile.txt
ls -l rfile.txt
# -rw-r--r-- 1 paul root 100 Jan 28 rfile.txt
```

#### Using chgrp (Change Group)

To change only the group owner:

```bash
chgrp paul rfile.txt
ls -l rfile.txt
# -rw-r--r-- 1 paul paul 100 Jan 28 rfile.txt
```

#### Recursive Ownership Change

```bash
# Change ownership of directory and all contents
chown -R paul:paul directory/
```

---

## Practical Examples

### Example 1: Making a Script Executable

```bash
# Create a script
echo '#!/bin/bash' > script.sh
echo 'echo "Hello World"' >> script.sh

# Try to run it
./script.sh
# Permission denied

# Check permissions
ls -l script.sh
# -rw-r--r-- 1 paul paul 30 Jan 28 script.sh

# Add execute permission
chmod +x script.sh

# Verify
ls -l script.sh
# -rwxr-xr-x 1 paul paul 30 Jan 28 script.sh

# Now run it
./script.sh
# Hello World
```

### Example 2: Securing a Private File

```bash
# Create a private file
touch private.txt
echo "Secret data" > private.txt

# Remove all permissions for group and others
chmod 600 private.txt

# Verify
ls -l private.txt
# -rw------- 1 paul paul 12 Jan 28 private.txt
```

### Example 3: Sharing Files with a Group

```bash
# Create a shared file
touch shared.txt

# Set permissions: owner full, group read+write, others none
chmod 660 shared.txt

# Verify
ls -l shared.txt
# -rw-rw---- 1 paul team 0 Jan 28 shared.txt
```

---

## Permission Best Practices

### Security Guidelines

1. **Principle of Least Privilege**: Grant only necessary permissions
2. **Avoid 777**: Never use `chmod 777` on production systems
3. **Protect Sensitive Files**: Use restrictive permissions (600 or 640)
4. **Scripts**: Only executable by owner (700 or 750)
5. **Web Files**: Typically 644 for files, 755 for directories

### Common Permission Schemes

```bash
# Private files (only owner can read/write)
chmod 600 file.txt

# Private executable (only owner can run)
chmod 700 script.sh

# Group-shared files (owner+group can read/write)
chmod 660 shared.txt

# Public readable files (everyone can read, only owner can write)
chmod 644 document.txt

# Public directories (everyone can list and enter)
chmod 755 public_dir/

# Shared directory (owner+group can modify)
chmod 770 team_dir/
```

---

## Summary

### Key Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `ls -l` | View permissions | `ls -l file.txt` |
| `chmod` | Change permissions | `chmod 755 script.sh` |
| `chown` | Change owner | `chown user:group file.txt` |
| `chgrp` | Change group | `chgrp team file.txt` |

### Permission Quick Reference

```
rwx = 7    (read + write + execute)
rw- = 6    (read + write)
r-x = 5    (read + execute)
r-- = 4    (read only)
-wx = 3    (write + execute)
-w- = 2    (write only)
--x = 1    (execute only)
--- = 0    (no permission)
```

### Remember

- **User** permissions apply to the file owner
- **Group** permissions apply to members of the file's group
- **Others** permissions apply to everyone else
- **Read** permission on directories allows listing contents
- **Execute** permission on directories allows entering them
- **Write** permission on directories allows creating/deleting files

---

## Additional Resources

- Man pages: `man chmod`, `man chown`, `man ls`
- Check current user: `whoami`
- Check current groups: `groups`
- View detailed file info: `stat filename`

---

**Thank you for following this guide! If you found it helpful, please share it with others learning Linux.**