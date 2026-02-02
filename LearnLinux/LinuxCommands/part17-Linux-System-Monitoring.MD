# Linux System Monitoring Commands - Complete Guide

## Introduction

This guide covers essential **system monitoring commands** in Linux that every system administrator and DevOps engineer should know. These commands help you monitor disk usage, memory utilization, and system resources.

### What You'll Learn
- `df` - Disk filesystem usage
- `du` - Disk usage by directories and files
- `free` - Memory (RAM) usage
- Practical use cases and monitoring strategies

---

## Table of Contents
1. [df Command - Disk Filesystem Usage](#df-command---disk-filesystem-usage)
2. [du Command - Disk Usage](#du-command---disk-usage)
3. [free Command - Memory Usage](#free-command---memory-usage)
4. [Practical Use Cases](#practical-use-cases)
5. [Monitoring Best Practices](#monitoring-best-practices)
6. [Command Cheatsheet](#command-cheatsheet)

---

## df Command - Disk Filesystem Usage

### What is df?

The `df` command shows **disk filesystem usage** - how much disk space is used and available on your system's filesystems.

**df** = **D**isk **F**ilesystem

### Basic Usage

```bash
df
```

**Output:**
```
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/sda1       20511312 8523456  10931648  44% /
tmpfs             524288       0    524288   0% /dev/shm
/dev/sda2       51475068 2547580  46291244   6% /home
```

**Problem:** Output is not very readable (shows in 1K blocks)

### Human-Readable Format

The most commonly used option:

```bash
df -h
```

**Output:**
```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        20G  8.2G   11G  44% /
tmpfs           512M     0  512M   0% /dev/shm
/dev/sda2        50G  2.5G   45G   6% /home
/dev/sda3       100G   50G   50G  50% /var
```

**Explanation:**
- **Filesystem**: Device or partition name
- **Size**: Total size of the filesystem
- **Used**: Space currently used
- **Avail**: Space available for use
- **Use%**: Percentage of space used
- **Mounted on**: Where the filesystem is mounted

### Key Points to Monitor

**Root Filesystem (`/`):**
- Most critical filesystem
- System files and OS located here
- Should not exceed 80-90% usage

**Common Locations:**
```
/           → Root filesystem (OS, system files)
/home       → User home directories
/var        → Variable data (logs, databases)
/tmp        → Temporary files
/boot       → Boot files and kernel
```

### Different Size Formats

**Kilobytes:**
```bash
df -k
```

**Megabytes:**
```bash
df -m
```

**Gigabytes:**
```bash
df --block-size=G
# or
df -BG
```

**Output Example:**
```bash
df -BG
Filesystem     1G-blocks  Used Available Use% Mounted on
/dev/sda1            20G    9G       11G  44% /
/dev/sda2            50G    3G       47G   6% /home
```

### Practical Examples

**1. Check specific filesystem:**
```bash
df -h /home
```

**2. Show filesystem type:**
```bash
df -T
```

**Output:**
```
Filesystem     Type     Size  Used Avail Use% Mounted on
/dev/sda1      ext4      20G  8.2G   11G  44% /
/dev/sda2      xfs       50G  2.5G   45G   6% /home
```

**3. Show only specific filesystem type:**
```bash
df -t ext4
```

**4. Exclude specific filesystem type:**
```bash
df -x tmpfs
```

---

## du Command - Disk Usage

### What is du?

The `du` command shows **disk usage** of files and directories. Unlike `df` which shows filesystem-level usage, `du` drills down into directories.

**du** = **D**isk **U**sage

### Basic Usage

```bash
du
```

**Output:**
```
8       ./documents
4       ./scripts
12      .
```

**Problem:** Too much information, shows every file and directory!

### Human-Readable Format

```bash
du -h
```

**Output:**
```
8.0K    ./documents
4.0K    ./scripts
12K     .
```

### Check Specific Directory

```bash
du -h /path/to/directory
```

**Example:**
```bash
du -h /home/paul/videos
```

**Output:**
```
1.5G    /home/paul/videos/project1
2.0G    /home/paul/videos/project2
500M    /home/paul/videos/backup
4.0G    /home/paul/videos
```

### Summary View

Show only total size of a directory:

```bash
du -sh directory_name
```

**Example:**
```bash
du -sh /home/paul/videos
```

**Output:**
```
4.5G    /home/paul/videos
```

**Options explained:**
- `-s` = **Summary** (show only total)
- `-h` = **Human-readable** format

### Show All Files and Directories

Include files in the output, not just directories:

```bash
du -ah directory_name
```

**Example:**
```bash
du -ah /home/paul/myfolder
```

**Output:**
```
500M    /home/paul/myfolder/video1.mp4
300M    /home/paul/myfolder/video2.mp4
4.0K    /home/paul/myfolder/notes.txt
2.0K    /home/paul/myfolder/config.ini
800M    /home/paul/myfolder
```

### Show Total at the End

```bash
du -ch directory_name
```

**Example:**
```bash
du -ch /home/paul/projects
```

**Output:**
```
1.2G    /home/paul/projects/web-app
800M    /home/paul/projects/mobile-app
500M    /home/paul/projects/scripts
2.5G    total
```

### Limit Directory Depth

Show only up to certain depth level:

```bash
du -h --max-depth=1 directory_name
```

**Example:**
```bash
du -h --max-depth=1 /var
```

**Output:**
```
2.5G    /var/log
1.2G    /var/lib
500M    /var/cache
100M    /var/tmp
4.3G    /var
```

### Sort by Size

**Find largest directories:**
```bash
du -h | sort -hr | head -n 10
```

**Explanation:**
- `sort -hr` = Sort human-readable, reverse order (largest first)
- `head -n 10` = Show top 10 results

**Example Output:**
```
4.5G    /home/paul/videos
2.5G    /home/paul/backups
1.8G    /home/paul/downloads
800M    /home/paul/documents
500M    /home/paul/music
```

### Exclude Patterns

**Exclude specific files or directories:**
```bash
du -h --exclude="*.log" /var
```

**Exclude multiple patterns:**
```bash
du -h --exclude="*.tmp" --exclude="*.log" /var
```

---

## free Command - Memory Usage

### What is free?

The `free` command displays **RAM (Random Access Memory)** usage on your system.

**Important:** `df` and `du` show disk (storage) usage, while `free` shows memory (RAM) usage.

### Basic Usage

```bash
free
```

**Output (in KB):**
```
              total        used        free      shared  buff/cache   available
Mem:        8042316     3215648     2548932      123456     2277736     4398436
Swap:       2097148      102400     1994748
```

### Human-Readable Format

```bash
free -h
```

**Output:**
```
              total        used        free      shared  buff/cache   available
Mem:           7.7G        3.1G        2.4G        120M        2.2G        4.2G
Swap:          2.0G        100M        1.9G
```

### Understanding the Output

**Memory Row (Mem):**
- **total**: Total installed RAM
- **used**: Memory currently in use by programs
- **free**: Completely unused memory
- **shared**: Memory shared between processes
- **buff/cache**: Memory used for buffers and cache (can be freed if needed)
- **available**: Memory available for new applications (includes reclaimable cache)

**Swap Row:**
- Virtual memory on disk used when RAM is full
- Slower than RAM
- High swap usage indicates insufficient RAM

### Different Display Formats

**Megabytes:**
```bash
free -m
```

**Gigabytes:**
```bash
free -g
```

**Display total row:**
```bash
free -h -t
```

**Output:**
```
              total        used        free      shared  buff/cache   available
Mem:           7.7G        3.1G        2.4G        120M        2.2G        4.2G
Swap:          2.0G        100M        1.9G
Total:         9.7G        3.2G        4.3G
```

### Continuous Monitoring

**Update every N seconds:**
```bash
free -h -s 3
```

This will refresh the memory status every 3 seconds.

**Update with count:**
```bash
free -h -s 1 -c 4
```

This will:
- Refresh every 1 second (`-s 1`)
- Run 4 times (`-c 4`)
- Then stop automatically

**Example output:**
```
              total        used        free      shared  buff/cache   available
Mem:           7.7G        3.1G        2.4G        120M        2.2G        4.2G
Swap:          2.0G        100M        1.9G

[After 1 second]

              total        used        free      shared  buff/cache   available
Mem:           7.7G        3.2G        2.3G        120M        2.2G        4.1G
Swap:          2.0G        100M        1.9G

[Continues 4 times total]
```

**Stop continuous monitoring:**
```
Ctrl + C
```

---

## Practical Use Cases

### Use Case 1: Pre-Deployment Check

**Scenario:** You're deploying an application that requires 5GB of RAM.

**Steps:**
```bash
# Check available memory
free -h

# Output
              total        used        free      shared  buff/cache   available
Mem:           7.7G        2.0G        1.5G        100M        4.2G        5.2G
Swap:          2.0G         0B        2.0G
```

**Analysis:**
- Available: **5.2G** ✅
- Application needs: **5GB** ✅
- **Conclusion:** Sufficient memory available, safe to deploy!

### Use Case 2: Disk Space Alert

**Scenario:** Monitor disk usage and alert when it reaches 80%.

**Check current usage:**
```bash
df -h
```

**Output:**
```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        20G   17G    3G  85% /
```

**Action Required:**
- Usage at **85%** 🚨
- Need to investigate and clean up space

**Find large directories:**
```bash
du -sh /var/* | sort -hr | head -10
```

**Output:**
```
15G     /var/log
2.5G    /var/lib
500M    /var/cache
```

**Solution:** Clean up old logs in `/var/log`

### Use Case 3: Application Performance Testing

**Scenario:** Monitor memory usage while application is running.

**Start monitoring:**
```bash
free -h -s 2
```

**Steps:**
1. Start the monitoring command
2. Launch your application
3. Perform operations/load testing
4. Observe memory usage in real-time

**Example monitoring output:**
```
[Initial]
Mem:  7.7G  2.0G  5.2G (Available)

[After app starts]
Mem:  7.7G  3.5G  3.7G (Available)

[Under load]
Mem:  7.7G  6.0G  1.2G (Available) ⚠️

[After load test]
Mem:  7.7G  3.8G  3.4G (Available)
```

**Analysis:**
- Application uses ~1.5GB at startup
- Under load, uses up to 4GB
- Memory usage drops after load
- **Conclusion:** Application has memory leak or needs optimization if memory doesn't decrease

### Use Case 4: Finding Space Hogs

**Scenario:** Disk is almost full, need to find what's taking up space.

**Step 1: Check which filesystem is full**
```bash
df -h
```

**Step 2: Find largest directories**
```bash
du -h --max-depth=1 / | sort -hr | head -20
```

**Step 3: Drill down into specific directory**
```bash
du -h --max-depth=2 /var | sort -hr | head -20
```

**Step 4: Find large files**
```bash
find /var -type f -size +100M -exec ls -lh {} \; | sort -k5 -hr
```

### Use Case 5: Automated Monitoring Script

**Create a simple monitoring script:**

```bash
#!/bin/bash
# monitor.sh - System monitoring script

echo "=== Disk Usage ==="
df -h | grep -v tmpfs

echo ""
echo "=== Memory Usage ==="
free -h

echo ""
echo "=== Top 5 Large Directories ==="
du -sh /home/* /var/* 2>/dev/null | sort -hr | head -5
```

**Make it executable and run:**
```bash
chmod +x monitor.sh
./monitor.sh
```

---

## Monitoring Best Practices

### 1. Regular Checks

**Create a monitoring schedule:**
- Daily: Quick `df -h` and `free -h` check
- Weekly: Detailed `du -sh` analysis
- Monthly: Comprehensive audit and cleanup

### 2. Set Up Alerts

**Disk usage threshold:**
- Warning at 80%
- Critical at 90%

**Memory usage threshold:**
- Warning when available < 20%
- Critical when available < 10%

### 3. Keep Historical Data

**Log monitoring data:**
```bash
# Add to cron job
0 */6 * * * df -h >> /var/log/disk-usage.log
0 */6 * * * free -h >> /var/log/memory-usage.log
```

### 4. Cleanup Strategy

**Regular cleanup:**
- Old log files
- Temporary files
- Package cache
- Old backups

**Example cleanup commands:**
```bash
# Clean old logs
find /var/log -name "*.log" -mtime +30 -delete

# Clean package cache
apt clean  # Debian/Ubuntu
yum clean all  # RHEL/CentOS

# Clean temp files
rm -rf /tmp/*
```

### 5. Capacity Planning

**Monitor trends:**
- Track growth over time
- Predict when disk/memory will be full
- Plan upgrades before critical shortage

---

## Command Cheatsheet

### df (Disk Filesystem)

| Command | Description |
|---------|-------------|
| `df -h` | Human-readable disk usage |
| `df -k` | Display in kilobytes |
| `df -m` | Display in megabytes |
| `df -BG` | Display in gigabytes |
| `df -T` | Show filesystem type |
| `df -h /home` | Check specific filesystem |
| `df -t ext4` | Show only ext4 filesystems |
| `df -x tmpfs` | Exclude tmpfs filesystems |

### du (Disk Usage)

| Command | Description |
|---------|-------------|
| `du -h` | Human-readable format |
| `du -sh directory` | Summary of directory |
| `du -ah directory` | Show all files and dirs |
| `du -ch directory` | Show total at end |
| `du -h --max-depth=1` | Limit depth to 1 level |
| `du -h \| sort -hr \| head -10` | Top 10 largest |
| `du -h --exclude="*.log"` | Exclude pattern |

### free (Memory Usage)

| Command | Description |
|---------|-------------|
| `free -h` | Human-readable format |
| `free -m` | Display in megabytes |
| `free -g` | Display in gigabytes |
| `free -h -t` | Show total row |
| `free -h -s 3` | Update every 3 seconds |
| `free -h -s 1 -c 4` | Update 1s, run 4 times |

---

## Quick Reference

### What to Monitor

| Metric | Command | Threshold |
|--------|---------|-----------|
| Disk space | `df -h` | < 80% usage |
| Directory size | `du -sh` | Monitor growth |
| Memory usage | `free -h` | > 20% available |
| Swap usage | `free -h` | < 50% usage |

### Warning Signs

🚨 **Disk Issues:**
- Disk usage > 90%
- Rapid growth in disk usage
- `/var/log` growing quickly

🚨 **Memory Issues:**
- Available memory < 10%
- High swap usage (> 50%)
- Swap constantly increasing

### Quick Troubleshooting

**When disk is full:**
```bash
# 1. Find the culprit
df -h
du -sh /* | sort -hr | head -10

# 2. Clean up
# Check logs
ls -lh /var/log/*.log
# Remove old logs
find /var/log -name "*.log" -mtime +7 -delete

# 3. Verify
df -h
```

**When memory is low:**
```bash
# 1. Check current usage
free -h

# 2. Find memory hogs
ps aux --sort=-%mem | head -10

# 3. Take action
# Restart problematic services
# Kill unnecessary processes
```

---

## Integration with Monitoring Tools

These commands are foundation for monitoring tools:

### Popular Tools
- **Nagios**: Uses df, du, free for checks
- **Zabbix**: Monitors disk and memory
- **Prometheus**: Collects metrics
- **Grafana**: Visualizes data

### Custom Alerting

**Example email alert script:**
```bash
#!/bin/bash
THRESHOLD=80

USAGE=$(df -h / | grep / | awk '{print $5}' | sed 's/%//g')

if [ $USAGE -gt $THRESHOLD ]; then
    echo "Disk usage is ${USAGE}% on $(hostname)" | 
    mail -s "Disk Space Alert" admin@example.com
fi
```

---

## Summary

### Key Takeaways

1. **df**: Monitor filesystem-level disk usage
2. **du**: Analyze directory and file sizes
3. **free**: Check RAM and swap usage

### When to Use Each

| Need | Use | Example |
|------|-----|---------|
| Overall disk space | `df -h` | Check if disk is full |
| Find large directories | `du -sh` | Find space hogs |
| Memory available | `free -h` | Before deploying app |
| Real-time monitoring | `free -h -s 1` | During performance test |

### Best Practices

✅ Monitor regularly  
✅ Set up automated alerts  
✅ Keep historical data  
✅ Plan capacity ahead  
✅ Clean up proactively  

---

## Additional Resources

**Man pages:**
```bash
man df
man du
man free
```

**Related commands:**
```bash
# Process monitoring
top
htop

# I/O monitoring
iostat
iotop

# Network monitoring
netstat
ss
```

---

**Remember:** Proactive monitoring prevents emergency situations. Regular checks and automated alerts are essential for system reliability!

**If you found this guide helpful, please share it with others learning Linux system administration!** 🐧