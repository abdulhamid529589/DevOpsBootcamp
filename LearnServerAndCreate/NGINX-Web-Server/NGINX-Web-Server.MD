# NGINX Web Server - Complete Practical Guide

## Overview
This comprehensive guide covers NGINX from basics to advanced concepts including web server setup, reverse proxy, load balancing, HTTPS/SSL configuration, and production-ready deployments.

---

## Table of Contents
1. [What is NGINX?](#what-is-nginx)
2. [Installation & Setup](#installation--setup)
3. [Basic Configuration](#basic-configuration)
4. [Hosting Multiple Websites](#hosting-multiple-websites)
5. [Custom Domain Setup](#custom-domain-setup)
6. [HTTPS/SSL Configuration](#httpsssl-configuration)
7. [Reverse Proxy](#reverse-proxy)
8. [Load Balancing](#load-balancing)
9. [Advanced Topics](#advanced-topics)

---

## What is NGINX?

**Definition:** NGINX is a web server that delivers websites to users.

### Key Features
- **Web Server**: Serves website content
- **Reverse Proxy**: Routes requests to backend servers
- **Load Balancer**: Distributes traffic across multiple servers
- **Caching**: Improves performance
- **SSL/TLS Termination**: Handles encryption

### How It Works
```
User → Internet → NGINX Server → Website Content → User's Browser
```

**Process:**
1. User requests website (e.g., example.com)
2. NGINX listens for incoming requests
3. NGINX checks configuration
4. Delivers website content to user

---

## Installation & Setup

### Prerequisites
- AWS Account (or any Linux server)
- EC2 Instance (Amazon Linux 2 recommended)

### Step 1: Launch EC2 Instance

**Instance Configuration:**
- Name: `nginx-server`
- AMI: Amazon Linux 2
- Instance Type: t2.micro (free tier)
- Key Pair: Proceed without (use EC2 Instance Connect)

**Network Settings:**
- ✅ Allow HTTP traffic from internet (Port 80)
- ✅ Public IP enabled

### Step 2: Install NGINX

```bash
# Update system
sudo yum update -y

# Install NGINX
sudo yum install nginx -y
```

### Step 3: Start NGINX Service

```bash
# Start NGINX
sudo systemctl start nginx

# Check status
sudo systemctl status nginx

# Enable auto-start on boot
sudo systemctl enable nginx
```

### Step 4: Verify Installation

Access your server's public IP in browser:
```
http://YOUR_PUBLIC_IP
```

You should see: **"Welcome to nginx!"**

---

## Basic Configuration

### Configuration File Locations

**Main Config:**
```
/etc/nginx/nginx.conf
```

**Website Content:**
```
/usr/share/nginx/html/
```

**Logs:**
```
/var/log/nginx/access.log
/var/log/nginx/error.log
```

### Understanding NGINX Config Structure

```nginx
http {
    server {
        listen 80;
        root /usr/share/nginx/html;
        
        location / {
            # Configuration here
        }
    }
}
```

**Components:**
- `http {}`: HTTP-level configuration
- `server {}`: Virtual host configuration
- `location {}`: URL-specific settings

---

## Creating Your First Website

### Step 1: Create Website Directory

```bash
# Create project directory
sudo mkdir -p /usr/share/nginx/html/example.com

# Navigate to directory
cd /usr/share/nginx/html/example.com
```

### Step 2: Create HTML File

```bash
sudo vi index.html
```

**Basic HTML Template:**
```html
<!DOCTYPE html>
<html>
<head>
    <title>My Website</title>
</head>
<body>
    <h1>This is an example website</h1>
</body>
</html>
```

### Step 3: Configure NGINX (From Scratch)

```bash
# Backup default config
sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.backup

# Edit config
sudo vi /etc/nginx/nginx.conf
```

**Minimal Configuration:**
```nginx
events {}

http {
    server {
        listen 80;
        root /usr/share/nginx/html/example.com;
    }
}
```

### Step 4: Test & Reload

```bash
# Test configuration syntax
sudo nginx -t

# Reload NGINX
sudo systemctl reload nginx
```

### Step 5: Access Website

```
http://YOUR_PUBLIC_IP
```

---

## Hosting Multiple Websites

### Scenario
Host multiple websites on the same server using different ports or domains.

### Configuration

```nginx
events {}

http {
    # First Website (Port 8080)
    server {
        listen 8080;
        server_name example.com;
        root /usr/share/nginx/html/example.com;
        
        location / {
            try_files $uri $uri/ =404;
        }
    }
    
    # Second Website (Port 8081)
    server {
        listen 8081;
        server_name mywebsite.com;
        root /usr/share/nginx/html/mywebsite.com;
        
        location / {
            try_files $uri $uri/ =404;
        }
    }
}
```

### Enable Custom Ports in Security Group

**AWS Console:**
1. Go to EC2 → Instances
2. Select instance → Security tab
3. Edit Inbound Rules
4. Add Rule: Custom TCP → Port 8080 → Source: Anywhere
5. Add Rule: Custom TCP → Port 8081 → Source: Anywhere
6. Save Rules

---

## Custom Domain Setup

### Step 1: Register Domain

Purchase domain from:
- GoDaddy
- Hostinger
- Namecheap
- Any domain registrar

**Example:** `myexample.xyz` (cheapest option for testing)

### Step 2: Configure DNS Records

**In Domain Registrar Dashboard:**
1. Go to DNS Management
2. Add A Record:
   - **Type:** A
   - **Host:** @ (or leave blank)
   - **Points to:** Your EC2 Public IP
   - **TTL:** Automatic

**Optional - Add www subdomain:**
- **Type:** A
- **Host:** www
- **Points to:** Your EC2 Public IP

### Step 3: Update NGINX Config

```nginx
server {
    listen 80;
    server_name myexample.xyz www.myexample.xyz;
    root /usr/share/nginx/html/myexample.xyz;
    
    location / {
        try_files $uri $uri/ =404;
    }
}
```

### Step 4: Reload & Test

```bash
sudo nginx -t
sudo systemctl reload nginx
```

**Access:**
```
http://myexample.xyz
```

---

## HTTPS/SSL Configuration

### Why HTTPS?
- **Security**: Encrypts data between browser and server
- **Trust**: Shows padlock icon in browser
- **SEO**: Google ranks HTTPS sites higher
- **Required**: For credit card processing, passwords, sensitive data

### Step 1: Install Certbot (Let's Encrypt)

```bash
# Install EPEL repository
sudo yum install -y epel-release

# Install Certbot
sudo yum install -y certbot
```

### Step 2: Stop NGINX Temporarily

```bash
sudo systemctl stop nginx
```

**Why?** Certbot needs port 80 to verify domain ownership.

### Step 3: Obtain SSL Certificate

```bash
sudo certbot certonly --standalone -d myexample.xyz
```

**Prompts:**
- Email address: your@email.com
- Agree to terms: Yes (Y)
- Share email: No (N)

**Certificate Locations:**
```
Certificate: /etc/letsencrypt/live/myexample.xyz/fullchain.pem
Private Key: /etc/letsencrypt/live/myexample.xyz/privkey.pem
```

**Note:** Certificates expire in 90 days (auto-renewal recommended)

### Step 4: Configure NGINX for HTTPS

```nginx
events {}

http {
    include /etc/nginx/mime.types;
    
    server {
        listen 443 ssl http2;
        server_name myexample.xyz www.myexample.xyz;
        root /usr/share/nginx/html/myexample.xyz;
        
        # SSL Certificates
        ssl_certificate /etc/letsencrypt/live/myexample.xyz/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/myexample.xyz/privkey.pem;
        
        # SSL Protocols
        ssl_protocols TLSv1.2 TLSv1.3;
        
        location / {
            try_files $uri $uri/ =404;
        }
    }
}
```

### Step 5: Enable HTTPS in Security Group

**AWS Console:**
- Add Rule: HTTPS → Port 443 → Source: Anywhere

### Step 6: Start NGINX & Test

```bash
sudo nginx -t
sudo systemctl start nginx
```

**Access:**
```
https://myexample.xyz
```

**Verify:** Click padlock icon → Connection is secure ✅

---

## Custom Error Pages

### Create 404 Error Page

```bash
cd /usr/share/nginx/html/myexample.xyz
sudo vi 404.html
```

**404.html Template:**
```html
<!DOCTYPE html>
<html>
<head>
    <title>404 - Page Not Found</title>
</head>
<body>
    <h1>Oops! Something went wrong</h1>
    <p>The page you're looking for doesn't exist.</p>
    <a href="/">Return to Home</a>
</body>
</html>
```

### Configure Error Page

```nginx
server {
    listen 443 ssl http2;
    server_name myexample.xyz;
    root /usr/share/nginx/html/myexample.xyz;
    
    # SSL configuration...
    
    # Custom error page
    error_page 404 /404.html;
    
    location / {
        try_files $uri $uri/ =404;
    }
}
```

**Test:**
```
https://myexample.xyz/nonexistent-page
```

---

## Including CSS & Static Files

### Issue: CSS Not Loading

**Problem:** When using `nginx.conf` from scratch, CSS/JS files may not load.

**Solution:** Include MIME types

### Fix Configuration

```nginx
http {
    include /etc/nginx/mime.types;  # This line is critical!
    
    server {
        # ... rest of config
    }
}
```

**What are MIME types?**
- Tells browser how to handle different file types
- `.css` → `text/css`
- `.js` → `application/javascript`
- `.jpg` → `image/jpeg`
- `.pdf` → `application/pdf`

### Clear Browser Cache

**Hard Reload:**
- Windows/Linux: `Ctrl + Shift + R`
- Mac: `Cmd + Shift + R`

---

## Logging Configuration

### Default Log Locations

```bash
# Access logs (all requests)
/var/log/nginx/access.log

# Error logs (errors only)
/var/log/nginx/error.log
```

### View Logs

```bash
# View access log
sudo less /var/log/nginx/access.log

# View error log
sudo less /var/log/nginx/error.log

# Follow logs in real-time
sudo tail -f /var/log/nginx/access.log
```

### Access Log Format

```
IP_Address - - [timestamp] "GET /path HTTP/1.1" status_code bytes "referer" "user_agent"
```

**Example:**
```
192.168.1.1 - - [15/Dec/2024:10:30:45] "GET /index.html HTTP/1.1" 200 1234 "-" "Mozilla/5.0 (Macintosh)"
```

### Custom Logs Per Website

```nginx
server {
    listen 443 ssl http2;
    server_name myexample.xyz;
    
    # Custom access log
    access_log /var/log/nginx/myexample.xyz-access.log;
    
    # Custom error log
    error_log /var/log/nginx/myexample.xyz-error.log;
    
    # ... rest of config
}
```

---

## Reverse Proxy

### What is Reverse Proxy?

**Traditional Setup:**
```
User → Internet → Web Server (Website + NGINX)
```

**Reverse Proxy Setup:**
```
User → Internet → NGINX (Reverse Proxy) → Backend Server (Website)
```

### Benefits

1. **Security**
   - Backend server not publicly accessible
   - Only NGINX exposed to internet
   - Protects backend from direct attacks

2. **Load Balancing**
   - Distribute traffic across multiple servers
   - Better performance and reliability

3. **SSL Termination**
   - Handle encryption at proxy level
   - Reduce load on backend servers

4. **Caching**
   - Cache responses at proxy level
   - Faster response times

### Setup Backend Server

**Create Second EC2 Instance:**
- Name: `web-1`
- Same configuration as before
- **Install Apache (not NGINX)**

**User Data Script (runs on startup):**
```bash
#!/bin/bash
yum install -y httpd
echo "<h1>This is Website 1</h1>" > /var/www/html/index.html
systemctl start httpd
systemctl enable httpd
```

### Configure Reverse Proxy

```nginx
server {
    listen 8080;
    server_name example.com;
    
    location / {
        proxy_pass http://BACKEND_SERVER_IP:80;
    }
}
```

**Example:**
```nginx
server {
    listen 8080;
    
    location / {
        proxy_pass http://172.31.45.67:80;
    }
}
```

### Security: Restrict Backend Access

**Backend Server Security Group:**
1. Edit Inbound Rules
2. HTTP Port 80 → Source: NGINX Server IP only
3. Remove "Anywhere" access

**Result:**
- Backend accessible only from NGINX server
- Public cannot access backend directly
- Enhanced security ✅

---

## Load Balancing

### What is Load Balancing?

Distributes incoming traffic across multiple backend servers to ensure:
- No single server gets overloaded
- Better performance
- High availability
- Fault tolerance

### Architecture

```
                   ┌─────────────┐
User 1 ────────────┤             │
User 2 ────────────┤   NGINX     │
User 3 ────────────┤ (Reverse    │
                   │  Proxy)     │
                   └──────┬──────┘
                          │
              ┌───────────┴───────────┐
              │                       │
         ┌────▼────┐             ┌────▼────┐
         │ Web-1   │             │ Web-2   │
         │ Server  │             │ Server  │
         └─────────┘             └─────────┘
```

### Setup Multiple Backend Servers

**Create Second Backend Server:**
- Instance name: `web-2`
- Use same User Data script but change message to "Website 2"

```bash
#!/bin/bash
yum install -y httpd
echo "<h1>This is Website 2</h1>" > /var/www/html/index.html
systemctl start httpd
systemctl enable httpd
```

### Configure Load Balancing

```nginx
events {}

http {
    # Define backend server pool
    upstream backend_servers {
        server 172.31.45.67:80;  # Web-1
        server 172.31.45.68:80;  # Web-2
    }
    
    server {
        listen 8080;
        
        location / {
            proxy_pass http://backend_servers;
        }
    }
}
```

### Test Load Balancing

**Refresh browser multiple times:**
```
Request 1: "This is Website 1"
Request 2: "This is Website 2"
Request 3: "This is Website 1"
Request 4: "This is Website 2"
```

**Result:** Requests alternate between servers (Round Robin)

---

## Load Balancing Methods

### 1. Round Robin (Default)

**Behavior:** Distributes requests evenly in rotation

```nginx
upstream backend_servers {
    server 172.31.45.67:80;
    server 172.31.45.68:80;
}
```

**Pattern:** 1 → 2 → 1 → 2 → 1 → 2

---

### 2. Least Connections

**Behavior:** Sends requests to server with fewest active connections

```nginx
upstream backend_servers {
    least_conn;
    
    server 172.31.45.67:80;
    server 172.31.45.68:80;
}
```

**Use Case:** When backend processing times vary

---

### 3. Weighted Round Robin

**Behavior:** Distributes traffic based on server capacity

```nginx
upstream backend_servers {
    server 172.31.45.67:80 weight=3;  # Powerful server
    server 172.31.45.68:80 weight=1;  # Basic server
}
```

**Pattern:** 3 requests to Web-1, 1 request to Web-2

---

### 4. IP Hash

**Behavior:** Same client always goes to same server

```nginx
upstream backend_servers {
    ip_hash;
    
    server 172.31.45.67:80;
    server 172.31.45.68:80;
}
```

**Use Case:** Session persistence (sticky sessions)

---

### 5. Backup Server

**Behavior:** Use backup only when primary servers fail

```nginx
upstream backend_servers {
    server 172.31.45.67:80;  # Primary
    server 172.31.45.68:80 backup;  # Backup
}
```

**Testing Failover:**
1. All traffic goes to Web-1
2. Stop/terminate Web-1
3. Traffic automatically switches to Web-2
4. High availability achieved ✅

---

## Advanced Topics

### 1. Timeouts Configuration

**Purpose:** Control connection lifetimes and prevent hanging connections

```nginx
http {
    # Client timeouts
    client_body_timeout 12s;
    client_header_timeout 12s;
    
    # Keepalive
    keepalive_timeout 20s;
    
    # Proxy timeouts (to backend)
    proxy_connect_timeout 60s;
    proxy_send_timeout 60s;
    proxy_read_timeout 60s;
    
    server {
        # ... config
    }
}
```

**Common Timeouts:**
- `keepalive_timeout`: How long to keep connection open
- `proxy_read_timeout`: How long to wait for backend response
- `client_body_timeout`: How long to wait for client request body

---

### 2. Caching

**Purpose:** Store responses to serve them faster without hitting backend

```nginx
http {
    # Define cache path and settings
    proxy_cache_path /var/cache/nginx 
                     levels=1:2 
                     keys_zone=my_cache:10m 
                     max_size=1g 
                     inactive=60m;
    
    server {
        listen 80;
        
        location / {
            proxy_pass http://backend_servers;
            proxy_cache my_cache;
            proxy_cache_valid 200 60m;  # Cache 200 responses for 60 min
        }
    }
}
```

**Benefits:**
- Faster response times
- Reduced backend load
- Better scalability

---

### 3. Rate Limiting

**Purpose:** Prevent abuse and DDoS attacks

```nginx
http {
    # Define rate limit zone
    limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;
    
    server {
        location / {
            limit_req zone=one burst=5;
            proxy_pass http://backend;
        }
    }
}
```

**Configuration:**
- `rate=1r/s`: 1 request per second
- `burst=5`: Allow 5 requests burst

---

### 4. Gzip Compression

**Purpose:** Compress responses to save bandwidth

```nginx
http {
    gzip on;
    gzip_types text/plain text/css application/json application/javascript;
    gzip_min_length 1000;
    
    server {
        # ... config
    }
}
```

---

### 5. Security Headers

```nginx
server {
    listen 443 ssl http2;
    
    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Strict-Transport-Security "max-age=31536000" always;
    
    location / {
        # ... config
    }
}
```

---

## Supported Protocols

NGINX supports multiple protocols beyond HTTP:

| Protocol | Use Case | Port |
|----------|----------|------|
| HTTP | Web traffic | 80 |
| HTTPS | Secure web traffic | 443 |
| WebSocket | Real-time communication | 80/443 |
| gRPC | Microservices communication | Custom |
| TCP/UDP | Generic proxy | Custom |
| Mail Proxy | Email protocols | 25, 110, 143 |

---

## Best Practices

### 1. Always Backup Configuration

```bash
sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.backup
```

### 2. Test Before Reloading

```bash
sudo nginx -t
sudo systemctl reload nginx
```

### 3. Use Separate Config Files

```bash
# Include separate configs
http {
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

### 4. Enable Auto-start

```bash
sudo systemctl enable nginx
```

### 5. Monitor Logs Regularly

```bash
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```

### 6. Keep NGINX Updated

```bash
sudo yum update nginx -y
```

### 7. Use Strong SSL Configuration

```nginx
ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers on;
ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';
```

---

## Common Issues & Solutions

### Issue 1: Connection Refused

**Symptoms:** Cannot access website

**Solutions:**
1. Check if NGINX is running: `sudo systemctl status nginx`
2. Check security group rules (ports 80, 443)
3. Check firewall: `sudo iptables -L`

---

### Issue 2: 502 Bad Gateway

**Symptoms:** NGINX cannot reach backend

**Solutions:**
1. Verify backend server is running
2. Check backend IP address in config
3. Verify security group allows traffic from NGINX
4. Check backend logs

---

### Issue 3: 404 Not Found

**Symptoms:** Page not found error

**Solutions:**
1. Verify file exists: `ls /usr/share/nginx/html/`
2. Check `root` directive in config
3. Verify file permissions
4. Check NGINX error logs

---

### Issue 4: SSL Certificate Issues

**Symptoms:** "Not Secure" warning

**Solutions:**
1. Verify certificate paths in config
2. Check certificate expiry: `sudo certbot certificates`
3. Renew certificate: `sudo certbot renew`
4. Restart NGINX: `sudo systemctl restart nginx`

---

## Quick Command Reference

```bash
# Service Management
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl reload nginx
sudo systemctl status nginx
sudo systemctl enable nginx

# Configuration Testing
sudo nginx -t
sudo nginx -T  # Show full config

# Log Management
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log

# Troubleshooting
sudo nginx -s reload
sudo netstat -tulpn | grep nginx
ps aux | grep nginx
```

---

## Cost Optimization (AWS)

**Important:** Always terminate instances when done testing!

```bash
# From AWS Console
1. Go to EC2 → Instances
2. Select instance(s)
3. Instance State → Terminate Instance
```

**Why?**
- Prevents unnecessary billing
- EC2 instances charge by the hour
- Even stopped instances may incur storage costs

---

## Summary

You've learned:

✅ NGINX installation and basic configuration  
✅ Hosting websites with custom domains  
✅ HTTPS/SSL setup with Let's Encrypt  
✅ Reverse proxy configuration  
✅ Load balancing with multiple backends  
✅ Custom error pages and logging  
✅ Security best practices  
✅ Production-ready configurations  

### Next Steps

1. Practice all configurations
2. Explore caching and compression
3. Learn about NGINX Plus (commercial)
4. Study microservices architectures
5. Implement monitoring (Prometheus, Grafana)

---

## Resources

- Official Documentation: https://nginx.org/en/docs/
- Let's Encrypt: https://letsencrypt.org/
- SSL Configuration Generator: https://ssl-config.mozilla.org/
- NGINX Config Tester: https://github.com/yandex/gixy

---

**Note:** This guide uses AWS EC2 for demonstrations, but concepts apply to any Linux server (VPS, on-premise, Docker, etc.).