# Linux grep Command - Complete Guide

## Overview
This comprehensive guide covers the `grep` command with 15+ practical use cases in Q&A format, perfect for interviews, exams, and day-to-day tasks.

## What is grep?

**Full Form:** **G**lobal **R**egular **E**xpression **P**rint

**Purpose:** Search for patterns or words in files, similar to Ctrl+F in a text editor.

### Basic Syntax
```bash
grep [options] pattern filename
```

### How grep Works
When you search for a pattern, grep prints the entire line containing that pattern.

**Example:**
```bash
grep "kara" users.csv
```
This prints all lines containing "kara" from the users.csv file.

## 15+ Practical Use Cases

### Use Case 1: Ignore Case Sensitivity

**Problem:** By default, grep is case-sensitive.

**Solution:** Use `-i` flag to ignore case

```bash
# Case-sensitive (won't match "Kara")
grep "kara" users.csv

# Case-insensitive (matches "kara", "Kara", "KARA")
grep -i "kara" users.csv
```

**Best Practice:** Always use `-i` for case-insensitive searches, especially for log analysis.

---

### Use Case 2: Search Everything Except Given Pattern

**Task:** Display all data except lines containing a specific keyword

**Solution:** Use `-v` flag (invert match)

```bash
# Show everything except lines containing "kara"
grep -v "kara" users.csv

# Can be combined with case-insensitive
grep -iv "kara" users.csv

# Multiple exclusions
grep -v "kara" users.csv | grep -v "mary"
```

---

### Use Case 3: Count Occurrences of a Keyword

**Task:** Find how many times a word appears in a file

**Solution:** Use `-c` flag (count)

```bash
# Count how many times "doctor" appears
grep -c "doctor" users.csv
# Output: 15 (means 15 users are doctors)
```

**Use Case:** Quickly count specific entries without viewing all data.

---

### Use Case 4: Search for Exact Match

**Task:** Find exact word match, not partial matches

**Solution:** Use `-w` flag (word match)

```bash
# Without -w: matches "kar", "kara", "karla"
grep "kar" users.csv

# With -w: only exact "kar"
grep -w "kar" users.csv

# Combined with case-insensitive
grep -iw "karlie" users.csv
```

---

### Use Case 5: Print Line Numbers

**Task:** Display line numbers where matches occur

**Solution:** Use `-n` flag (line number)

```bash
# Show line number with match
grep -n "karlie" users.csv
# Output: 100:14,karlie,doe,doctor,karlie@example.com

# Verify the line number
sed -n '100p' users.csv
```

**Use Case:** Useful for automation, scripting, and precise data location.

---

### Use Case 6: Search in Multiple Files

**Task:** Search for a keyword across multiple files

**Solution:** Provide multiple filenames

```bash
# Search in multiple specific files
grep "mary" users.csv file1.txt

# Output shows filename and matched line
users.csv:10,mary,smith,engineer,mary@example.com
file1.txt:mary is a doctor
```

**Note:** grep automatically displays filenames when searching multiple files.

---

### Use Case 7: Suppress Filenames in Output

**Task:** Hide filenames when searching multiple files

**Solution:** Use `-h` flag (no filename)

```bash
# Normal output (with filenames)
grep "mary" users.csv file1.txt

# Suppressed filenames
grep -h "mary" users.csv file1.txt
```

**Use Case:** Cleaner output for scripting and data processing.

---

### Use Case 8: Search Multiple Keywords

**Method 1:** Use `-e` flag for each keyword

```bash
grep -e "mary" -e "kara" -e "karlie" users.csv
```

**Method 2:** Use `egrep` with pipe separator (cleaner syntax)

```bash
egrep "kara|mary|karlie" users.csv
```

Both methods achieve the same result.

---

### Use Case 9: Search Multiple Keywords in Multiple Files

**Syntax:** Combine multiple keywords and filenames

```bash
egrep "kara|mary" users.csv file1.txt
```

**Output:** Shows filename, matched keyword, and line content.

---

### Use Case 10: Print Only Filenames

**Task:** Display only filenames containing the match, not the content

**Solution:** Use `-l` flag (list filenames)

```bash
# Shows filenames only
grep -l "mary" users.csv file1.txt file2.txt

# Output:
users.csv
file1.txt
```

**Use Case:** Quickly identify which files contain specific data.

---

### Use Case 11: Search Using Keywords from a File

**Problem:** Need to search for 100+ keywords manually

**Solution:** Use `-f` flag to read keywords from a file

**Setup:**
```bash
# keywords.txt contains:
karlie
raju
```

**Command:**
```bash
grep -f keywords.txt users.csv
```

**Advanced:** Search in all files in current directory

```bash
grep -f keywords.txt *
```

**Note:** This will also read the keywords file itself. Use specific patterns to avoid.

---

### Use Case 12: Match Lines Starting with Pattern

**Task:** Find lines that start with a specific pattern

**Solution:** Use `^` (caret) symbol

```bash
# Find all lines starting with "100"
grep "^100" users.csv

# Shows all users with IDs starting with 100
```

**Explanation:** `^` denotes "starts with" in regular expressions.

