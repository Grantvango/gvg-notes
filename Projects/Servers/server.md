# Server Security Guide - SSH Configuration

## SSH Security Best Practices

### 1. Key-Based Authentication Setup

#### Generate SSH Key Pair

```bash
# Generate a strong SSH key pair (Ed25519 preferred)
ssh-keygen -t ed25519 -C "your-email@example.com"

# Or use RSA with 4096 bits if Ed25519 isn't supported
ssh-keygen -t rsa -b 4096 -C "your-email@example.com"
```

#### Copy Public Key to Server

```bash
# Method 1: Using ssh-copy-id (recommended)
ssh-copy-id -i ~/.ssh/id_ed25519.pub username@server-ip

# Method 2: Manual copy
cat ~/.ssh/id_ed25519.pub | ssh username@server-ip "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

### 2. SSH Server Configuration Hardening

Edit `/etc/ssh/sshd_config` on your server:

```bash
# Backup original config
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup

# Edit configuration
sudo nano /etc/ssh/sshd_config
```

#### Essential Security Settings

```bash
# Change default port (security through obscurity)
Port 2222

# Disable root login
PermitRootLogin no

# Disable password authentication (use keys only)
PasswordAuthentication no
PubkeyAuthentication yes
ChallengeResponseAuthentication no

# Disable empty passwords
PermitEmptyPasswords no

# Limit user access
AllowUsers your-username
# Or limit to specific groups
# AllowGroups ssh-users

# Protocol and encryption settings
Protocol 2
HostbasedAuthentication no
IgnoreRhosts yes

# Connection settings
MaxAuthTries 3
LoginGraceTime 30
MaxStartups 2
ClientAliveInterval 300
ClientAliveCountMax 2

# Disable unnecessary features
X11Forwarding no
AllowTcpForwarding no
GatewayPorts no
PermitTunnel no

# Use strong ciphers and MACs
Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com,aes128-gcm@openssh.com,aes256-ctr,aes192-ctr,aes128-ctr
MACs hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com,hmac-sha2-256,hmac-sha2-512
KexAlgorithms curve25519-sha256@libssh.org,diffie-hellman-group16-sha512,diffie-hellman-group18-sha512,diffie-hellman-group14-sha256
```

#### Restart SSH Service

```bash
# Test configuration first
sudo sshd -t

# If no errors, restart SSH
sudo systemctl restart sshd
# or on older systems
sudo service ssh restart
```

### 3. Additional Security Measures

#### Firewall Configuration (UFW)

```bash
# Enable UFW
sudo ufw enable

# Allow SSH on custom port
sudo ufw allow 2222/tcp

# Limit SSH connections to prevent brute force
sudo ufw limit 2222/tcp

# Check status
sudo ufw status
```

#### Firewall Configuration (iptables)

```bash
# Allow SSH on custom port
sudo iptables -A INPUT -p tcp --dport 2222 -j ACCEPT

# Limit SSH connections (max 4 connections per minute)
sudo iptables -A INPUT -p tcp --dport 2222 -m state --state NEW -m recent --set
sudo iptables -A INPUT -p tcp --dport 2222 -m state --state NEW -m recent --update --seconds 60 --hitcount 4 -j DROP

# Save rules (method varies by distribution)
sudo iptables-save > /etc/iptables/rules.v4
```

#### Fail2Ban Installation and Configuration

```bash
# Install Fail2Ban
sudo apt update && sudo apt install fail2ban

# Create custom configuration
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

# Edit jail.local
sudo nano /etc/fail2ban/jail.local
```

Fail2Ban SSH jail configuration:

```ini
[sshd]
enabled = true
port = 2222
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
findtime = 600
```

#### Two-Factor Authentication (2FA)

```bash
# Install Google Authenticator
sudo apt install libpam-google-authenticator

# Configure for user
google-authenticator

# Edit PAM configuration
sudo nano /etc/pam.d/sshd
# Add: auth required pam_google_authenticator.so

