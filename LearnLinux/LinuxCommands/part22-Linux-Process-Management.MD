# Linux Process Management Commands Guide

## Introduction

This guide covers five essential Linux commands for process management:
1. **jobs** - View active jobs
2. **bg** - Run jobs in the background
3. **fg** - Bring jobs to the foreground
4. **nice** - Set process priority
5. **nohup** - Run processes that persist after terminal closes

These commands have been briefly covered in previous videos, but here we'll explore them in detail to understand how to effectively manage processes.

## Understanding Jobs

### What is a Job?

A **job** is simply a task or work assigned to the terminal. When you give your terminal some work to do, and it's actively performing that task, it becomes a job.

### The jobs Command

Lists all active jobs in the current terminal session.

```bash
jobs
```

**Important:** Jobs are specific to each terminal session. If you start a fresh terminal, there will be no jobs initially.

### Example: Creating a Job

```bash
sleep 100
```

This command tells the terminal to sleep for 100 seconds. While it's running, it's considered a job.

### Stopping a Job

Press `Ctrl+Z` to stop (suspend) a running job.

```bash
sleep 100
# Press Ctrl+Z
[1]+  Stopped                 sleep 100
```

After stopping, check active jobs:

```bash
jobs
# Output:
[1]+  Stopped                 sleep 100
```

**Job Information:**
- `[1]` - Job ID
- `Stopped` - Current status
- `sleep 100` - Command name

The job is paused but still exists in the terminal's memory.

## Background and Foreground Execution

### bg (Background) Command

**Full Form:** Background

**Purpose:** Resume a stopped job in the background, allowing you to continue using the terminal.

**Syntax:**
```bash
bg
```

Or for a specific job:
```bash
bg %job_id
```

### Example: Background Execution

```bash
# Start a long-running process
sleep 120

# Press Ctrl+Z to stop it
[1]+  Stopped                 sleep 120

# Resume in background
bg
[1]+ sleep 120 &

# Terminal is now free for other work
```

The `&` symbol indicates the process is running in the background.

### fg (Foreground) Command

**Full Form:** Foreground

**Purpose:** Bring a background or stopped job back to the foreground.

**Syntax:**
```bash
fg
```

Or for a specific job:
```bash
fg %job_id
```

### Example: Foreground Execution

```bash
# After running a job in background
jobs
[1]+  Running                 sleep 120 &

# Bring it to foreground
fg
sleep 120
```

## Managing Multiple Jobs

When you have multiple jobs, you can specify which one to manage using the job ID.

### Example: Multiple Jobs

```bash
# Create multiple jobs
sleep 200 &
[1] 12345

./script.sh
# Press Ctrl+Z
[2]+  Stopped                 ./script.sh

sleep 300
# Press Ctrl+Z
[3]+  Stopped                 sleep 300

# View all jobs
jobs
[1]   Running                 sleep 200 &
[2]-  Stopped                 ./script.sh
[3]+  Stopped                 sleep 300
```

### Resume Specific Jobs

**Foreground:**
```bash
fg %2        # Brings job 2 (script.sh) to foreground
```

**Background:**
```bash
bg %3        # Runs job 3 (sleep 300) in background
```

## Process Priority with nice

### What is Nice Value?

The **nice value** determines the priority of a process. It ranges from **-20 to +19**.

- **-20** = Highest priority (most important, like VIP)
- **0** = Normal priority (moderate)
- **+19** = Lowest priority (least important)

### Understanding Priority

Think of it like airline boarding:
- **Business class (-20)** = Highest priority, boards first
- **Economy class (0)** = Normal priority
- **Standby (+19)** = Lowest priority, boards last

Similarly, processes with lower nice values get more CPU time and resources from the server.

### Checking Nice Value

**Using ps command:**
```bash
# Find process ID
ps aux | grep script_name

# Check nice value
ps -l PID
```

Or use specific format:
```bash
ps -o pid,ni,cmd -p PID
```

Example:
```bash
./script.sh &
ps aux | grep script.sh
# Output: user  12345  ...

ps -l 12345
# Shows nice value: 0 (default)
```

### Setting Nice Value

**Syntax:**
```bash
nice -n <value> <command>
```

Where:
- `-n` specifies the nice value
- `<value>` ranges from -20 to +19
- `<command>` is the process to run

### Examples

**Normal priority (default 0):**
```bash
./script.sh
```

**Higher priority (-10):**
```bash
nice -n -10 ./script.sh
```

**Lower priority (+10):**
```bash
nice -n 10 ./script.sh
```

**Verification:**
```bash
# Start process with custom nice value
nice -n -10 ./script.sh &
[1] 12345

# Get process ID
ps aux | grep script.sh

# Check nice value
ps -l 12345
# Output shows NI: -10
```

### Important Notes

- Only **root user** can set negative nice values (higher priority)
- Regular users can only set positive values (lower priority)
- You need **sudo privileges** to increase process priority

## Running Processes with nohup

### The Problem

When you close a terminal, all jobs started in that terminal are terminated. This is problematic for long-running scripts or maintenance tasks.

### The Solution: nohup

**nohup** (No Hang Up) allows processes to continue running even after you close the terminal.

