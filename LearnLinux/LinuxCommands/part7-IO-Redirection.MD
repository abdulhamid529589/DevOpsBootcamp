# Linux I/O Redirection Guide

## Overview
I/O Redirection allows you to control where input comes from and where output goes in Linux. Instead of displaying output on the terminal, you can redirect it to files, and vice versa.

## Why Use Redirection?

### Real-World Use Cases

1. **Combine Multiple Files**
   - Merge multiple data files into one
   - Consolidate logs from different sources

2. **Split Large Files**
   - Extract relevant data from large log files (GB-sized files with millions of lines)
   - Filter specific information (e.g., extract only "doctor" records from a user database)
   - Make analysis easier by working with smaller, focused datasets

3. **Error Logging**
   - Capture errors separately from normal output
   - Debug scripts and applications

## Types of Redirection

| Type | File Descriptor | Symbol | Description |
|------|----------------|--------|-------------|
| Standard Input (STDIN) | 0 | `<` | Input from keyboard/file |
| Standard Output (STDOUT) | 1 | `>` or `>>` | Normal command output |
| Standard Error (STDERR) | 2 | `2>` or `2>>` | Error messages |

## File Descriptors Explained

```
┌─────────────┐
│   Process   │
│   (Shell)   │
└──────┬──────┘
       │
       ├──► 0 (STDIN)  ─── Input from keyboard/file
       ├──► 1 (STDOUT) ─── Normal output to terminal/file
       └──► 2 (STDERR) ─── Error output to terminal/file
```

## Basic Redirection Operators

### 1. Output Redirection (`>`)
Redirects output to a file (overwrites existing content)

```bash
# Redirect command output to file
ls > file.txt

# Overwrites previous content
echo "Hello" > myfile.txt
cat myfile.txt  # Shows: Hello

echo "World" > myfile.txt
cat myfile.txt  # Shows: World (Hello is gone)
```

### 2. Append Redirection (`>>`)
Appends output to a file (preserves existing content)

```bash
# Add content without overwriting
echo "Hello" > myfile.txt
echo "Buddy" >> myfile.txt
cat myfile.txt
# Output:
# Hello
# Buddy
```

### 3. Input Redirection (`<`)
Takes input from a file instead of keyboard

```bash
# Read from file as input
cat < data1.txt

# Equivalent to
cat data1.txt
```

## Practical Examples

### Example 1: Combine Multiple Files

```bash
# Combine data1.txt and data2.txt into data3.txt
cat data1.txt data2.txt > data3.txt

# Verify the result
cat data3.txt
```

### Example 2: Extract Specific Data from Large File

```bash
# Original file: users.csv (contains many professions)
# Extract only "doctor" records
grep "doctor" users.csv > doctor_data.txt

# Now work with smaller, relevant file
less doctor_data.txt
```

This approach is especially useful for:
- Analyzing large log files
- Filtering specific error types
- Extracting data for specific time periods

### Example 3: Redirect Errors Only (`2>`)

```bash
# Try to access restricted directory (will cause error)
cd /root 2> error.txt

# Error is saved to file, not displayed on terminal
cat error.txt
# Output: Permission denied
```

**Important**: Using `2>` redirects ONLY errors, not normal output

```bash
# This will still print to terminal (not an error)
ls 2>> error.txt  # Output appears on screen

# This will save to file (is an error)
cd /root 2>> error.txt  # Error saved to file
```

### Example 4: Redirect Both Output and Errors (`&>`)

```bash
# Redirect both STDOUT and STDERR to same file
ls &> output.txt

# Try with error-causing command
ls /nonexistent &> output.txt

# Check the file
cat output.txt
# Contains both normal output and error messages
```

### Example 5: Separate Output and Error Streams

```bash
# Save output to one file, errors to another
command > output.txt 2> error.txt

# Example
ls /home /root > output.txt 2> error.txt
```

## Quick Reference Table

| Command | Description | Overwrites? |
|---------|-------------|-------------|
| `cmd > file` | Redirect STDOUT to file | Yes |
| `cmd >> file` | Append STDOUT to file | No |
| `cmd 2> file` | Redirect STDERR to file | Yes |
| `cmd 2>> file` | Append STDERR to file | No |
| `cmd &> file` | Redirect both STDOUT and STDERR | Yes |
| `cmd &>> file` | Append both STDOUT and STDERR | No |
| `cmd < file` | Take input from file | N/A |
| `cmd > file 2>&1` | Redirect STDERR to STDOUT, then to file | Yes |

## Common Patterns

### 1. Discard Output (Send to /dev/null)
```bash
# Suppress all output
command > /dev/null 2>&1

# Suppress only errors
command 2> /dev/null
```

### 2. Save Both Success and Error Logs
```bash
# Production script logging
./my_script.sh > success.log 2> error.log
```

### 3. Append to Log File
```bash
# Daily log appending
date >> system.log
df -h >> system.log
```

### 4. Create or Write to File Using Echo
```bash
# Create new file with content
echo "Hello" > myfile.txt

# Add more content
echo "Buddy" >> myfile.txt
```

## File Descriptor Numbers

- **0** = Standard Input (STDIN) - Default: Keyboard
- **1** = Standard Output (STDOUT) - Default: Terminal
- **2** = Standard Error (STDERR) - Default: Terminal

### Examples with File Descriptors

```bash
# Explicit STDOUT redirection (same as >)
ls 1> output.txt

# Redirect STDERR to STDOUT, then both to file
command > file 2>&1

# Modern syntax (recommended)
command &> file
```

## Best Practices

1. **Always check before overwriting**: Use `>>` if you want to preserve existing data
2. **Separate logs**: Keep error logs separate from output logs for easier debugging
3. **Use meaningful filenames**: `error.log`, `output.log` instead of generic names
4. **Test with small data first**: Before processing large files, test your commands
5. **Check file contents**: Use `cat`, `less`, or `tail` to verify redirected output

## Common Use Cases in Production

### System Administration
```bash
# Capture system information
uname -a > system_info.txt
df -h >> system_info.txt
free -m >> system_info.txt

# Monitor processes
ps aux > running_processes.txt
```

### Log Analysis
```bash
# Extract errors from log file
grep "ERROR" /var/log/app.log > errors_only.log

# Count specific error types
grep "Connection timeout" /var/log/app.log | wc -l > timeout_count.txt
```

### Backup and Data Processing
```bash
# Combine multiple config files
cat config1.conf config2.conf > combined_config.conf

# Filter and save specific data
awk '/pattern/' large_file.txt > filtered_data.txt
```

## Summary

Redirection is a powerful feature that allows you to:
- **Control output**: Save command results to files instead of terminal
- **Manage errors**: Separate error messages from normal output
- **Process data**: Combine, filter, and split files efficiently
- **Automate tasks**: Create logs and reports without manual intervention

**Key Takeaway**: Master these redirection operators to work more efficiently with files and command outputs in Linux!