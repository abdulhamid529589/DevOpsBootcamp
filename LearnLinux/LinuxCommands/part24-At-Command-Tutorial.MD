# At Command Tutorial Guide

## Table of Contents
- [Introduction](#introduction)
- [What is the At Command?](#what-is-the-at-command)
- [Use Cases](#use-cases)
- [Basic Commands](#basic-commands)
- [Time Format Specifications](#time-format-specifications)
- [Practical Examples](#practical-examples)
- [Managing Scheduled Jobs](#managing-scheduled-jobs)
- [Key Differences: At vs Cron](#key-differences-at-vs-cron)

## Introduction

The `at` command is a powerful Linux utility for scheduling one-time tasks to execute at a specific time in the future. Unlike cron jobs that run repeatedly, `at` is designed for tasks that need to run only once.

## What is the At Command?

The `at` command allows you to schedule commands or scripts to execute at a specified time in the future. The service that handles these scheduled tasks is called `atd` (at daemon), which runs as a background process on your system.

### Check At Service Status

```bash
# Check if atd service is running
systemctl status atd
```

The service should show as active and running in the background.

## Use Cases

The `at` command is perfect for:

- **One-time scheduled tasks**: Execute a command or script once at a specific future time
- **Meeting reminders**: Send notifications or emails before meetings
- **Scheduled reports**: Generate and send reports at a specific date/time
- **Future maintenance**: Schedule system maintenance tasks for off-peak hours
- **Delayed actions**: Execute tasks after a specific delay (e.g., "in 2 hours")

**Key Point**: Unlike cron jobs that run repeatedly on a schedule, `at` jobs execute only once and are then removed from the queue.

## Basic Commands

### Schedule a Job

```bash
# Basic syntax
at <time_specification>
at> <command_to_execute>
at> <Ctrl+D to save>

# Example
at 16:30
at> echo "Hello" > /tmp/test.txt
at> <Ctrl+D>
```

### View Scheduled Jobs

```bash
# List all pending at jobs
atq

# Alternative command
at -l
```

### Remove a Scheduled Job

```bash
# Remove job by job number
atrm <job_number>

# Alternative command
at -d <job_number>

# Example
atrm 1
```

## Time Format Specifications

The `at` command supports flexible time specifications:

### Specific Time Formats

```bash
# Specific time today (24-hour format)
at 16:17
at 14:30
at 09:00

# Specific time with AM/PM
at 4pm
at 9am
at 10:30pm

# Specific date and time
at 10:30 December 10
at 4pm November 30
at 9am Monday
at 10am Wednesday
```

### Relative Time Formats

```bash
# Minutes from now
at now + 5 minutes
at now + 30 minutes

# Hours from now
at now + 2 hours
at now + 5 hours

# Days from now
at now + 1 day
at now + 3 days

# Specific day
at monday
at wednesday
at tomorrow
at next month
```

## Practical Examples

### Example 1: Simple Echo Command

Schedule a task to run at 4:30 PM:

```bash
at 16:30
at> echo "Hello" > /tmp/test.txt
at> <Ctrl+D>

# Output shows:
# warning: commands will be executed using /bin/sh
# job 1 at Thu Jan 29 16:30:00 2026
```

### Example 2: Execute a Script

Let's create and schedule a script:

**Step 1: Create a test script**

```bash
# Create test.sh
cat > test.sh << 'EOF'
#!/bin/bash
touch /tmp/script_executed.txt
echo "Script ran at $(date)" > /tmp/script_executed.txt
EOF

# Make it executable
chmod +x test.sh
```

**Step 2: Schedule the script**

```bash
at 16:17
at> /path/to/test.sh
at> <Ctrl+D>
```

**Step 3: Verify the job**

```bash
atq
# Output example:
# 1    Thu Jan 29 16:17:00 2026 a paul
```

### Example 3: Multiple Commands

You can schedule multiple commands in a single job:

```bash
at 14:00
at> echo "Task 1" > /tmp/task1.txt
at> echo "Task 2" > /tmp/task2.txt
at> date >> /tmp/execution_time.txt
at> <Ctrl+D>
```

### Example 4: Schedule for Tomorrow

```bash
at 9am tomorrow
at> /usr/local/bin/backup.sh
at> <Ctrl+D>
```

### Example 5: Schedule for Next Week

```bash
at 10am next monday
at> /usr/local/bin/weekly_report.sh
at> <Ctrl+D>
```

### Example 6: Schedule Using Relative Time

```bash
# Run in 5 minutes
at now + 5 minutes
at> echo "5 minutes passed" > /tmp/reminder.txt
at> <Ctrl+D>

# Run in 2 hours
at now + 2 hours
at> /path/to/maintenance.sh
at> <Ctrl+D>
```

## Managing Scheduled Jobs

### List All Pending Jobs

```bash
atq
```

**Output format**:
```
Job#  Date                    Time      Queue  User
1     Thu Jan 29 16:30:00 2026 a      paul
2     Fri Jan 30 09:00:00 2026 a      paul
```

The output shows:
- **Job number**: Unique identifier for the job
- **Date and time**: When the job will execute
- **Queue**: Job queue (usually 'a')
- **User**: User who created the job

### View Job Details

```bash
# View the commands in a specific job
at -c <job_number>

# Example
at -c 1
```

### Remove a Scheduled Job

```bash
# Remove job number 1
atrm 1

# Verify removal
atq
# Job should no longer appear in the list
```

### Remove All Jobs

```bash
# Remove all jobs for current user
for job in $(atq | awk '{print $1}'); do atrm $job; done
```

## Key Differences: At vs Cron

| Feature | At Command | Cron Jobs |
|---------|-----------|-----------|
| **Frequency** | One-time execution | Repeated execution |
| **Use Case** | Future one-off tasks | Regular scheduled tasks |
| **Persistence** | Job deleted after execution | Job persists indefinitely |
| **Syntax** | Interactive or file-based | Crontab format (5 fields) |
| **Service** | `atd` daemon | `crond` or `cron` daemon |
| **Job Management** | `atq`, `atrm` | `crontab -l`, `crontab -r` |

### When to Use At

- ✓ One-time tasks
- ✓ Specific future date/time
- ✓ Ad-hoc scheduling
- ✓ Tasks that don't need to repeat

### When to Use Cron

- ✓ Recurring tasks
- ✓ Regular intervals (daily, weekly, monthly)
- ✓ Ongoing automation
- ✓ System maintenance tasks

## Time Specification Reference

### Absolute Time

```bash
at 16:30              # Today at 4:30 PM
at 4pm                # Today at 4:00 PM
at 9am tomorrow       # Tomorrow at 9:00 AM
at 10:30 December 10  # Specific date and time
at midnight           # Today at midnight (00:00)
at noon               # Today at noon (12:00)
```

### Relative Time

```bash
at now + 5 minutes
at now + 2 hours
at now + 1 day
at now + 1 week
at now + 1 month
```

### Day-Specific

```bash
at monday
at tuesday
at next monday
at next week
```

## Best Practices

1. **Use absolute paths**: Always use full paths for scripts and files
   ```bash
   at 16:30
   at> /usr/local/bin/script.sh
   ```

2. **Set environment variables**: If needed, set them in your command
   ```bash
   at 14:00
   at> export PATH=/usr/bin:/usr/local/bin
   at> /path/to/script.sh
   ```

3. **Redirect output for logging**:
   ```bash
   at 16:30
   at> /path/to/script.sh >> /var/log/at_job.log 2>&1
   ```

4. **Test your commands first**: Always test commands manually before scheduling
   ```bash
   # Test first
   /path/to/script.sh
   
   # Then schedule
   at 16:30
   at> /path/to/script.sh
   ```

5. **Verify job creation**: Always check that your job was scheduled correctly
   ```bash
   atq
   ```

## Troubleshooting

### Job Not Executing

**Check service status**:
```bash
systemctl status atd
```

**Verify job is scheduled**:
```bash
atq
```

**Check for errors**:
```bash
# Check system logs
journalctl -u atd
```

### Permission Issues

```bash
# Ensure script is executable
chmod +x /path/to/script.sh

# Check script ownership
ls -l /path/to/script.sh
```

### At Command Not Found

```bash
# Install at package on Ubuntu/Debian
sudo apt-get install at

# Install at package on CentOS/RHEL
sudo yum install at

# Start and enable the service
sudo systemctl start atd
sudo systemctl enable atd
```

## Quick Reference

```bash
# Schedule a job
at <time>
at> <command>
at> <Ctrl+D>

# List pending jobs
atq

# Remove a job
atrm <job_number>

# View job details
at -c <job_number>

# Check service status
systemctl status atd
```

## Common Time Format Examples

```bash
# Today at specific time
at 16:30
at 4pm

# Tomorrow
at 9am tomorrow
at tomorrow

# Next week
at monday
at next monday

# Relative time
at now + 30 minutes
at now + 2 hours
at now + 1 day

# Specific date
at 10:30 December 10
at 4pm November 30
```

---

**Remember**: The `at` command is perfect for one-time scheduled tasks. For recurring tasks, use cron jobs instead. Always verify your jobs with `atq` after scheduling and test commands manually before scheduling them.