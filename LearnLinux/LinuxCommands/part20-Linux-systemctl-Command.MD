# Linux systemctl Command Guide

## Introduction

This guide covers the `systemctl` command in Linux, which is used to manage services on your Linux system. We have used this command in many previous videos to start and stop services, but here we'll explore it in more detail.

## What are Services?

Services are simply programs or applications that run in the background, and we interact with them externally.

**Example:**
- In Windows, when you install MySQL, the MySQL service runs in the background
- The database runs continuously in the background
- You interact with it externally through tools like command line or GUI
- You execute queries and the service responds

Similarly, Linux has many services that we manage using the `systemctl` command.

## What is systemctl?

**systemctl** is used to control the status of services.

### Syntax
```bash
systemctl [option] [service_name]
```

## Basic Operations

### 1. Starting and Stopping Services

**Start a service:**
```bash
systemctl start service_name
```

**Stop a service:**
```bash
systemctl stop service_name
```

**Example:**
```bash
systemctl start httpd
systemctl stop httpd
```

### 2. Checking Service Status

To check if a service started or stopped successfully:

```bash
systemctl status service_name
```

**Example:**
```bash
systemctl status httpd
```

**Output indicates:**
- `Active: active (running)` - Service is running successfully
- `Active: inactive (dead)` - Service is stopped

### 3. Restarting Services

When you make configuration changes to an application and want those changes to take effect, you need to restart the service:

```bash
systemctl restart service_name
```

**Example:**
```bash
systemctl restart httpd
```

This will stop and then start the service again, applying any configuration changes.

When you check the status after restart, you'll see basic logs related to your service, including timing information showing when it stopped and started.

## Enabling and Disabling Services

### Enable a Service

**Purpose:** To ensure a service automatically starts when the system boots up.

If you want a service to:
- Always be up and running
- Automatically start after a system reboot
- Not require manual starting every time

Then you should enable the service.

**Example Use Case:**
- Web services that need to be always available
- Database servers that should start automatically

**Check if a service is enabled:**
```bash
systemctl is-enabled service_name
```

**Enable a service:**
```bash
systemctl enable service_name
```

**Important Note:** Enabling a service does NOT start it immediately. It only configures it to start automatically on boot.

**To enable and start together:**
```bash
systemctl enable --now service_name
```

The `--now` flag will enable the service AND start it immediately in a single command.

### Disable a Service

**Purpose:** Prevents a service from starting automatically when the computer reboots.

```bash
systemctl disable service_name
```

**Important:** Disabling a service doesn't prevent users from manually starting it - it only prevents automatic startup on boot.

## Masking Services

### What is Masking?

To completely prevent a service from being started (by any user or any program):

```bash
systemctl mask service_name
```

**What masking does:**
- No user (even root) can start the service directly
- External programs or scripts cannot start it
- Dependencies from other services cannot trigger it

**Example:**
```bash
systemctl mask httpd
```

If you try to start a masked service:
```bash
systemctl start httpd
# Output: Failed to start httpd.service: Unit is masked
```

### Unmasking a Service

To allow a service to be started again:

```bash
systemctl unmask service_name
```

After unmasking, you can start the service normally:
```bash
systemctl start service_name
```

## System Control Commands

### Power Off System
```bash
systemctl poweroff
```

### Reboot System
```bash
systemctl reboot
```

### Force Reboot (Ignore Other Users)
```bash
systemctl --force reboot
```

This will force reboot even if other users are logged in.

## Advanced Commands

### List Available Sockets

To see sockets available for inter-process communication:

```bash
systemctl list-sockets --show-types
```

This shows:
- Different columns for socket information
- Socket types
- Which unit is using each socket

This is helpful when you need socket-related information.

### List Active Units

To see all active and running processes/units:

```bash
systemctl
```

**Filter for running services:**
```bash
systemctl | grep -i running
```

**View with pager:**
```bash
systemctl -n --more
```

This shows all loaded, active, and running services with details like:
- Service name (e.g., cronie, bluetooth, sshd)
- Load status
- Active status
- Running status

This is very helpful when you need to see which services are currently active and running.

## Practice Tasks

To reinforce your learning, try these tasks:

### Task 1: Check Service Status
- Check the status of the SSH service (`sshd`)

### Task 2: Stop and Disable Service
- Stop the service
- Check its status
- Disable the service
- Reboot your computer
- Check if the service is running (it should NOT be)

### Task 3: Enable Service
- Enable the service
- Reboot your system
- Check if the service started automatically (it SHOULD be running)

### Task 4: Mask and Test
- Stop the service
- Mask it
- Try starting it (you should NOT be able to)

These practical tasks will help you confirm that everything is really working as explained, moving beyond just theoretical knowledge.

## Summary

The `systemctl` command is a powerful tool for managing services in Linux. Key operations include:

- **start/stop** - Control service execution
- **status** - Check service state
- **restart** - Apply configuration changes
- **enable/disable** - Control automatic startup on boot
- **mask/unmask** - Completely prevent/allow service startup
- **list-sockets** - View inter-process communication sockets
- **list units** - View all active services

## Conclusion

I hope this video helped you learn something new! If you're new to this channel, please subscribe.

---

**Note:** This guide is based on a YouTube video transcript about Linux systemctl commands.