**Syntax:**
```bash
nohup command &
```

The `&` at the end runs it in the background.

### Example: Terminal-Dependent Process

**Without nohup:**
```bash
# Terminal 1
./script.sh
# Press Ctrl+Z
[1]+  Stopped                 ./script.sh

# Check process
ps aux | grep script.sh
# Shows running

# Close Terminal 1

# Terminal 2
ps aux | grep script.sh
# Process is GONE - script terminated!
```

### Example: Terminal-Independent Process

**With nohup:**
```bash
# Terminal 1
nohup ./script.sh &
[1] 12345
nohup: ignoring input and appending output to 'nohup.out'

# Check process
ps aux | grep script.sh
# Shows running

# Close Terminal 1

# Terminal 2
ps aux | grep script.sh
# Process STILL RUNNING - script continues!
```

### nohup Output File

By default, `nohup` creates a file named **nohup.out** in the current directory.

**What's in nohup.out?**
- All output from your script (stdout)
- All errors (stderr)
- Essentially, a log file of your script's activity

**View the log:**
```bash
cat nohup.out
# Or
tail -f nohup.out    # Real-time monitoring
```

### Redirecting nohup Output

You can redirect output to a custom file:

**Syntax:**
```bash
nohup command > custom_output.log 2>&1 &
```

**Example:**
```bash
nohup ./backup_script.sh > backup.log 2>&1 &
```

This redirects:
- Standard output (stdout) to `backup.log`
- Standard error (stderr) to `backup.log`

### Suppressing Output Completely

If you don't want any output:

```bash
nohup command > /dev/null 2>&1 &
```

## Practical Use Cases

### Use Case 1: Long-Running Backup Script

```bash
nohup ./backup_database.sh > backup.log 2>&1 &
```

Benefits:
- Continues even if SSH session drops
- Logs all output for later review
- Frees up terminal for other work

### Use Case 2: Running Multiple Priority Tasks

```bash
# High priority task
nice -n -10 ./critical_process.sh &

# Normal priority task
./regular_process.sh &

# Low priority task
nice -n 15 ./background_cleanup.sh &
```

### Use Case 3: Starting Services That Must Persist

```bash
nohup python3 web_server.py > server.log 2>&1 &
```

## Practice Tasks

### Task 1: Job Management
1. Start a sleep command: `sleep 200`
2. Stop it with `Ctrl+Z`
3. View jobs with `jobs`
4. Resume in background with `bg`
5. Check it's running with `jobs`

### Task 2: Multiple Jobs
1. Create 3 different jobs
2. List all jobs
3. Bring job 2 to foreground
4. Send job 3 to background

### Task 3: Nice Values
1. Start a script with default nice value
2. Check its nice value
3. Kill the process
4. Restart with nice value -5
5. Verify the new nice value

### Task 4: nohup Testing
1. Start a script with nohup
2. Close the terminal
3. Open a new terminal
4. Verify the script is still running
5. Check the nohup.out file

### Task 5: Combined Usage
1. Start a process with custom nice value and nohup
2. Verify it's running
3. Close terminal
4. Confirm it's still running with modified priority

## Command Summary

| Command | Purpose | Example |
|---------|---------|---------|
| `jobs` | List active jobs | `jobs` |
| `bg` | Run job in background | `bg %1` |
| `fg` | Bring job to foreground | `fg %2` |
| `nice` | Set process priority | `nice -n -10 command` |
| `nohup` | Run process independent of terminal | `nohup command &` |
| `Ctrl+Z` | Stop (suspend) current job | Press `Ctrl+Z` |
| `ps` | View process information | `ps aux | grep script` |

## Best Practices

1. **Always use `&` with nohup** - Runs process in background immediately
2. **Redirect nohup output** - Prevents cluttering nohup.out file
3. **Use nice values wisely** - Don't set everything to highest priority
4. **Monitor long-running processes** - Check logs regularly
5. **Document job IDs** - Keep track of important background jobs
6. **Use descriptive filenames** - For nohup output files

## Troubleshooting

### Problem: Job disappeared after closing terminal
**Solution:** Use `nohup` next time

### Problem: Cannot set negative nice value
**Solution:** Use `sudo` or run as root user

### Problem: nohup.out file too large
**Solution:** Redirect to /dev/null or rotate logs regularly

### Problem: Lost track of background jobs
**Solution:** Use `ps aux` to find all processes

## Homework Exercise

**Challenge:** Start a process using nohup with output redirection to /dev/null

```bash
nohup ./script.sh > /dev/null 2>&1 &
```

**Observe:**
- No nohup.out file is created
- All output is discarded
- Process runs silently in background
- Terminal closes without affecting process

Try this and understand the difference between:
- `nohup command &` (creates nohup.out)
- `nohup command > /dev/null 2>&1 &` (no output file)

## Conclusion

Process management is crucial for efficient Linux system administration. These five commands give you complete control over:
- Job lifecycle management
- Process prioritization
- Background execution
- Terminal-independent processing

Master these commands to become more productive with Linux!

---

**Note:** If you found this guide helpful, please like and subscribe to the channel!