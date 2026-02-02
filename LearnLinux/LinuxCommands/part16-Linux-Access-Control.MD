# Linux Access Control Lists (ACL) - Complete Guide

## Introduction

This guide covers **Access Control Lists (ACL)** in Linux, a powerful feature that extends traditional file permissions to provide more granular access control.

### What You'll Learn
- What ACL is and why it's needed
- The limitations of traditional file permissions
- How to use `getfacl` and `setfacl` commands
- Managing user and group-specific permissions
- Recursive ACL application

---

## Table of Contents
1. [Why Do We Need ACL?](#why-do-we-need-acl)
2. [Understanding the Problem](#understanding-the-problem)
3. [What is ACL?](#what-is-acl)
4. [ACL Commands](#acl-commands)
5. [Practical Examples](#practical-examples)
6. [Removing ACL Entries](#removing-acl-entries)
7. [Recursive ACL](#recursive-acl)
8. [Best Practices](#best-practices)

---

## Why Do We Need ACL?

### The Limitation of Traditional Permissions

You might wonder: *"We already have `chmod`, `chown`, and traditional permissions. Why do we need ACL?"*

**Traditional Linux permissions have limitations:**
- Only 3 permission sets: **Owner**, **Group**, **Others**
- Cannot grant permissions to multiple specific users
- Cannot grant different permissions to different groups simultaneously
- Changing "Others" permissions affects ALL users

---

## Understanding the Problem

### Real-World Scenario

Let's look at a practical example:

**Setup:**
```bash
# Paul (owner) creates a file
[paul@server ~]$ touch test.txt
[paul@server ~]$ ls -l test.txt
-rw-rw-r-- 1 paul paul 0 Jan 28 test.txt
```

**Permission Breakdown:**
```
-rw-rw-r--
 │  │  │
 │  │  └─── Others: read only (r--)
 │  └────── Group: read + write (rw-)
 └───────── User (paul): read + write (rw-)
```

### The Challenge

**Requirement:** User `nick` needs write permission to `test.txt`

**Problem:** `nick` is neither the owner nor in paul's group, so he only has "read" permission.

### Why Traditional Solutions Don't Work

**Option 1: Modify "Others" permissions**
```bash
chmod o+w test.txt  # Give write to Others
```
❌ **Problem:** This gives write permission to ALL users, not just nick.

**Option 2: Change ownership**
```bash
chown nick test.txt
```
❌ **Problem:** Paul loses ownership of his own file.

**Option 3: Add to group**
❌ **Problem:** Requires creating groups and may not be practical for temporary permissions.

### ✅ Solution: Access Control Lists (ACL)

ACL allows us to grant specific permissions to specific users or groups without changing base ownership or affecting other users.

---

## What is ACL?

**Access Control List (ACL)** allows you to:
- Give more **specific sets of permissions** to files or directories
- Target **individual users or groups**
- Maintain existing ownership and base permissions
- Create complex permission schemes

### Key Benefits
✅ Grant permissions to multiple specific users  
✅ Assign different permissions to different groups  
✅ No need to modify base permissions  
✅ Fine-grained access control  

---

## ACL Commands

### Two Main Commands

| Command | Purpose | Description |
|---------|---------|-------------|
| `getfacl` | **Get** File ACL | View ACL permissions on a file/directory |
| `setfacl` | **Set** File ACL | Modify ACL permissions on a file/directory |

---

## Practical Examples

### 1. Viewing ACL Permissions

#### Using getfacl

```bash
getfacl test.txt
```

**Output:**
```
# file: test.txt
# owner: paul
# group: paul
user::rw-
group::rw-
other::r--
```

**Explanation:**
- Shows file owner and group
- Displays user, group, and other permissions
- Clean, readable format

**Compare with traditional ls:**
```bash
ls -l test.txt
-rw-rw-r-- 1 paul paul 0 Jan 28 test.txt
```

---

### 2. Granting User-Specific Permissions

#### Syntax
```bash
setfacl -m u:username:permissions filename
```

**Components:**
- `-m` : **Modify** ACL
- `u:` : **User** entry
- `username` : Name of the user
- `permissions` : `r` (read), `w` (write), `x` (execute)
- `filename` : Target file

#### Example: Give nick read+write permission

```bash
# Grant nick read and write permissions
setfacl -m u:nick:rw test.txt
```

**Verify the change:**
```bash
getfacl test.txt
```

**Output:**
```
# file: test.txt
# owner: paul
# group: paul
user::rw-
user:nick:rw-         ← New ACL entry for nick
group::rw-
mask::rw-
other::r--
```

**Also visible in ls output:**
```bash
ls -l test.txt
-rw-rw-r--+ 1 paul paul 0 Jan 28 test.txt
           ↑
    Plus sign indicates ACL is set
```

#### Test the Permission

```bash
# As nick user
[nick@server ~]$ vi test.txt
# Nick can now edit the file!
[nick@server ~]$ echo "Modified by nick" >> test.txt
# Success! Write permission works
```

---

### 3. Granting Group-Specific Permissions

#### Syntax
```bash
setfacl -m g:groupname:permissions filename
```

**Components:**
- `g:` : **Group** entry
- `groupname` : Name of the group
- Rest same as user syntax

#### Example: Give development group read+write+execute

```bash
# Grant permissions to development group
setfacl -m g:dev:rwx test.txt
```

**Verify:**
```bash
getfacl test.txt
```

**Output:**
```
# file: test.txt
# owner: paul
# group: paul
user::rw-
user:nick:rw-
group::rw-
group:dev:rwx         ← New group ACL entry
mask::rwx
other::r--
```

---

### 4. Multiple ACL Entries

You can have multiple user and group ACL entries on the same file:

```bash
# Add multiple users
setfacl -m u:alice:r test.txt
setfacl -m u:bob:rw test.txt
setfacl -m u:charlie:rwx test.txt

# Add multiple groups
setfacl -m g:developers:rwx test.txt
setfacl -m g:testers:r test.txt
```

**Result:**
```bash
getfacl test.txt
```

```
user::rw-
user:nick:rw-
user:alice:r--
user:bob:rw-
user:charlie:rwx
group::rw-
group:dev:rwx
group:testers:r--
mask::rwx
other::r--
```

---

## Removing ACL Entries

### Remove Specific User Entry

```bash
setfacl -x u:username filename
```

**Example:**
```bash
# Remove nick's ACL entry
setfacl -x u:nick test.txt

# Verify
getfacl test.txt
# nick's entry will be gone
```

### Remove Specific Group Entry

```bash
setfacl -x g:groupname filename
```

**Example:**
```bash
# Remove dev group's ACL entry
setfacl -x g:dev test.txt
```

### Remove ALL ACL Entries

To remove all ACL entries at once:

```bash
setfacl -b filename
```

**Example:**
```bash
# Remove all ACL entries
setfacl -b test.txt

# Verify
getfacl test.txt
# Only base permissions remain
ls -l test.txt
-rw-rw-r-- 1 paul paul 0 Jan 28 test.txt
#         ↑ No plus sign - ACL removed
```

---

## Recursive ACL

### The Problem

When you have a directory with many files:

```bash
ls -l folder/
-rw-r--r-- 1 paul paul  100 Jan 28 file1.txt
-rw-r--r-- 1 paul paul  200 Jan 28 file2.txt
-rw-r--r-- 1 paul paul  150 Jan 28 file3.txt
... (50 more files)
```

**Challenge:** Setting ACL on each file individually would be time-consuming!

### Solution: Recursive ACL

Use the `-R` flag to apply ACL to a directory and all its contents:

```bash
setfacl -R -m u:username:permissions directory/
```

#### Example: Grant nick permissions on entire folder

```bash
# Apply ACL recursively to folder and all contents
setfacl -R -m u:nick:rw folder/
```

**Verify:**
```bash
# Check directory
getfacl folder/
ls -ld folder/
drwxrwxr-x+ 2 paul paul 4096 Jan 28 folder/
#         ↑ Plus sign on directory

# Check files inside
ls -l folder/
-rw-rw-r--+ 1 paul paul  100 Jan 28 file1.txt
-rw-rw-r--+ 1 paul paul  200 Jan 28 file2.txt
-rw-rw-r--+ 1 paul paul  150 Jan 28 file3.txt
#         ↑ All files have plus sign

# Check ACL on a file
getfacl folder/file1.txt
user::rw-
user:nick:rw-    ← ACL applied!
group::rw-
mask::rw-
other::r--
```

**Result:** All existing files in the folder now have the ACL entry!

---

## Command Reference

### Complete Command Syntax

#### setfacl Options

| Option | Description | Example |
|--------|-------------|---------|
| `-m` | **Modify** ACL | `setfacl -m u:nick:rw file.txt` |
| `-x` | Remove specific entry | `setfacl -x u:nick file.txt` |
| `-b` | Remove all ACL entries | `setfacl -b file.txt` |
| `-R` | **Recursive** (apply to directory + contents) | `setfacl -R -m u:nick:rw folder/` |
| `-d` | Set **default** ACL (for new files) | `setfacl -d -m u:nick:rw folder/` |

#### Entry Types

| Type | Syntax | Example |
|------|--------|---------|
| User | `u:username:perms` | `u:nick:rw` |
| Group | `g:groupname:perms` | `g:dev:rwx` |
| Other | `o::perms` | `o::r` |
| Mask | `m::perms` | `m::rwx` |

#### Permissions

| Permission | Symbol | Meaning |
|------------|--------|---------|
| Read | `r` | Can read file contents |
| Write | `w` | Can modify file |
| Execute | `x` | Can execute file/enter directory |
| None | `-` | No permission |

---

## Practical Use Cases

### Use Case 1: Shared Project Directory

**Scenario:** Development team needs access to project folder

```bash
# Create project directory
mkdir /projects/webapp
cd /projects/webapp

# Set recursive ACL for dev team
setfacl -R -m g:developers:rwx /projects/webapp

# Set default ACL for new files
setfacl -d -m g:developers:rwx /projects/webapp
```

### Use Case 2: Temporary Contractor Access

**Scenario:** Give contractor temporary access without adding to groups

```bash
# Grant specific permissions to contractor
setfacl -m u:contractor:r /sensitive/report.pdf

# Later, easily revoke
setfacl -x u:contractor /sensitive/report.pdf
```

### Use Case 3: Log File Access

**Scenario:** Multiple admins need to read logs, but only log system can write

```bash
# Grant read-only to admin users
setfacl -m u:admin1:r /var/log/app.log
setfacl -m u:admin2:r /var/log/app.log
setfacl -m u:admin3:r /var/log/app.log

# Or use a group
setfacl -m g:log-readers:r /var/log/app.log
```

---

## Default ACL

### What is Default ACL?

Default ACL is applied to **new files and subdirectories** created within a directory.

### Setting Default ACL

```bash
setfacl -d -m u:username:permissions directory/
```

**Example:**
```bash
# Set default ACL on directory
setfacl -d -m u:nick:rw /shared/

# Create new file in directory
touch /shared/newfile.txt

# Check ACL - nick automatically has permissions!
getfacl /shared/newfile.txt
user::rw-
user:nick:rw-    ← Automatically inherited!
```

### Viewing Default ACL

```bash
getfacl directory/
```

**Output shows both access and default ACL:**
```
# file: /shared
# owner: paul
# group: paul
user::rwx
group::rwx
other::r-x

# default entries (for new files)
default:user::rwx
default:user:nick:rw-
default:group::rwx
default:mask::rwx
default:other::r-x
```

---

## Best Practices

### 1. **Document ACL Usage**
- Keep track of which files/directories have ACL
- Document why specific users have access
- Use comments or a separate documentation file

### 2. **Use Groups When Possible**
- For multiple users with same permissions, use group ACL
- Easier to manage than multiple user entries

### 3. **Regular Audits**
```bash
# Find all files with ACL in a directory
find /path -type f -exec getfacl {} \; 2>/dev/null | grep "^# file"
```

### 4. **Backup ACL**
```bash
# Backup ACL for a directory
getfacl -R /important/directory > acl-backup.txt

# Restore ACL
setfacl --restore=acl-backup.txt
```

### 5. **Be Specific**
- Only grant necessary permissions (principle of least privilege)
- Use read-only when write is not needed
- Regularly review and remove unnecessary ACL entries

### 6. **Test Changes**
- Verify ACL changes with `getfacl`
- Test access with affected user accounts
- Check both positive (allowed) and negative (denied) cases

---

## Troubleshooting

### ACL Not Working?

**1. Check if filesystem supports ACL:**
```bash
mount | grep acl
# Should show "acl" in mount options
```

**2. Mount filesystem with ACL support:**
```bash
mount -o remount,acl /dev/sda1
```

**3. Make it permanent in /etc/fstab:**
```
/dev/sda1  /  ext4  defaults,acl  0  1
```

### Plus Sign Not Showing?

The `+` in `ls -l` only appears when ACL entries exist beyond basic permissions.

```bash
# This might not show +
setfacl -m u:nick:rw file.txt  # If nick already had rw via group

# This will show +
setfacl -m u:alice:r file.txt  # Alice gets specific permission
```

---

## ACL vs Traditional Permissions

| Feature | Traditional Permissions | ACL |
|---------|------------------------|-----|
| **User Access** | Only owner | Owner + multiple specific users |
| **Group Access** | Single group | Multiple groups possible |
| **Granularity** | 3 levels (owner/group/others) | Unlimited entries |
| **Complexity** | Simple | More complex |
| **Portability** | Universal | Requires filesystem support |
| **Best For** | Simple scenarios | Complex permission requirements |

---

## Quick Command Cheatsheet

```bash
# VIEW ACL
getfacl filename

# ADD USER PERMISSION
setfacl -m u:username:rw filename

# ADD GROUP PERMISSION
setfacl -m g:groupname:rwx filename

# RECURSIVE APPLICATION
setfacl -R -m u:username:rw directory/

# SET DEFAULT ACL (for new files)
setfacl -d -m u:username:rw directory/

# REMOVE SPECIFIC USER
setfacl -x u:username filename

# REMOVE SPECIFIC GROUP
setfacl -x g:groupname filename

# REMOVE ALL ACL
setfacl -b filename

# BACKUP ACL
getfacl -R /path > acl_backup.txt

# RESTORE ACL
setfacl --restore=acl_backup.txt

# FIND FILES WITH ACL
find /path -type f -exec ls -l {} \; | grep "^-.*+"
```

---

## Summary

### Key Takeaways

1. **ACL extends traditional permissions** without replacing them
2. **Use ACL when you need:**
   - Specific permissions for multiple users
   - Different permissions for different groups
   - Granular access control
3. **Main commands:** `getfacl` (view) and `setfacl` (modify)
4. **The `+` sign** in `ls -l` indicates ACL is set
5. **Recursive ACL** (`-R`) applies to directories and all contents
6. **Default ACL** (`-d`) applies to newly created files

### When to Use ACL

✅ **Use ACL when:**
- Multiple specific users need different access levels
- Temporary access is needed without group changes
- Complex permission schemes are required
- Fine-grained control is necessary

❌ **Use traditional permissions when:**
- Simple owner/group/others scheme is sufficient
- All users in a category need same permissions
- Simplicity and portability are priorities

---

## Additional Resources

**Man Pages:**
```bash
man setfacl
man getfacl
man acl
```

**Check ACL Support:**
```bash
# Check if installed
which setfacl
which getfacl

# Check filesystem capabilities
tune2fs -l /dev/sda1 | grep acl
```

---

**If you found this guide helpful, please share it with others learning Linux system administration!**

**Thank you for reading!** 🐧