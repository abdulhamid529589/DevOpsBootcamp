# Linux Environment Variables Guide

## Overview
Environment variables are key-value pairs that store system and user configuration information. They help programs access important settings and customize the shell environment for different users and applications.

## What are Environment Variables?

Environment variables store information such as:
- User details (username, home directory)
- System paths (where to find programs)
- Shell preferences (default shell, language)
- Application settings

### Format
```bash
VARIABLE_NAME=value
```

**Naming Convention**:
- All UPPERCASE letters
- Multiple words separated by underscores (`_`)
- Examples: `HOME`, `USER`, `PATH`, `SHELL`

## Viewing Environment Variables

### View All Environment Variables
```bash
# Method 1
printenv

# Method 2 (shorter)
env

# Both show all environment variables
```

### Sample Output
```bash
SHELL=/bin/bash
HOME=/home/paul
USER=paul
HOSTNAME=centos-01
PATH=/usr/local/bin:/usr/bin:/bin
```

### View Specific Environment Variable

**Method 1: Using printenv**
```bash
printenv HOME
# Output: /home/paul

printenv USER
# Output: paul
```

**Method 2: Using echo (with $ prefix)**
```bash
echo $HOME
# Output: /home/paul

echo $USER
# Output: paul

echo $SHELL
# Output: /bin/bash
```

**Note**: When using `echo`, you must prefix the variable name with `$` to tell the shell it's a variable.

## Practical Use Case: Script Using Environment Variables

### Example Script
```bash
# File: print-username.sh
#!/bin/bash
echo "Current user is: $USER"
```

### Testing
```bash
# Run as paul
./print-username.sh
# Output: Current user is: paul

# Switch to alex user
su - alex

# Run again
./print-username.sh
# Output: Current user is: alex
```

**Key Point**: The script automatically detects the current user without hardcoding!

## Types of Environment Variables

| Type | Scope | Persistence | Use Case |
|------|-------|-------------|----------|
| **Local Variables** | Current shell only | Temporary (session) | Testing, temporary settings |
| **User Variables** | Specific user, all their shells | Permanent | User-specific configs |
| **Global Variables** | All users | Permanent | System-wide settings |

## Creating Local Variables

### Basic Creation
```bash
# Create variable
MY_VAR=hello

# Verify (no value yet if not set)
echo $MY_VAR
# Output: hello
```

### Overwrite Variable
```bash
# Change value
MY_VAR=hi

# Check
echo $MY_VAR
# Output: hi
```

### Multiple Words (Use Quotes)
```bash
# Wrong - will cause error
MY_VAR=hello world

# Correct - use quotes
MY_VAR="hello world"

# Verify
echo $MY_VAR
# Output: hello world
```

### Local Variable Limitations
```bash
# Terminal 1
MY_VAR="hello world"
echo $MY_VAR
# Output: hello world

# Terminal 2 (new shell)
echo $MY_VAR
# Output: (nothing - variable not available)
```

**Local variables exist ONLY in the current shell session!**

### Export to Environment (Still Temporary)
```bash
# Export variable to environment
export MY_VAR="hello world"

# Check in environment
env | grep MY_VAR
# Output: MY_VAR=hello world

# But still doesn't persist in new terminals!
```

### Unset Variable
```bash
# Remove variable
unset MY_VAR

# Verify
echo $MY_VAR
# Output: (nothing)
```

## Creating Persistent User Variables

### For Specific User (All Login Shells)

Variables set in `.bash_profile` (or `.profile` in Ubuntu) persist across all login sessions for that user.

#### Step 1: Check Current Value
```bash
echo $NEW_VAR
# Output: (nothing)
```

#### Step 2: Edit User Profile
```bash
# Location of profile file
ls -la ~ | grep profile
# CentOS/RHEL: .bash_profile
# Ubuntu/Debian: .profile

# Edit the file
vi ~/.bash_profile
```

#### Step 3: Add Variable
```bash
# Add at the end of file
export NEW_VAR=linux
```

#### Step 4: Save and Apply

**Option 1: Logout and Login**
```bash
# Logout
exit

# Login again via SSH
ssh user@server

# Check
echo $NEW_VAR
# Output: linux
```

**Option 2: Source the File (Apply without logout)**
```bash
# Apply changes to current shell
source ~/.bash_profile

# Check
echo $NEW_VAR
# Output: linux

# Verify in environment
env | grep NEW_VAR
# Output: NEW_VAR=linux
```

### For New Tabs (Non-Login Shells)

Variables in `.bashrc` are loaded for new terminal tabs (interactive non-login shells).

#### Edit .bashrc
```bash
vi ~/.bashrc

# Add at the end
export NEW_VAR=linux
```

#### Test with New Tab
```bash
# Open new tab
# Check variable
echo $NEW_VAR
# Output: linux (automatically available)
```

### File Comparison

| File | When Loaded | Use Case |
|------|-------------|----------|
| `.bash_profile` | Login shells (SSH, TTY) | User environment setup |
| `.bashrc` | Interactive non-login shells (new tabs) | Aliases, functions, prompts |

**Best Practice**: Add variables to `.bash_profile` and source `.bashrc` from it:
```bash
# In .bash_profile
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi
```

## Creating Global Variables (All Users)

### Edit System Profile
```bash
# Requires sudo access
sudo vi /etc/profile
```

### Add Global Variable
```bash
# Add at the end of /etc/profile
export G_VAR=global_value
```

