# Linux ps Command - Complete Guide

## Introduction

The **ps command** (Process Status) is one of the most essential commands in Linux for monitoring and managing processes. This guide covers everything you need to know about viewing and managing running processes on Linux systems.

### What You'll Learn
- What processes are and how they work
- Basic to advanced `ps` command usage
- Practical examples for real-world scenarios
- Finding and monitoring specific processes
- Understanding process hierarchies

---

## Table of Contents
1. [Understanding Processes](#understanding-processes)
2. [Basic ps Command](#basic-ps-command)
3. [Commonly Used ps Options](#commonly-used-ps-options)
4. [Practical Examples](#practical-examples)
5. [Filtering Processes](#filtering-processes)
6. [Process Trees](#process-trees)
7. [Real-World Use Cases](#real-world-use-cases)
8. [Command Cheatsheet](#command-cheatsheet)

---

## Understanding Processes

### What is a Process?

When you run any **application** or **service** on a Linux server, it creates a **process** in the background. Each process has:

- **Process ID (PID)**: A unique number to identify the process
- **Owner**: User who started the process
- **Resources**: CPU and memory usage
- **State**: Running, sleeping, stopped, etc.

**Example:**
```
When you start Apache web server → httpd process is created
When you run Python script → python process is created
When you open terminal → bash process is created
```

### Why Monitor Processes?

- Check if an application is running
- Identify resource-heavy processes
- Troubleshoot performance issues
- Kill hung or problematic processes
- Monitor system health

---

## Basic ps Command

### Simplest Usage

```bash
ps
```

**Output:**
```
  PID TTY          TIME CMD
 1234 pts/0    00:00:00 bash
 5678 pts/0    00:00:00 ps
```

**What it shows:**
- Only processes in the **current shell**
- Your current bash shell
- The ps command itself (since it's running)

### Understanding Columns

| Column | Meaning | Description |
|--------|---------|-------------|
| **PID** | Process ID | Unique identifier for the process |
| **TTY** | Terminal Type | Terminal where process is running |
| **TIME** | CPU Time | Amount of CPU time used (minutes:seconds) |
| **CMD** | Command | Command that started the process |

---

## Commonly Used ps Options

### 1. Show All Processes (Current Terminal)

```bash
ps -e
# or
ps -A
```

Both commands are **identical** - they show all processes running on the system.

**Usage with pager:**
```bash
ps -e | more
```

This displays processes page by page.

**Output:**
```
  PID TTY          TIME CMD
    1 ?        00:00:02 systemd
    2 ?        00:00:00 kthreadd
  123 ?        00:00:01 sshd
  456 ?        00:00:05 httpd
  789 ?        00:00:02 mysqld
 1234 pts/0    00:00:00 bash
```

### 2. Full Format Display

```bash
ps -ef
```

Shows **all processes** in **full format** with more information.

**Output:**
```
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 10:30 ?        00:00:02 /sbin/init
root       123     1  0 10:30 ?        00:00:01 /usr/sbin/sshd
apache     456   123  0 10:31 ?        00:00:05 /usr/sbin/httpd
mysql      789     1  0 10:32 ?        00:00:02 /usr/sbin/mysqld
paul      1234  1000  0 14:30 pts/0    00:00:00 bash
```

**Additional Columns:**
- **UID**: User ID (which user owns the process)
- **PPID**: Parent Process ID
- **C**: CPU utilization
- **STIME**: Start time of the process

**Usage with pager:**
```bash
ps -ef | more
```

### 3. BSD Format (Most Detailed)

```bash
ps aux
```

**Note:** No hyphen before options!

Shows all processes in **BSD format** with maximum details.

**Output:**
```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 193904  6784 ?        Ss   10:30   0:02 /sbin/init
root       123  0.0  0.2  82560  4328 ?        Ss   10:30   0:01 /usr/sbin/sshd
apache     456  0.1  2.5 453204 52456 ?        S    10:31   0:05 /usr/sbin/httpd
mysql      789  0.3  8.5 1753204 175456 ?      Sl   10:32   0:12 /usr/sbin/mysqld
paul      1234  0.0  0.1  23456  3456 pts/0    Ss   14:30   0:00 bash
```

**New Columns:**
- **USER**: Username (instead of UID)
- **%CPU**: Percentage of CPU usage
- **%MEM**: Percentage of memory (RAM) usage
- **VSZ**: Virtual memory size
- **RSS**: Resident set size (physical memory)
- **STAT**: Process state
- **START**: When the process started

**This is similar to Windows Task Manager!**

---

## Practical Examples

### Example 1: Check if Apache is Running

**Scenario:** You want to verify if the Apache web server (httpd) is running.

```bash
ps -ef | grep httpd
```

**Output:**
```
root      1317     1  0 10:31 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
apache    1318  1317  0 10:31 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
apache    1319  1317  0 10:31 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
apache    1320  1317  0 10:31 ?        00:00:00 /usr/sbin/httpd -DFOREGROUND
paul      2547  2134  0 14:45 pts/0    00:00:00 grep --color=auto httpd
```

**Analysis:**
- **Main process**: PID 1317 (owned by root)
- **Child processes**: PIDs 1318-1320 (owned by apache)
- **grep command**: Also appears (PID 2547) - this is the grep command itself

**Verify with systemctl:**
```bash
systemctl status httpd
```

**Output:**
```
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled)
   Active: active (running) since Mon 2025-01-28 10:31:00 UTC
 Main PID: 1317 (httpd)
   Status: "Total requests: 0; Idle/Busy workers 100/0"
```

**Notice:** Main PID matches (1317)!

### Example 2: Check Java Processes

```bash
ps -ef | grep java
```

### Example 3: Check Python Processes

```bash
ps -ef | grep python
```

---

## Filtering Processes

### 1. Filter by User

Show all processes owned by a specific user:

```bash
ps -u username
```

**Example - Show root user processes:**
```bash
ps -u root
```

**Example - Show paul user processes:**
```bash
ps -u paul
```

**Output:**
```
  PID TTY          TIME CMD
 1234 pts/0    00:00:00 bash
 2134 pts/0    00:00:00 ps
 2547 pts/0    00:00:00 more
```

**These are all processes owned by user paul.**

### 2. Filter by Group

Show all processes owned by a specific group:

```bash
ps -g groupname
```

**Example:**
```bash
ps -g paul
```

**Shows all processes associated with the paul group.**

### 3. Filter by Process Name with grep

**General syntax:**
```bash
ps -ef | grep process_name
```

**Examples:**
```bash
# Check Apache
ps -ef | grep httpd

# Check MySQL
ps -ef | grep mysql

# Check SSH
ps -ef | grep sshd

# Check Nginx
ps -ef | grep nginx

# Check Node.js
ps -ef | grep node
```

---

## Process Trees

### Understanding Process Hierarchies

Processes can have **parent-child relationships**:
- A main application process can spawn multiple sub-processes
- These work together to run your application

**Example:** Apache web server
- **Main process** (PID 1317) - runs as root
- **Worker processes** (PIDs 1318-1320) - run as apache user
- All workers are children of the main process

### View Process Tree

```bash
ps -ejH
```

**Options:**
- `-e` = All processes
- `-j` = Jobs format
- `-H` = Show hierarchy

**Example Output:**
```
  PID  PGID   SID TTY          TIME CMD
    1     1     1 ?        00:00:02 systemd
  123   123   123 ?        00:00:01   sshd
  456   456   456 ?        00:00:05   crond
 1317  1317  1317 ?        00:00:00   httpd
 1318  1317  1317 ?        00:00:00     httpd
 1319  1317  1317 ?        00:00:00     httpd
 1320  1317  1317 ?        00:00:00     httpd
```

**Notice the indentation:**
- Main httpd (1317) is at one level
- Child processes (1318-1320) are indented under it

### View Specific Process Tree

**Combine with grep:**
```bash
ps -ejH | grep httpd
```

**Output:**
```
 1317  1317  1317 ?        00:00:00   httpd
 1318  1317  1317 ?        00:00:00     httpd
 1319  1317  1317 ?        00:00:00     httpd
 1320  1317  1317 ?        00:00:00     httpd
```

**Compare with systemctl:**
```bash
systemctl status httpd
```

**Output:**
```
● httpd.service
   Main PID: 1317 (httpd)
   CGroup: /system.slice/httpd.service
           ├─1317 /usr/sbin/httpd
           ├─1318 /usr/sbin/httpd
           ├─1319 /usr/sbin/httpd
           └─1320 /usr/sbin/httpd
```

**Same hierarchy visible in both!**

### Alternative: pstree Command

For a better visual tree:

```bash
pstree
```

**Or for specific process:**
```bash
pstree -p 1317
```

---

## Real-World Use Cases

### Use Case 1: Troubleshoot High CPU Usage

**Scenario:** Server is slow, need to find CPU-intensive processes.

```bash
ps aux --sort=-%cpu | head -10
```

**Output:**
```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
mysql      789 45.3  8.5 1753204 175456 ?      Sl   10:32   45:12 /usr/sbin/mysqld
apache     456 12.1  2.5 453204 52456 ?        S    10:31   12:05 /usr/sbin/httpd
node      2134  8.5  4.2 823456 87234 ?        Sl   14:20   3:45 node server.js
```

**Analysis:**
- MySQL is using **45.3% CPU** - investigate queries
- Apache using **12.1%** - check traffic
- Node.js using **8.5%** - check application code

### Use Case 2: Find Memory Leaks

**Scenario:** Memory usage is growing, need to find the culprit.

```bash
ps aux --sort=-%mem | head -10
```

**Output:**
```
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
java      3456  5.5 35.2 8753204 1875456 ?     Sl   09:30  155:12 java -jar app.jar
mysql      789 45.3  8.5 1753204 175456 ?      Sl   10:32   45:12 /usr/sbin/mysqld
```

**Analysis:**
- Java application using **35.2% memory** - potential memory leak

### Use Case 3: Monitor Specific Application

**Scenario:** Monitor your application's process continuously.

```bash
watch -n 2 'ps aux | grep myapp'
```

**This updates every 2 seconds** showing real-time changes.

### Use Case 4: Check All User Processes

**Scenario:** Audit what a specific user is running.

```bash
ps -fu username
```

**Example:**
```bash
ps -fu paul
```

**Output with full details:**
```
UID        PID  PPID  C STIME TTY          TIME CMD
paul      1234  1000  0 14:30 pts/0    00:00:00 bash
paul      2547  1234  0 14:45 pts/0    00:00:00 vi script.sh
paul      2892  1234  0 15:00 pts/0    00:00:00 python app.py
```

### Use Case 5: Find Zombie Processes

**Zombie processes** are dead processes that haven't been cleaned up.

```bash
ps aux | grep Z
```

**Or:**
```bash
ps aux | awk '$8 ~ /Z/ {print}'
```

---

## Advanced Usage

### 1. Custom Output Format

Select specific columns to display:

```bash
ps -eo pid,user,%cpu,%mem,cmd
```

**Output:**
```
  PID USER     %CPU %MEM CMD
    1 root      0.0  0.1 /sbin/init
  123 root      0.0  0.2 /usr/sbin/sshd
  456 apache    0.1  2.5 /usr/sbin/httpd
```

### 2. Sort by Different Criteria

**Sort by CPU (descending):**
```bash
ps aux --sort=-%cpu
```

**Sort by memory (descending):**
```bash
ps aux --sort=-%mem
```

**Sort by process ID:**
```bash
ps aux --sort=pid
```

**Multiple sort criteria:**
```bash
ps aux --sort=-%cpu,-%mem
```

### 3. Show Process with Specific PID

```bash
ps -p 1317
```

**Or multiple PIDs:**
```bash
ps -p 1317,1318,1319
```

### 4. Show Full Command Line

Sometimes the CMD is truncated. Show full command:

```bash
ps -ef --forest
```

**Or:**
```bash
ps auxww
```

The `ww` shows unlimited width.

---

## Process States

### Understanding STAT Column

In `ps aux` output, the **STAT** column shows process state:

| Code | Meaning |
|------|---------|
| **R** | Running or runnable |
| **S** | Sleeping (waiting for an event) |
| **D** | Uninterruptible sleep (usually I/O) |
| **Z** | Zombie (terminated but not cleaned) |
| **T** | Stopped (by job control signal) |
| **t** | Stopped (by debugger) |

**Additional modifiers:**
| Code | Meaning |
|------|---------|
| **<** | High priority |
| **N** | Low priority |
| **L** | Has pages locked in memory |
| **s** | Session leader |
| **l** | Multi-threaded |
| **+** | In foreground process group |

**Example States:**
```
Ss   - Sleeping session leader
R+   - Running in foreground
Sl   - Sleeping, multi-threaded
Z    - Zombie process
```

---

## Comparison with Other Tools

### ps vs top

| Feature | ps | top |
|---------|----|----|
| **Type** | Snapshot | Real-time |
| **Updates** | Static | Dynamic |
| **Interactivity** | No | Yes |
| **Best for** | Scripts, quick checks | Interactive monitoring |

**Example usage:**
```bash
# Quick check
ps aux

# Interactive monitoring
top
```

### ps vs htop

**htop** is an improved version of **top** with:
- Color coding
- Mouse support
- Visual CPU/memory bars
- Easier process management

```bash
htop
```

### ps vs systemctl

For **services**, use **systemctl**:

```bash
# Check service status
systemctl status httpd

# List all services
systemctl list-units --type=service
```

For **processes**, use **ps**.

---

## Command Cheatsheet

### Basic Commands

| Command | Description |
|---------|-------------|
| `ps` | Show processes in current shell |
| `ps -e` or `ps -A` | Show all processes |
| `ps -ef` | All processes, full format |
| `ps aux` | All processes, BSD format (most detailed) |

### Filtering

| Command | Description |
|---------|-------------|
| `ps -u username` | Show user's processes |
| `ps -g groupname` | Show group's processes |
| `ps -ef \| grep name` | Find process by name |
| `ps -p PID` | Show specific process |

### Sorting

| Command | Description |
|---------|-------------|
| `ps aux --sort=-%cpu` | Sort by CPU (high to low) |
| `ps aux --sort=-%mem` | Sort by memory (high to low) |
| `ps aux --sort=pid` | Sort by process ID |

### Tree View

| Command | Description |
|---------|-------------|
| `ps -ejH` | Show process tree |
| `ps -ejH \| grep name` | Show specific process tree |
| `pstree` | Visual process tree |
| `pstree -p PID` | Tree for specific process |

### Custom Output

| Command | Description |
|---------|-------------|
| `ps -eo pid,user,cmd` | Custom columns |
| `ps auxww` | Show full command line |

---

## Quick Reference

### Most Useful Commands

**1. Check if process is running:**
```bash
ps -ef | grep process_name
```

**2. Find resource hogs:**
```bash
# CPU intensive
ps aux --sort=-%cpu | head -10

# Memory intensive
ps aux --sort=-%mem | head -10
```

**3. Monitor specific user:**
```bash
ps -fu username
```

**4. View process tree:**
```bash
ps -ejH | grep process_name
```

**5. Get process details:**
```bash
ps aux | grep PID
```

---

## Troubleshooting Tips

### Process Not Showing Up?

**1. Check if it's running:**
```bash
systemctl status service_name
```

**2. Check all variations:**
```bash
ps -ef | grep -i name  # Case-insensitive
```

**3. Check exact command:**
```bash
ps auxww | grep name  # Full command line
```

### Too Many Processes?

**Filter out grep itself:**
```bash
ps -ef | grep httpd | grep -v grep
```

**Or use pgrep:**
```bash
pgrep -a httpd
```

### Find Process Using Port

**Combine with netstat/ss:**
```bash
netstat -tulpn | grep :80
ss -tulpn | grep :80
```

---

## Integration with Other Commands

### Kill Processes

**After finding with ps:**
```bash
# Find process
ps -ef | grep myapp

# Kill it
kill PID
kill -9 PID  # Force kill
```

### Monitor Continuously

```bash
# Update every 2 seconds
watch -n 2 'ps aux | grep myapp'

# Or use top
top -p PID
```

### Export to File

```bash
# Save for analysis
ps aux > processes.txt

# Compare before/after
ps aux > before.txt
# ... make changes ...
ps aux > after.txt
diff before.txt after.txt
```

---

## Summary

### Key Takeaways

1. **ps** shows process information (snapshot)
2. **ps aux** gives most detailed info (CPU, memory, etc.)
3. Use **grep** to filter specific processes
4. Use **-u** to filter by user
5. Use **-ejH** to see process hierarchies

### Essential Commands to Remember

```bash
# Check if running
ps -ef | grep process_name

# Find resource usage
ps aux --sort=-%cpu | head -10

# View process tree
ps -ejH | grep process_name

# Monitor user processes
ps -u username
```

### When to Use ps

✅ **Quick checks**: Is process running?  
✅ **Resource monitoring**: CPU/memory usage  
✅ **Troubleshooting**: Find problematic processes  
✅ **Scripting**: Automated process management  
✅ **Auditing**: Track user activity  

---

## Additional Resources

**Man pages:**
```bash
man ps
```

**Related commands:**
```bash
top      # Interactive process viewer
htop     # Better interactive viewer
pgrep    # Find processes by name
pkill    # Kill processes by name
pstree   # Show process tree
```

**System monitoring:**
```bash
vmstat   # Virtual memory statistics
iostat   # I/O statistics
free     # Memory usage
df       # Disk usage
```

---

**Remember:** The `ps` command is one of the most important tools for Linux system administration. Master it, and you'll be able to diagnose and troubleshoot most system issues!

**If you found this guide helpful, please share it with others learning Linux!** 🐧