# grep Command Variants - Complete Guide

## Overview
This guide covers different variants of the grep command family: `grep`, `egrep`, `fgrep`, `pgrep`, `zgrep`, and `pdfgrep`. Each variant is designed for specific use cases.

---

## 1. grep - Basic Text Search

### Purpose
Search for patterns or words in regular text files.

### Basic Usage
```bash
# Search for a single word
grep "sara" users.csv

# Search for another user
grep "lucy" users.csv
```

### How it Works
- Searches for the specified pattern in the file
- Prints entire lines containing the match
- Case-sensitive by default

### Example Output
```
23,sara,johnson,engineer,sara@example.com
45,lucy,smith,doctor,lucy@example.com
```

---

## 2. egrep - Extended grep (Multiple Patterns)

### Purpose
Search for multiple patterns/words simultaneously using extended regular expressions.

### Syntax
```bash
egrep "pattern1|pattern2|pattern3" filename
```

### Usage Examples

**Single Pattern:**
```bash
grep "sara" users.csv
```

**Multiple Patterns:**
```bash
# Search for three names at once
egrep "sara|lucy|candy" users.csv
```

### Output
```
23,sara,johnson,engineer,sara@example.com
34,candy,brown,designer,candy@example.com
45,lucy,smith,doctor,lucy@example.com
```

### Key Features
- Use pipe (`|`) to separate multiple search terms
- Returns all lines matching ANY of the patterns
- More efficient than running grep multiple times

### Alternative Method
```bash
# Using grep with -e flag (equivalent to egrep)
grep -e "sara" -e "lucy" -e "candy" users.csv
```

---

## 3. pgrep - Process grep

### Purpose
Search for active processes and retrieve their Process IDs (PIDs).

### Traditional Method
```bash
# Find nginx process using ps and grep
ps -ef | grep nginx

# Output shows full process details:
user  1238  1  0 10:23 ?  00:00:00 nginx: master process
user  1240  1238  0 10:23 ?  00:00:00 nginx: worker process
```

### Using pgrep
```bash
# Get only the Process ID
pgrep nginx

# Output (just PIDs):
1238
1240
```

### Comparison

| Method | Output | Use Case |
|--------|--------|----------|
| `ps -ef \| grep nginx` | Full process details | When you need complete info |
| `pgrep nginx` | Just PIDs | When you only need PIDs |

### Use Cases
- **Scripting:** Get PID to perform actions on processes
- **Automation:** Kill specific processes automatically
- **Monitoring:** Check if a service is running
- **Service Management:** Restart or stop services by PID

### Practical Examples
```bash
# Kill a process by name
kill $(pgrep nginx)

# Check if service is running
if pgrep apache2 > /dev/null; then
    echo "Apache is running"
fi

# Get PID for further operations
PID=$(pgrep mysql)
echo "MySQL PID: $PID"
```

---

## 4. fgrep - Fixed String grep (Literal Search)

### Purpose
Search for **exact literal strings** without treating special characters as regular expressions.

### The Problem with Regular grep

**File Content (example.txt):**
```
hello.buddy
hello-buddy
hello buddy
helloXbuddy
```

**Using grep:**
```bash
grep "hello.buddy" example.txt
```

**Incorrect Output:**
```
hello.buddy
hello-buddy
hello buddy
helloXbuddy
```

**Why?** The dot (`.`) in grep means "any single character" in regular expressions, so it matches all variations.

### The Solution: fgrep

```bash
# Search for exact literal string
fgrep "hello.buddy" example.txt
```

**Correct Output:**
```
hello.buddy
```

### Another Example - Asterisk (*)

**File Content:**
```
hello buddy
hellooo buddy
hello*buddy
```

**Using grep (wrong):**
```bash
grep "hello*buddy" example.txt

# Output: Matches "hello" and "hellooo" (asterisk means "zero or more 'o'")
hello buddy
hellooo buddy
```

**Using fgrep (correct):**
```bash
fgrep "hello*buddy" example.txt

# Output: Only exact match
hello*buddy
```

### Alternative Method
```bash
# Using grep with -F flag (equivalent to fgrep)
grep -F "hello.buddy" example.txt
```

