# Linux Wildcards Guide

## Overview
Wildcards are special characters that represent one or more characters in filenames and commands. They help filter and manipulate files efficiently, especially in directories with hundreds or thousands of files.

## Common Wildcards

| Wildcard | Description | Example |
|----------|-------------|---------|
| `*` | Matches any number of characters | `*.txt` matches all text files |
| `?` | Matches a single character | `file?.txt` matches file1.txt, file2.txt |
| `[abc]` | Matches any of the enclosed characters | `[abc]*` matches files starting with a, b, or c |
| `{1..100}` | Matches a range | `file{1..100}` creates/matches file1 to file100 |

## Practical Use Cases

### 1. Find All Files with Specific Extension
```bash
ls *.xml    # List all XML files
ls *.jpg    # List all JPG files
```

### 2. Create Multiple Files at Once
```bash
touch file{1..100}    # Creates file1, file2, ... file100
```

### 3. Delete Multiple Files
```bash
# Always list first to verify
ls file*
# Then delete
rm file*
```

### 4. Match Single Character
```bash
ls ?123    # Matches a123, b123, c123 (first char varies, rest is "123")
```

### 5. Match Exact Number of Characters
```bash
ls ????    # Lists files with exactly 4 characters
ls ?????   # Lists files with exactly 5 characters
```

### 6. Match Files Starting with Specific Letters
```bash
ls [abc]*       # Files starting with a, b, or c
ls [abcd]*      # Files starting with a, b, c, or d
```

### 7. Match Files with Numeric Values
```bash
ls *[0-9]*      # Files containing any numeric value
```

### 8. Match Alphabetic Characters
```bash
ls *[a-z]*      # Files with lowercase letters
ls *[A-Z]*      # Files with uppercase letters
ls [A-Z]*       # Files starting with uppercase letters
```

### 9. Match Specific Pattern Length
```bash
ls test??       # Files starting with "test" followed by exactly 2 characters
# Matches: test01, test99 (total 6 characters)
```

### 10. Match Files with Underscore
```bash
ls *_*          # Files containing at least one underscore
```

### 11. Exclude Files with Specific Letter
```bash
ls | grep -v e  # List files that don't contain letter 'e'
```

### 12. Move Files by Pattern
```bash
# Verify first
ls *.jpg
# Then move
mv *.jpg images/
```

### 13. Delete Files with Specific Pattern
```bash
# List backup files
ls *backup*
# Delete them
rm *backup*
```

## Advanced Patterns

### Character Ranges
- `[0-9]` - Matches numeric values (0 through 9)
- `[a-z]` - Matches lowercase letters
- `[A-Z]` - Matches uppercase letters

### Line Matching (with grep)
- `^` - Matches start of line
  ```bash
  grep "^R" users.csv    # Lines starting with 'R'
  ```
- `$` - Matches end of line
  ```bash
  grep ".com$" users.csv  # Lines ending with '.com'
  ```

### Complex Date Pattern Example
```bash
grep -E "[0-9]{4}-[0-9]{2}-[0-9]{2}" file.txt
# Matches dates in YYYY-MM-DD format
```

## Best Practices

1. **Always preview before deleting**: Use `ls` with your pattern before using `rm`
   ```bash
   ls file*    # Preview
   rm file*    # Delete after verification
   ```

2. **Combine wildcards with various commands**:
   - `ls` - List files
   - `rm` - Remove files
   - `mv` - Move files
   - `cp` - Copy files

3. **Use ranges efficiently**: Instead of typing file1, file2, file3... use `file{1..100}`

4. **Be specific**: More specific patterns prevent accidental matches

## Common Scenarios

### Working with Servers
When dealing with thousands of files on servers, wildcards make filtering much easier:
```bash
ls *.log                    # All log files
ls error_*.log              # Error logs only
ls access_log_2024*         # 2024 access logs
```

### File Organization
```bash
mv *.jpg images/            # Move all images
cp file[1-5].txt backup/    # Copy files 1-5 to backup
rm test??                   # Remove test files with 2-char suffix
```

## Summary
Wildcards are powerful tools that significantly improve productivity in Linux. They allow you to work with multiple files efficiently and are essential for anyone working with servers or large file systems.

**Key Takeaway**: Master these patterns to make your Linux workflow faster and more efficient!