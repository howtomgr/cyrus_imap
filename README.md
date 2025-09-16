# Cyrus IMAP Installation Guide

Cyrus IMAP is a free and open-source Mail Server. A highly scalable enterprise mail system

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
  - CPU: 2 cores minimum (4+ cores recommended)
  - RAM: 2GB minimum (4GB+ recommended)
  - Storage: 1GB for installation
  - Network: 143/993 ports
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 143/993 (default cyrus-imap port)
- **Dependencies**:
  - cyrus-sasl, cyrus-admin
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

# Install cyrus-imap
sudo dnf install -y cyrus-imap cyrus-sasl, cyrus-admin

# Enable and start service
sudo systemctl enable --now cyrus-imapd

# Configure firewall
sudo firewall-cmd --permanent --add-service=cyrus-imap
sudo firewall-cmd --reload

# Verify installation
cyrus-imap --version || systemctl status cyrus-imapd
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install cyrus-imap
sudo apt install -y cyrus-imap cyrus-sasl, cyrus-admin

# Enable and start service
sudo systemctl enable --now cyrus-imapd

# Configure firewall
sudo ufw allow 143/993

# Verify installation
cyrus-imap --version || systemctl status cyrus-imapd
```

### Arch Linux

```bash
# Install cyrus-imap
sudo pacman -S cyrus-imap

# Enable and start service
sudo systemctl enable --now cyrus-imapd

# Verify installation
cyrus-imap --version || systemctl status cyrus-imapd
```

### Alpine Linux

```bash
# Install cyrus-imap
apk add --no-cache cyrus-imap

# Enable and start service
rc-update add cyrus-imapd default
rc-service cyrus-imapd start

# Verify installation
cyrus-imap --version || rc-service cyrus-imapd status
```

### openSUSE/SLES

```bash
# Install cyrus-imap
sudo zypper install -y cyrus-imap cyrus-sasl, cyrus-admin

# Enable and start service
sudo systemctl enable --now cyrus-imapd

# Configure firewall
sudo firewall-cmd --permanent --add-service=cyrus-imap
sudo firewall-cmd --reload

# Verify installation
cyrus-imap --version || systemctl status cyrus-imapd
```

### macOS

```bash
# Using Homebrew
brew install cyrus-imap

# Start service
brew services start cyrus-imap

# Verify installation
cyrus-imap --version
```

### FreeBSD

```bash
# Using pkg
pkg install cyrus-imap

# Enable in rc.conf
echo 'cyrus-imapd_enable="YES"' >> /etc/rc.conf

# Start service
service cyrus-imapd start

# Verify installation
cyrus-imap --version || service cyrus-imapd status
```

### Windows

```powershell
# Using Chocolatey
choco install cyrus-imap

# Or using Scoop
scoop install cyrus-imap

# Verify installation
cyrus-imap --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory if needed
sudo mkdir -p /etc/cyrus-imapd

# Set up basic configuration
sudo tee /etc/cyrus-imapd/cyrus-imap.conf << 'EOF'
# Cyrus IMAP Configuration
maxchild: 100, maxconnections: 1000
EOF

# Test configuration
sudo cyrus-imap -t || sudo cyrus-imapd configtest

# Reload service
sudo systemctl reload cyrus-imapd
```

### Security Hardening

```bash
# Set appropriate permissions
sudo chown -R cyrus-imap:cyrus-imap /etc/cyrus-imapd
sudo chmod 750 /etc/cyrus-imapd

# Enable security features
# See security section for detailed hardening steps
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable cyrus-imapd

# Start service
sudo systemctl start cyrus-imapd

# Stop service
sudo systemctl stop cyrus-imapd

# Restart service
sudo systemctl restart cyrus-imapd

# Reload configuration
sudo systemctl reload cyrus-imapd

# Check status
sudo systemctl status cyrus-imapd

# View logs
sudo journalctl -u cyrus-imapd -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add cyrus-imapd default

# Start service
rc-service cyrus-imapd start

# Stop service
rc-service cyrus-imapd stop

# Restart service
rc-service cyrus-imapd restart

# Check status
rc-service cyrus-imapd status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'cyrus-imapd_enable="YES"' >> /etc/rc.conf

# Start service
service cyrus-imapd start

# Stop service
service cyrus-imapd stop

# Restart service
service cyrus-imapd restart

# Check status
service cyrus-imapd status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start cyrus-imap
brew services stop cyrus-imap
brew services restart cyrus-imap

# Check status
brew services list | grep cyrus-imap
```

### Windows Service Manager

```powershell
# Start service
net start cyrus-imapd

# Stop service
net stop cyrus-imapd

# Using PowerShell
Start-Service cyrus-imapd
Stop-Service cyrus-imapd
Restart-Service cyrus-imapd

# Check status
Get-Service cyrus-imapd
```

## Advanced Configuration

### Performance Optimization

```bash
# Configure performance settings
cat >> /etc/cyrus-imapd/cyrus-imap.conf << 'EOF'
maxchild: 100, maxconnections: 1000
EOF