### When to Use fgrep
- Searching for strings with **special characters**: `.`, `*`, `?`, `[`, `]`, `^`, `$`, `\`
- Need **exact/literal matches**
- Working with **configuration files** or **code** containing special characters
- **File paths** with dots and slashes
- **Email addresses** or **URLs**

### Special Characters that Need fgrep

| Character | Meaning in grep | Example |
|-----------|----------------|---------|
| `.` | Any single character | `a.b` matches `aab`, `abb`, `acb` |
| `*` | Zero or more of previous | `ab*` matches `a`, `ab`, `abb` |
| `?` | Zero or one of previous | `ab?` matches `a`, `ab` |
| `[...]` | Character class | `[abc]` matches `a`, `b`, or `c` |
| `^` | Start of line | `^hello` matches lines starting with "hello" |
| `$` | End of line | `world$` matches lines ending with "world" |

---

## 5. zgrep - Compressed File grep

### Purpose
Search inside **compressed/zipped files** without extracting them.

### The Problem

**Regular File:**
```bash
# Works fine
grep "sara" users.csv
```

**Compressed File:**
```bash
# Try to read compressed file
cat users.csv.gz

# Output: Binary/garbage characters
��^@��^@��^@...

# Try grep on compressed file
grep "sara" users.csv.gz

# Output: No results (can't read compressed data)
```

### The Solution: zgrep

```bash
# Search inside compressed file directly
zgrep "sara" users.csv.gz

# Output: Normal results
23,sara,johnson,engineer,sara@example.com
```

### Comparison

| Command | File Type | Requires Extraction? |
|---------|-----------|---------------------|
| `grep` | Regular files | No |
| `zgrep` | Compressed (.gz) | **No** |

### Use Cases

**1. Production Server Log Analysis**
```bash
# Old logs are often compressed to save space
zgrep "ERROR" /var/log/application.log.gz
```

**2. Archived Data**
```bash
# Search old archived data without unzipping
zgrep "user_id:12345" archive_2023.csv.gz
```

**3. Multiple Compressed Files**
```bash
# Search across multiple compressed logs
zgrep "failed login" /var/log/auth.log*.gz
```

### Benefits
- **No need to unzip** files (saves time and disk space)
- **Work directly** with compressed archives
- **Preserve originals** (don't modify compressed files)
- **Efficient** for large compressed datasets

### Practical Examples

```bash
# Search in compressed system logs
zgrep "error" /var/log/syslog.1.gz

# Count occurrences in compressed file
zgrep -c "warning" app.log.gz

# Case-insensitive search
zgrep -i "exception" debug.log.gz

# Search multiple patterns
zgrep -E "error|warning" system.log.gz
```

---

## 6. pdfgrep - PDF File Search

### Purpose
Search for text patterns inside **PDF files**.

### The Problem with Regular Files

**Try to read PDF:**
```bash
cat dummy.pdf

# Output: Binary/unreadable data
%PDF-1.4
��������...
```

**Try to use less:**
```bash
less dummy.pdf

# Output: Binary file (not readable)
```

**Try regular grep:**
```bash
grep "dummy" dummy.pdf

# Output: No results or binary file warning
```

### The Solution: pdfgrep

```bash
# Search inside PDF file
pdfgrep "dummy" dummy.pdf

# Output: Actual text content from PDF
This is a dummy PDF file for testing purposes.
```

### Installation

**Ubuntu/Debian:**
```bash
sudo apt-get install pdfgrep
```

**RHEL/CentOS:**
```bash
sudo yum install pdfgrep
```

**macOS:**
```bash
brew install pdfgrep
```

### Usage Examples

**Basic Search:**
```bash
pdfgrep "keyword" document.pdf
```

**Case-Insensitive:**
```bash
pdfgrep -i "keyword" document.pdf
```

**Show Page Numbers:**
```bash
pdfgrep -n "keyword" document.pdf

# Output:
3: This line contains the keyword on page 3
7: Another keyword mention on page 7
```

**Count Occurrences:**
```bash
pdfgrep -c "keyword" document.pdf

