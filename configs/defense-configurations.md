# Defense Configuration Guide

## Fail2Ban Configuration

### /etc/fail2ban/jail.local
```ini
[wordpress]
enabled = true
port = http,https
filter = wordpress
logpath = /var/log/apache2/access.log
maxretry = 3
bantime = 3600
findtime = 600
```

### /etc/fail2ban/filter.d/wordpress.conf
```ini
[Definition]
failregex = ^<HOST> .* "POST /wp-login.php
ignoreregex =
```

## WordPress Security Plugin (Wordfence Config)

```php
// wp-config.php additions
define('DISALLOW_FILE_EDIT', true);
define('WP_FAIL2BAN_LOG', '/var/log/wordpress-fail2ban.log');

// Login attempt limiting
$wf_config = array(
    'loginAttempts' => 5,
    'lockoutDuration' => 900, // 15 minutes
    '2fa_required' => true
);
```

## Apache Rate Limiting

### /etc/apache2/mods-enabled/evasive.conf
```apache
<IfModule mod_evasive20.c>
    DOSHashTableSize 3097
    DOSPageCount 5
    DOSSiteCount 100
    DOSPageInterval 1
    DOSSiteInterval 1
    DOSBlockingPeriod 600
</IfModule>
```

## Firewall Rules (iptables)

```bash
# Rate limit connections to port 80
iptables -A INPUT -p tcp --dport 80 -m state --state NEW -m recent --set
iptables -A INPUT -p tcp --dport 80 -m state --state NEW -m recent \
  --update --seconds 60 --hitcount 20 -j DROP
```

## SIEM Alert Rule (Generic)

```yaml
rule_name: WordPress Brute Force Detected
severity: high
conditions:
  - field: uri
    value: /wp-login.php
  - field: failed_attempts
    operator: greater_than
    value: 10
    timeframe: 5m
action:
  - alert_soc
  - block_ip
  - notify_admin
```

## Password Policy

```
Minimum Length: 12 characters
Requirements:
  - Uppercase letters
  - Lowercase letters
  - Numbers
  - Special characters
  - No dictionary words
  - No username in password
Expiration: 90 days
History: Last 5 passwords
```