# Apply system tuning
sudo sysctl -w net.core.somaxconn=65535
sudo sysctl -w net.ipv4.tcp_max_syn_backlog=65535

# Restart service
sudo systemctl restart cyrus-imapd
```

### Clustering and High Availability

```bash
# Configure clustering (if supported)
# See official documentation for cluster setup

# Basic load balancing setup example
# Configure multiple instances on different ports
```

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream cyrus-imap_backend {
    server 127.0.0.1:143/993;
    server 127.0.0.1:{default_port}1 backup;
}

server {
    listen 80;
    server_name cyrus-imap.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name cyrus-imap.example.com;

    ssl_certificate /etc/ssl/certs/cyrus-imap.example.com.crt;
    ssl_certificate_key /etc/ssl/private/cyrus-imap.example.com.key;

    location / {
        proxy_pass http://cyrus-imap_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket support (if needed)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName cyrus-imap.example.com
    Redirect permanent / https://cyrus-imap.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName cyrus-imap.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/cyrus-imap.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/cyrus-imap.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:143/993/
    ProxyPassReverse / http://127.0.0.1:143/993/
    
    # WebSocket support (if needed)
    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://127.0.0.1:143/993/$1" [P,L]
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend cyrus-imap_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/cyrus-imap.pem
    redirect scheme https if !{ ssl_fc }
    default_backend cyrus-imap_backend

backend cyrus-imap_backend
    balance roundrobin
    option httpchk GET /health
    server cyrus-imap1 127.0.0.1:143/993 check
    server cyrus-imap2 127.0.0.1:{default_port}1 check backup
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R cyrus-imap:cyrus-imap /etc/cyrus-imapd
sudo chmod 750 /etc/cyrus-imapd

# Configure firewall
sudo firewall-cmd --permanent --add-service=cyrus-imap
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on

# Configure fail2ban
sudo tee /etc/fail2ban/jail.d/cyrus-imap.conf << 'EOF'
[cyrus-imap]
enabled = true
port = 143/993
filter = cyrus-imap
logpath = /var/log/cyrus/*.log
maxretry = 5
bantime = 3600
EOF
```

### SSL/TLS Configuration

```bash
# Generate SSL certificates
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/cyrus-imap.key \
    -out /etc/ssl/certs/cyrus-imap.crt

# Configure SSL in cyrus-imap
# See official documentation for SSL configuration
```

## Database Setup

### PostgreSQL Backend (if applicable)

```bash
# Create database and user
sudo -u postgres psql << EOF
CREATE DATABASE cyrus-imap_db;
CREATE USER cyrus-imap_user WITH ENCRYPTED PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE cyrus-imap_db TO cyrus-imap_user;
EOF

# Configure cyrus-imap to use PostgreSQL
# See official documentation for database configuration
```

### MySQL/MariaDB Backend (if applicable)

```bash
# Create database and user
sudo mysql << EOF
CREATE DATABASE cyrus-imap_db;
CREATE USER 'cyrus-imap_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON cyrus-imap_db.* TO 'cyrus-imap_user'@'localhost';
FLUSH PRIVILEGES;
EOF
```

## Performance Optimization

### System Tuning

```bash
# Kernel parameters
sudo tee -a /etc/sysctl.conf << EOF
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.ip_local_port_range = 1024 65535
net.core.netdev_max_backlog = 5000
vm.swappiness = 10
EOF

sudo sysctl -p

# Cyrus IMAP specific tuning
maxchild: 100, maxconnections: 1000
```

### Resource Limits

```bash
# Configure system limits
sudo tee -a /etc/security/limits.conf << EOF
cyrus-imap soft nofile 65535
cyrus-imap hard nofile 65535
cyrus-imap soft nproc 32768
cyrus-imap hard nproc 32768
EOF
```

## Monitoring

### Prometheus Integration

```yaml
# prometheus.yml configuration
scrape_configs:
  - job_name: 'cyrus-imap'
    static_configs:
      - targets: ['localhost:143/993']
    metrics_path: '/metrics'
```

### Health Checks

```bash
# Basic health check script
#!/bin/bash
if systemctl is-active --quiet cyrus-imapd; then
    echo "Cyrus IMAP is running"
    exit 0
else
    echo "Cyrus IMAP is not running"
    exit 1
fi
```

### Log Monitoring

