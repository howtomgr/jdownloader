# jdownloader Installation Guide

jdownloader is a free and open-source download management. JDownloader automates downloading from various hosting sites

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 1GB minimum
  - Storage: 1GB for downloads
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 5800 (default jdownloader port)
  - VNC on 5900
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install jdownloader
sudo dnf install -y jdownloader

# Enable and start service
sudo systemctl enable --now jdownloader

# Configure firewall
sudo firewall-cmd --permanent --add-port=5800/tcp
sudo firewall-cmd --reload

# Verify installation
jdownloader --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install jdownloader
sudo apt install -y jdownloader

# Enable and start service
sudo systemctl enable --now jdownloader

# Configure firewall
sudo ufw allow 5800

# Verify installation
jdownloader --version
```

### Arch Linux

```bash
# Install jdownloader
sudo pacman -S jdownloader

# Enable and start service
sudo systemctl enable --now jdownloader

# Verify installation
jdownloader --version
```

### Alpine Linux

```bash
# Install jdownloader
apk add --no-cache jdownloader

# Enable and start service
rc-update add jdownloader default
rc-service jdownloader start

# Verify installation
jdownloader --version
```

### openSUSE/SLES

```bash
# Install jdownloader
sudo zypper install -y jdownloader

# Enable and start service
sudo systemctl enable --now jdownloader

# Configure firewall
sudo firewall-cmd --permanent --add-port=5800/tcp
sudo firewall-cmd --reload

# Verify installation
jdownloader --version
```

### macOS

```bash
# Using Homebrew
brew install jdownloader

# Start service
brew services start jdownloader

# Verify installation
jdownloader --version
```

### FreeBSD

```bash
# Using pkg
pkg install jdownloader

# Enable in rc.conf
echo 'jdownloader_enable="YES"' >> /etc/rc.conf

# Start service
service jdownloader start

# Verify installation
jdownloader --version
```

### Windows

```bash
# Using Chocolatey
choco install jdownloader

# Or using Scoop
scoop install jdownloader

# Verify installation
jdownloader --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/jdownloader

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
jdownloader --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable jdownloader

# Start service
sudo systemctl start jdownloader

# Stop service
sudo systemctl stop jdownloader

# Restart service
sudo systemctl restart jdownloader

# Check status
sudo systemctl status jdownloader

# View logs
sudo journalctl -u jdownloader -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add jdownloader default

# Start service
rc-service jdownloader start

# Stop service
rc-service jdownloader stop

# Restart service
rc-service jdownloader restart

# Check status
rc-service jdownloader status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'jdownloader_enable="YES"' >> /etc/rc.conf

# Start service
service jdownloader start

# Stop service
service jdownloader stop

# Restart service
service jdownloader restart

# Check status
service jdownloader status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start jdownloader
brew services stop jdownloader
brew services restart jdownloader

# Check status
brew services list | grep jdownloader
```

### Windows Service Manager

```powershell
# Start service
net start jdownloader

# Stop service
net stop jdownloader

# Using PowerShell
Start-Service jdownloader
Stop-Service jdownloader
Restart-Service jdownloader

# Check status
Get-Service jdownloader
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream jdownloader_backend {
    server 127.0.0.1:5800;
}

server {
    listen 80;
    server_name jdownloader.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name jdownloader.example.com;

    ssl_certificate /etc/ssl/certs/jdownloader.example.com.crt;
    ssl_certificate_key /etc/ssl/private/jdownloader.example.com.key;

    location / {
        proxy_pass http://jdownloader_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName jdownloader.example.com
    Redirect permanent / https://jdownloader.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName jdownloader.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/jdownloader.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/jdownloader.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:5800/
    ProxyPassReverse / http://127.0.0.1:5800/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend jdownloader_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/jdownloader.pem
    redirect scheme https if !{ ssl_fc }
    default_backend jdownloader_backend

backend jdownloader_backend
    balance roundrobin
    server jdownloader1 127.0.0.1:5800 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R jdownloader:jdownloader /etc/jdownloader
sudo chmod 750 /etc/jdownloader

# Configure firewall
sudo firewall-cmd --permanent --add-port=5800/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status jdownloader

# View logs
sudo journalctl -u jdownloader -f

# Monitor resource usage
top -p $(pgrep jdownloader)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/jdownloader"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/jdownloader-backup-$DATE.tar.gz" /etc/jdownloader /var/lib/jdownloader

echo "Backup completed: $BACKUP_DIR/jdownloader-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop jdownloader

# Restore from backup
tar -xzf /backup/jdownloader/jdownloader-backup-*.tar.gz -C /

# Start service
sudo systemctl start jdownloader
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u jdownloader -n 100
sudo tail -f /var/log/jdownloader/jdownloader.log

# Check configuration
jdownloader --version

# Check permissions
ls -la /etc/jdownloader
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 5800

# Test connectivity
telnet localhost 5800

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep jdownloader)

# Check disk I/O
iotop -p $(pgrep jdownloader)

# Check connections
ss -an | grep 5800
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  jdownloader:
    image: jdownloader:latest
    ports:
      - "5800:5800"
    volumes:
      - ./config:/etc/jdownloader
      - ./data:/var/lib/jdownloader
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update jdownloader

# Debian/Ubuntu
sudo apt update && sudo apt upgrade jdownloader

# Arch Linux
sudo pacman -Syu jdownloader

# Alpine Linux
apk update && apk upgrade jdownloader

# openSUSE
sudo zypper update jdownloader

# FreeBSD
pkg update && pkg upgrade jdownloader

# Always backup before updates
tar -czf /backup/jdownloader-pre-update-$(date +%Y%m%d).tar.gz /etc/jdownloader

# Restart after updates
sudo systemctl restart jdownloader
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/jdownloader

# Clean old logs
find /var/log/jdownloader -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/jdownloader
```

## Additional Resources

- Official Documentation: https://docs.jdownloader.org/
- GitHub Repository: https://github.com/jdownloader/jdownloader
- Community Forum: https://forum.jdownloader.org/
- Best Practices Guide: https://docs.jdownloader.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