---

### Use Case 13: Match Lines Ending with Pattern

**Task:** Find lines that end with a specific pattern

**Solution:** Use `$` (dollar) symbol

```bash
# Find lines ending with "er"
grep "er$" users.csv

# Find lines ending with "per" (developer, helper)
grep "per$" users.csv
```

**Example Output:** Lines ending with "officer", "fighter", "developer"

---

### Use Case 14: Recursive Search in Directories

**Task:** Search inside a directory and all its files

**Solution:** Use `-r` or `-R` flag (recursive)

**Method 1:** Use `-R` flag
```bash
grep -R "raju" grep_directory/
```

**Method 2:** Use wildcard
```bash
grep "raju" grep_directory/*
```

**Output:** Shows filename and matched content from all files in directory.

---

### Use Case 15: Quiet Mode for Conditional Checks

**Task:** Check if pattern exists without printing output (for scripting)

**Solution:** Use `-q` flag (quiet mode)

```bash
# Normal search (prints output)
grep "kara" users.csv

# Quiet mode (no output)
grep -q "kara" users.csv

# Check exit status
echo $?
# Output: 0 (success - pattern found)

# Non-matching pattern
grep -q "randomname" users.csv
echo $?
# Output: 1 (failure - pattern not found)
```

**Use Case:** Perfect for conditional statements in shell scripts:

```bash
if grep -q "kara" users.csv; then
    echo "User found"
else
    echo "User not found"
fi
```

---

## Bonus: Search Command Output

**Use Case:** Filter command output using grep with pipes

### Example: Find Specific Files

```bash
# List all files and filter
ls | grep "file"

# Output: Only files with "file" in their name
file1.txt
file.txt
```

### Example: Filter Process Information

```bash
# Find specific process
ps aux | grep "nginx"

# Find network connections
netstat -an | grep "ESTABLISHED"
```

---

## Quick Reference Table

| Flag | Purpose | Example |
|------|---------|---------|
| `-i` | Ignore case | `grep -i "pattern" file` |
| `-v` | Invert match (exclude) | `grep -v "pattern" file` |
| `-c` | Count matches | `grep -c "pattern" file` |
| `-w` | Exact word match | `grep -w "word" file` |
| `-n` | Show line numbers | `grep -n "pattern" file` |
| `-h` | Hide filenames | `grep -h "pattern" files*` |
| `-l` | List filenames only | `grep -l "pattern" files*` |
| `-e` | Multiple patterns | `grep -e "p1" -e "p2" file` |
| `-f` | Read patterns from file | `grep -f keywords.txt file` |
| `-r/-R` | Recursive search | `grep -R "pattern" directory/` |
| `-q` | Quiet mode | `grep -q "pattern" file` |

---

## Regular Expression Symbols

| Symbol | Meaning | Example |
|--------|---------|---------|
| `^` | Start of line | `grep "^100" file` |
| `$` | End of line | `grep "er$" file` |
| `|` | OR operator (egrep) | `egrep "word1\|word2" file` |

---

## Common Command Combinations

```bash
# Case-insensitive search with line numbers
grep -in "pattern" file

# Count case-insensitive matches
grep -ic "pattern" file

# Exact word match, case-insensitive
grep -iw "word" file

# Recursive search, case-insensitive
grep -Ri "pattern" directory/

# Search multiple patterns with line numbers
egrep -n "pattern1|pattern2" file

# Quiet check in script
if grep -q "error" logfile; then
    # Handle error
fi
```

---

## Best Practices

1. **Always use `-i`** for case-insensitive searches (especially in log analysis)
2. **Use `-w`** when searching for exact words to avoid partial matches
3. **Use `-q`** in scripts for conditional checks (faster, cleaner)
4. **Use `egrep`** for multiple patterns (cleaner than multiple `-e` flags)
5. **Combine flags** for powerful searches: `-in`, `-iw`, `-Ri`
6. **Use `-f`** when searching for many keywords (100+ terms)
7. **Pipe with other commands** for advanced filtering

---

## Real-World Examples

### Log Analysis
```bash
# Find errors in log file
grep -i "error" /var/log/syslog

# Count critical errors
grep -ic "critical" /var/log/app.log

# Find errors excluding warnings
grep -i "error" logfile | grep -v "warning"
```

### User Management
```bash
# Find specific user
grep "username" /etc/passwd

# Count active users
grep -c "/bin/bash" /etc/passwd
```

### Configuration Files
```bash
# Find uncommented lines in config
grep -v "^#" /etc/config.conf

# Search all config files
grep -R "database" /etc/myapp/
```

### Development
```bash
# Find TODO comments
grep -Rn "TODO" src/

# Find function definitions
grep -w "function_name" *.py
```

---

## Summary

The `grep` command is one of the most powerful text search tools in Linux. Key takeaways:

- **Flexible searching** with regular expressions
- **Multiple options** for different use cases
- **Scriptable** with quiet mode and exit status
- **Combinable** with other commands via pipes
- **Essential skill** for DevOps, system administration, and development

Master these use cases, and you'll significantly improve your command-line efficiency!