# Output:
5
```

**Search Multiple PDFs:**
```bash
pdfgrep "keyword" *.pdf

# Output:
file1.pdf: This is keyword in file1
file2.pdf: Another keyword in file2
```

**Recursive Search:**
```bash
pdfgrep -r "keyword" /path/to/pdfs/
```

### Use Cases

1. **Resume/CV Screening**
```bash
# Find candidates with specific skills
pdfgrep -i "python" resumes/*.pdf
```

2. **Legal Document Review**
```bash
# Search for specific clauses
pdfgrep "confidentiality" contracts/*.pdf
```

3. **Research Papers**
```bash
# Find specific topics
pdfgrep -n "machine learning" papers/*.pdf
```

4. **Invoice Processing**
```bash
# Find specific invoice numbers
pdfgrep "INV-2024" invoices/*.pdf
```

---

## Quick Comparison Table

| Command | Purpose | File Type | Special Feature |
|---------|---------|-----------|-----------------|
| `grep` | Basic text search | Regular files | Standard pattern matching |
| `egrep` | Multiple patterns | Regular files | Extended regex, pipe separator |
| `fgrep` | Literal strings | Regular files | No regex interpretation |
| `pgrep` | Process search | Running processes | Returns PIDs only |
| `zgrep` | Compressed files | .gz files | No extraction needed |
| `pdfgrep` | PDF files | .pdf files | Searches PDF content |

---

## Choosing the Right grep Variant

```bash
# Regular text file, single pattern
grep "pattern" file.txt

# Multiple patterns at once
egrep "pattern1|pattern2|pattern3" file.txt

# Special characters (literal search)
fgrep "hello.world" file.txt

# Find process IDs
pgrep nginx

# Compressed files
zgrep "pattern" file.gz

# PDF documents
pdfgrep "pattern" document.pdf
```

---

## Common Flags Across Variants

Most grep variants support common flags:

| Flag | Purpose | Example |
|------|---------|---------|
| `-i` | Ignore case | `zgrep -i "error" file.gz` |
| `-c` | Count matches | `pdfgrep -c "keyword" file.pdf` |
| `-n` | Line numbers | `egrep -n "pattern" file.txt` |
| `-v` | Invert match | `grep -v "exclude" file.txt` |
| `-r` | Recursive | `pdfgrep -r "pattern" directory/` |

---

## Real-World Scenarios

### Scenario 1: Log Analysis on Production
```bash
# Current logs (regular)
grep "ERROR" /var/log/app.log

# Archived logs (compressed)
zgrep "ERROR" /var/log/app.log.*.gz
```

### Scenario 2: Finding Process to Restart
```bash
# Get PID and restart
kill $(pgrep apache2)
systemctl restart apache2
```

### Scenario 3: Configuration Files with Special Characters
```bash
# Wrong: treats . as wildcard
grep "server.domain.com" config.txt

# Correct: literal search
fgrep "server.domain.com" config.txt
```

### Scenario 4: Searching Multiple User Names
```bash
# Inefficient: multiple commands
grep "john" users.txt
grep "jane" users.txt
grep "jack" users.txt

# Efficient: single command
egrep "john|jane|jack" users.txt
```

### Scenario 5: Resume Screening
```bash
# Search for skills in PDF resumes
pdfgrep -i "python" resumes/*.pdf | grep -i "django"
```

---

## Best Practices

1. **Use `egrep`** for multiple patterns (more readable than multiple `-e` flags)
2. **Use `fgrep`** when searching literal strings with special characters
3. **Use `pgrep`** for process management in scripts
4. **Use `zgrep`** for compressed logs (don't waste time/space unzipping)
5. **Use `pdfgrep`** for PDF documents (don't convert to text first)
6. **Combine variants** with pipes for complex searches

---

## Summary

- **grep**: Standard text search tool
- **egrep**: Extended grep for multiple patterns (use `|` separator)
- **fgrep**: Fixed string grep (literal search, no regex)
- **pgrep**: Process grep (find PIDs of running processes)
- **zgrep**: Compressed file grep (search .gz files directly)
- **pdfgrep**: PDF grep (search PDF documents)

Master these variants to handle any search scenario efficiently!