```bash
# Configure log rotation
sudo tee /etc/logrotate.d/cyrus-imap << 'EOF'
/var/log/cyrus/*.log {
    daily
    rotate 14
    compress
    delaycompress
    missingok
    notifempty
    create 0640 cyrus-imap cyrus-imap
    postrotate
        systemctl reload cyrus-imapd > /dev/null 2>&1 || true
    endscript
}
EOF
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Cyrus IMAP backup script
BACKUP_DIR="/backup/cyrus-imap"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"

# Stop service (if required)
systemctl stop cyrus-imapd

# Backup configuration
tar -czf "$BACKUP_DIR/cyrus-imap-config-$DATE.tar.gz" /etc/cyrus-imapd

# Backup data (adjust paths as needed)
tar -czf "$BACKUP_DIR/cyrus-imap-data-$DATE.tar.gz" /var/lib/cyrus-imap

# Start service
systemctl start cyrus-imapd

# Clean old backups (keep 30 days)
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +30 -delete

echo "Backup completed: $BACKUP_DIR"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop cyrus-imapd

# Restore configuration
sudo tar -xzf /backup/cyrus-imap/cyrus-imap-config-*.tar.gz -C /

# Restore data
sudo tar -xzf /backup/cyrus-imap/cyrus-imap-data-*.tar.gz -C /

# Set permissions
sudo chown -R cyrus-imap:cyrus-imap /etc/cyrus-imapd
sudo chown -R cyrus-imap:cyrus-imap /var/lib/cyrus-imap

# Start service
sudo systemctl start cyrus-imapd
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u cyrus-imapd -n 100
sudo tail -f /var/log/cyrus/*.log

# Check configuration
sudo cyrus-imap -t || sudo cyrus-imapd configtest

# Check permissions
ls -la /etc/cyrus-imapd
ls -la /var/lib/cyrus-imap
```

2. **Connection refused**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 143/993
sudo netstat -tlnp | grep 143/993

# Check firewall
sudo firewall-cmd --list-all
sudo iptables -L -n

# Test connection
telnet localhost 143/993
nc -zv localhost 143/993
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep imapd)
htop -p $(pgrep imapd)

# Check connections
ss -ant | grep :143/993 | wc -l

# Monitor I/O
iotop -p $(pgrep imapd)
```

### Debug Mode

```bash
# Run in debug mode
sudo cyrus-imap -d
# or
sudo cyrus-imapd debug

# Increase log verbosity
# Edit configuration to enable debug logging
```

## Integration Examples

### Docker Compose

```yaml
version: '3.8'
services:
  cyrus-imap:
    image: cyrus-imap:latest
    container_name: cyrus-imap
    ports:
      - "143/993:143/993"
    volumes:
      - ./config:/etc/cyrus-imapd
      - ./data:/var/lib/cyrus-imap
    environment:
      - cyrus-imap_CONFIG=/etc/cyrus-imapd/cyrus-imap.conf
    restart: unless-stopped
    networks:
      - cyrus-imap_net

networks:
  cyrus-imap_net:
    driver: bridge
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cyrus-imap
spec:
  replicas: 3
  selector:
    matchLabels:
      app: cyrus-imap
  template:
    metadata:
      labels:
        app: cyrus-imap
    spec:
      containers:
      - name: cyrus-imap
        image: cyrus-imap:latest
        ports:
        - containerPort: 143/993
        volumeMounts:
        - name: config
          mountPath: /etc/cyrus-imapd
      volumes:
      - name: config
        configMap:
          name: cyrus-imap-config
---
apiVersion: v1
kind: Service
metadata:
  name: cyrus-imap
spec:
  selector:
    app: cyrus-imap
  ports:
  - port: 143/993
    targetPort: 143/993
  type: LoadBalancer
```

### Ansible Playbook

```yaml
---
- name: Install and configure Cyrus IMAP
  hosts: all
  become: yes
  tasks:
    - name: Install cyrus-imap
      package:
        name: cyrus-imap
        state: present
    
    - name: Configure cyrus-imap
      template:
        src: cyrus-imap.conf.j2
        dest: /etc/cyrus-imapd/cyrus-imap.conf
        owner: cyrus-imap
        group: cyrus-imap
        mode: '0640'
      notify: restart cyrus-imap
    
    - name: Start and enable cyrus-imap
      systemd:
        name: cyrus-imapd
        state: started
        enabled: yes
  
  handlers:
    - name: restart cyrus-imap
      systemd:
        name: cyrus-imapd
        state: restarted
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update cyrus-imap

# Debian/Ubuntu
sudo apt update && sudo apt upgrade cyrus-imap

# Arch Linux
sudo pacman -Syu cyrus-imap

# Alpine Linux
apk update && apk upgrade cyrus-imap

# openSUSE
sudo zypper update cyrus-imap

# FreeBSD
pkg update && pkg upgrade cyrus-imap

# Always backup before updates
tar -czf /backup/cyrus-imap-pre-update-$(date +%Y%m%d).tar.gz /etc/cyrus-imapd

# Restart after updates
sudo systemctl restart cyrus-imapd
```

### Regular Maintenance Tasks

```bash
# Clean logs
find /var/log/cyrus -name "*.log" -mtime +30 -delete

# Verify integrity
sudo cyrus-imap --verify || sudo cyrus-imapd check

# Update databases (if applicable)
sudo cyrus-imap-update-db

# Optimize performance
sudo cyrus-imap-optimize

# Check for security updates
sudo cyrus-imap --security-check
```

## Additional Resources

- Official Documentation: https://docs.cyrus-imap.org/
- GitHub Repository: https://github.com/cyrus-imap/cyrus-imap
- Community Forum: https://forum.cyrus-imap.org/
- Wiki: https://wiki.cyrus-imap.org/
- Comparison vs Dovecot, Courier, Zimbra, Exchange: https://docs.cyrus-imap.org/comparison

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
