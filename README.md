# shynet Installation Guide

shynet is a free and open-source web analytics. Shynet provides modern, privacy-friendly, cookie-free web analytics

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
  - RAM: 512MB minimum
  - Storage: 1GB for data
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8080 (default shynet port)
  - None
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

# Install shynet
sudo dnf install -y shynet

# Enable and start service
sudo systemctl enable --now shynet

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
shynet --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install shynet
sudo apt install -y shynet

# Enable and start service
sudo systemctl enable --now shynet

# Configure firewall
sudo ufw allow 8080

# Verify installation
shynet --version
```

### Arch Linux

```bash
# Install shynet
sudo pacman -S shynet

# Enable and start service
sudo systemctl enable --now shynet

# Verify installation
shynet --version
```

### Alpine Linux

```bash
# Install shynet
apk add --no-cache shynet

# Enable and start service
rc-update add shynet default
rc-service shynet start

# Verify installation
shynet --version
```

### openSUSE/SLES

```bash
# Install shynet
sudo zypper install -y shynet

# Enable and start service
sudo systemctl enable --now shynet

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
shynet --version
```

### macOS

```bash
# Using Homebrew
brew install shynet

# Start service
brew services start shynet

# Verify installation
shynet --version
```

### FreeBSD

```bash
# Using pkg
pkg install shynet

# Enable in rc.conf
echo 'shynet_enable="YES"' >> /etc/rc.conf

# Start service
service shynet start

# Verify installation
shynet --version
```

### Windows

```bash
# Using Chocolatey
choco install shynet

# Or using Scoop
scoop install shynet

# Verify installation
shynet --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/shynet

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
shynet --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable shynet

# Start service
sudo systemctl start shynet

# Stop service
sudo systemctl stop shynet

# Restart service
sudo systemctl restart shynet

# Check status
sudo systemctl status shynet

# View logs
sudo journalctl -u shynet -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add shynet default

# Start service
rc-service shynet start

# Stop service
rc-service shynet stop

# Restart service
rc-service shynet restart

# Check status
rc-service shynet status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'shynet_enable="YES"' >> /etc/rc.conf

# Start service
service shynet start

# Stop service
service shynet stop

# Restart service
service shynet restart

# Check status
service shynet status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start shynet
brew services stop shynet
brew services restart shynet

# Check status
brew services list | grep shynet
```

### Windows Service Manager

```powershell
# Start service
net start shynet

# Stop service
net stop shynet

# Using PowerShell
Start-Service shynet
Stop-Service shynet
Restart-Service shynet

# Check status
Get-Service shynet
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream shynet_backend {
    server 127.0.0.1:8080;
}

server {
    listen 80;
    server_name shynet.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name shynet.example.com;

    ssl_certificate /etc/ssl/certs/shynet.example.com.crt;
    ssl_certificate_key /etc/ssl/private/shynet.example.com.key;

    location / {
        proxy_pass http://shynet_backend;
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
    ServerName shynet.example.com
    Redirect permanent / https://shynet.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName shynet.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/shynet.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/shynet.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8080/
    ProxyPassReverse / http://127.0.0.1:8080/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend shynet_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/shynet.pem
    redirect scheme https if !{ ssl_fc }
    default_backend shynet_backend

backend shynet_backend
    balance roundrobin
    server shynet1 127.0.0.1:8080 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R shynet:shynet /etc/shynet
sudo chmod 750 /etc/shynet

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
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
sudo systemctl status shynet

# View logs
sudo journalctl -u shynet -f

# Monitor resource usage
top -p $(pgrep shynet)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/shynet"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/shynet-backup-$DATE.tar.gz" /etc/shynet /var/lib/shynet

echo "Backup completed: $BACKUP_DIR/shynet-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop shynet

# Restore from backup
tar -xzf /backup/shynet/shynet-backup-*.tar.gz -C /

# Start service
sudo systemctl start shynet
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u shynet -n 100
sudo tail -f /var/log/shynet/shynet.log

# Check configuration
shynet --version

# Check permissions
ls -la /etc/shynet
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8080

# Test connectivity
telnet localhost 8080

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep shynet)

# Check disk I/O
iotop -p $(pgrep shynet)

# Check connections
ss -an | grep 8080
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  shynet:
    image: shynet:latest
    ports:
      - "8080:8080"
    volumes:
      - ./config:/etc/shynet
      - ./data:/var/lib/shynet
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update shynet

# Debian/Ubuntu
sudo apt update && sudo apt upgrade shynet

# Arch Linux
sudo pacman -Syu shynet

# Alpine Linux
apk update && apk upgrade shynet

# openSUSE
sudo zypper update shynet

# FreeBSD
pkg update && pkg upgrade shynet

# Always backup before updates
tar -czf /backup/shynet-pre-update-$(date +%Y%m%d).tar.gz /etc/shynet

# Restart after updates
sudo systemctl restart shynet
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/shynet

# Clean old logs
find /var/log/shynet -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/shynet
```

## Additional Resources

- Official Documentation: https://docs.shynet.org/
- GitHub Repository: https://github.com/shynet/shynet
- Community Forum: https://forum.shynet.org/
- Best Practices Guide: https://docs.shynet.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