# Edit SSH config
sudo nano /etc/ssh/sshd_config
# Add: ChallengeResponseAuthentication yes
# Add: AuthenticationMethods publickey,keyboard-interactive
```

### 4. Monitoring and Logging

#### Enable Detailed Logging

```bash
# In /etc/ssh/sshd_config
LogLevel VERBOSE
SyslogFacility AUTH
```

#### Monitor SSH Logs

```bash
# View recent SSH attempts
sudo tail -f /var/log/auth.log | grep ssh

# Check failed login attempts
sudo grep "Failed password" /var/log/auth.log

# Check successful logins
sudo grep "Accepted publickey" /var/log/auth.log
```

#### Log Analysis Tools

```bash
# Install and use logwatch
sudo apt install logwatch
sudo logwatch --detail high --service sshd --range today

# Use journalctl for systemd logs
sudo journalctl -u ssh -f
```

### 5. Client-Side Security

#### SSH Config File (~/.ssh/config)

```bash
Host myserver
    HostName server-ip-or-domain
    Port 2222
    User username
    IdentityFile ~/.ssh/id_ed25519
    IdentitiesOnly yes
    ServerAliveInterval 60
    ServerAliveCountMax 3
    # Use strong ciphers
    Ciphers chacha20-poly1305@openssh.com,aes256-gcm@openssh.com
    MACs hmac-sha2-256-etm@openssh.com,hmac-sha2-512-etm@openssh.com
```

#### Key Management

```bash
# Set proper permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519
chmod 644 ~/.ssh/id_ed25519.pub
chmod 600 ~/.ssh/config
```

### 6. Advanced Protection Techniques

#### Port Knocking

```bash
# Install knockd
sudo apt install knockd

# Configure in /etc/knockd.conf
[openSSH]
sequence = 7000,8000,9000
seq_timeout = 5
command = /sbin/iptables -A INPUT -s %IP% -p tcp --dport 2222 -j ACCEPT
tcpflags = syn

[closeSSH]
sequence = 9000,8000,7000
seq_timeout = 5
command = /sbin/iptables -D INPUT -s %IP% -p tcp --dport 2222 -j ACCEPT
tcpflags = syn
```

#### VPN Access Only

```bash
# Restrict SSH to VPN subnet only
# In /etc/ssh/sshd_config
ListenAddress 10.0.0.1  # VPN IP only

# Or use iptables
sudo iptables -A INPUT -p tcp --dport 2222 -s 10.0.0.0/24 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 2222 -j DROP
```

#### SSH Bastion/Jump Host

```bash
# Connect through bastion host
ssh -J bastion-user@bastion-host:port target-user@target-host

# Or configure in ~/.ssh/config
Host target-server
    ProxyJump bastion-user@bastion-host:port
    HostName target-host
    User target-user
```

### 7. Security Checklist

- [ ] SSH keys generated and configured
- [ ] Password authentication disabled
- [ ] Default SSH port changed
- [ ] Root login disabled
- [ ] User access limited
- [ ] Strong ciphers and MACs configured
- [ ] Firewall rules implemented
- [ ] Fail2Ban installed and configured
- [ ] 2FA enabled (optional but recommended)
- [ ] Logging enabled and monitored
- [ ] Client SSH config optimized
- [ ] Key permissions set correctly
- [ ] Unattended security updates configured
- [ ] Regular security updates applied

### 8. Maintenance Tasks

#### Automated Security Updates (Unattended Upgrades)

Configure automatic installation of security updates to keep your system protected:

```bash
# Install unattended-upgrades package
sudo apt update && sudo apt install unattended-upgrades

# Enable automatic updates
sudo dpkg-reconfigure -plow unattended-upgrades
```

Configure `/etc/apt/apt.conf.d/50unattended-upgrades`:

```bash
# Edit the configuration file
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

Recommended configuration:

```bash
// Automatically upgrade packages from these (origin:archive) pairs
Unattended-Upgrade::Allowed-Origins {
    "${distro_id}:${distro_codename}-security";
    "${distro_id}ESMApps:${distro_codename}-apps-security";
    "${distro_id}ESM:${distro_codename}-infra-security";
    // Optional: include stable updates (be cautious)
    // "${distro_id}:${distro_codename}-updates";
};

// Packages that should never be automatically upgraded
Unattended-Upgrade::Package-Blacklist {
    "kernel*";
    "linux-*";
    "openssh-server";
    // Add other critical packages you want to manually control
};

// Email configuration for notifications
Unattended-Upgrade::Mail "your-email@example.com";
Unattended-Upgrade::MailOnlyOnError "true";

// Automatically remove unused dependencies
Unattended-Upgrade::Remove-Unused-Dependencies "true";

// Automatically remove unused kernel packages
Unattended-Upgrade::Remove-New-Unused-Dependencies "true";
Unattended-Upgrade::Remove-Unused-Kernel-Packages "true";

// Automatically reboot if required (be careful with this)
Unattended-Upgrade::Automatic-Reboot "false";
// If you enable reboots, set a time
// Unattended-Upgrade::Automatic-Reboot-Time "02:00";

// Enable logging
Unattended-Upgrade::SyslogEnable "true";
Unattended-Upgrade::SyslogFacility "daemon";

// Bandwidth limit for downloads (in KB/sec)
Acquire::http::Dl-Limit "200";
```

Configure automatic update intervals in `/etc/apt/apt.conf.d/20auto-upgrades`:

```bash
# Edit auto-upgrades configuration
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
```

```bash
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Download-Upgradeable-Packages "1";
APT::Periodic::AutocleanInterval "7";
APT::Periodic::Unattended-Upgrade "1";
```

#### Manual Security Updates

```bash
# Update system packages
sudo apt update && sudo apt upgrade

# Install only security updates
sudo apt update && sudo apt upgrade -s | grep -i security

# Check for SSH-specific updates
apt list --upgradable | grep ssh

# Check what unattended-upgrades would do (dry run)
sudo unattended-upgrades --dry-run --debug
```

#### Monitor Unattended Upgrades

```bash
# Check unattended-upgrades status
sudo systemctl status unattended-upgrades

# View recent upgrade logs
sudo tail -f /var/log/unattended-upgrades/unattended-upgrades.log

# Check if reboot is required
ls /var/run/reboot-required

# View packages that were upgraded
grep "upgraded" /var/log/apt/history.log | tail -10
```

#### Key Rotation

```bash
# Generate new keys periodically (annually recommended)
ssh-keygen -t ed25519 -C "your-email@example.com-$(date +%Y)"

# Remove old keys from authorized_keys
nano ~/.ssh/authorized_keys
```

#### Audit SSH Access

```bash
# Review authorized keys
cat ~/.ssh/authorized_keys

# Check active SSH sessions
who
w

# Review recent logins
last | grep ssh
```

## Emergency Access Recovery

### If Locked Out

1. Use console access (cloud provider dashboard, KVM, etc.)
2. Reset SSH configuration to defaults
3. Re-enable password authentication temporarily
4. Fix configuration issues
5. Re-apply security settings

### Backup Access Methods

- Keep a separate admin user with different SSH keys
- Configure console access through your hosting provider
- Set up out-of-band management (IPMI, iLO, etc.)
- Document recovery procedures

## Resources and References

- [OpenSSH Manual](https://www.openssh.com/manual.html)
- [SSH Security Best Practices](https://infosec.mozilla.org/guidelines/openssh)
- [Fail2Ban Documentation](https://www.fail2ban.org/wiki/index.php/Main_Page)
- [NIST Guidelines for SSH](https://nvlpubs.nist.gov/nistpubs/ir/2015/nist.ir.7966.pdf)