### Apply Changes
```bash
# Source the file
source /etc/profile

# Check
echo $G_VAR
# Output: global_value
```

### Test with Different Users
```bash
# As paul user
echo $G_VAR
# Output: global_value

# Switch to alex user
su - alex

# Check again
echo $G_VAR
# Output: global_value (available to all users!)
```

### Important Notes
- Requires **sudo/root access** to edit `/etc/profile`
- Without sudo, you'll see "Read only" error
- Changes affect **all users** on the system
- Use carefully in production environments

## Complete Workflow Summary

### Local/Temporary Variable (Testing)
```bash
# Set
MY_VAR="test"

# Use
echo $MY_VAR

# Unset
unset MY_VAR
```

### User-Specific Persistent Variable
```bash
# Edit profile
vi ~/.bash_profile

# Add
export MY_VAR="value"

# Apply
source ~/.bash_profile

# Verify
echo $MY_VAR
```

### System-Wide Global Variable
```bash
# Edit (requires sudo)
sudo vi /etc/profile

# Add
export G_VAR="global_value"

# Apply
source /etc/profile

# Verify for all users
echo $G_VAR
```

## Real-World Use Case: Application Version Management

### Scenario
You have a Java application that uses external JAR files. Different versions exist:
- `config_v1.jar`
- `config_v2.jar`
- `config_v3.jar`

The application expects to read a specific location from an environment variable.

### Solution
```bash
# Set environment variable pointing to current version
export APP_CONFIG="/opt/app/config_v2.jar"

# Application reads from $APP_CONFIG

# To switch versions, just update the variable:
export APP_CONFIG="/opt/app/config_v3.jar"

# Restart application - now uses v3!
```

### Benefits
- ✅ Easy version switching
- ✅ No code changes needed
- ✅ Clear configuration
- ✅ Environment-specific settings

## Common Environment Variables

| Variable | Description | Example Value |
|----------|-------------|---------------|
| `HOME` | User's home directory | `/home/paul` |
| `USER` | Current username | `paul` |
| `SHELL` | Default shell | `/bin/bash` |
| `PATH` | Executable search paths | `/usr/bin:/bin` |
| `PWD` | Current directory | `/home/paul/projects` |
| `HOSTNAME` | System hostname | `centos-01` |
| `LANG` | System language | `en_US.UTF-8` |

## Checking and Using PATH

### View PATH
```bash
echo $PATH
# Output: /usr/local/bin:/usr/bin:/bin:/usr/local/sbin
```

### Add to PATH (Temporary)
```bash
export PATH=$PATH:/new/directory
```

### Add to PATH (Permanent)
```bash
# Edit .bash_profile
vi ~/.bash_profile

# Add
export PATH=$PATH:/opt/myapp/bin

# Apply
source ~/.bash_profile
```

## Best Practices

### 1. Use Descriptive Names
```bash
# Good
export DB_HOST="localhost"
export APP_ENV="production"

# Avoid
export VAR1="localhost"
export X="production"
```

### 2. Backup Before Editing Profile Files
```bash
# Backup
cp ~/.bash_profile ~/.bash_profile.backup

# Then edit
vi ~/.bash_profile
```

### 3. Test Locally First
```bash
# Test as local variable first
MY_VAR="test"
echo $MY_VAR

# If works, make permanent
export MY_VAR="test"
```

### 4. Document Changes
```bash
# In .bash_profile, add comments
# Application database configuration
export DB_HOST="localhost"
export DB_PORT="5432"
```

### 5. Use Quotes for Values with Spaces
```bash
# Correct
export APP_NAME="My Application"

# Wrong
export APP_NAME=My Application  # Error!
```

### 6. Avoid Overwriting System Variables
```bash
# Dangerous - don't do this
export PATH="/my/path"  # Overwrites entire PATH!

# Safe - append instead
export PATH=$PATH:/my/path
```

## Troubleshooting

### Variable Not Available in New Terminal
**Problem**: Set variable but not available in new shell
**Solution**: 
- Add to `.bash_profile` for login shells
- Add to `.bashrc` for new tabs
- Run `source` to apply immediately

### Permission Denied When Editing /etc/profile
**Problem**: Cannot edit system file
**Solution**: Use `sudo`
```bash
sudo vi /etc/profile
```

### Variable Not Updating
**Problem**: Changed variable but still shows old value
**Solution**: Source the file or start new session
```bash
source ~/.bash_profile
```

### Variable Works in One User, Not Another
**Problem**: Variable set in user profile, not available to other users
**Solution**: Set in `/etc/profile` for all users

## Quick Reference

```bash
# View all variables
env
printenv

# View specific variable
echo $VARIABLE_NAME
printenv VARIABLE_NAME

# Set local variable
VAR=value

# Export to environment (temporary)
export VAR=value

# Unset variable
unset VAR

# Permanent user variable (add to file)
vi ~/.bash_profile
export VAR=value
source ~/.bash_profile

# Permanent global variable (requires sudo)
sudo vi /etc/profile
export VAR=value
source /etc/profile
```

## Summary

- **Environment variables** store configuration and system information
- **Local variables** exist only in current shell
- **User variables** (`.bash_profile`) persist for specific user
- **Global variables** (`/etc/profile`) available to all users
- Use `export` to make variables available to child processes
- Use `source` to apply changes without logout
- Always backup profile files before editing

**Key Takeaway**: Environment variables provide flexible configuration management. Choose the right scope (local/user/global) based on your needs!