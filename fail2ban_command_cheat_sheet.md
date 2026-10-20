# Fail2ban Management & Operation Commands

This document contains a comprehensive list of essential commands for managing, monitoring, and operating Fail2ban on a Linux server running Asterisk PJSIP.

---

## 1. Fail2ban Service Control

Commands used to manage the background service status, starting, stopping, and restarting Fail2ban.

* **Check Service Status**
  ```bash
  sudo systemctl status fail2ban
  ```
  *Description:* Displays whether the Fail2ban service is currently active, running, or failed.

* **Restart Fail2ban Service**
  ```bash
  sudo systemctl restart fail2ban
  ```
  *Description:* Reloads all configuration files (`jail.local`, `filter.d/`) and restarts the service.

* **Start / Stop Service**
  ```bash
  sudo systemctl start fail2ban
  sudo systemctl stop fail2ban
  ```
  *Description:* Starts or gracefully shuts down the Fail2ban daemon.

* **Enable Automatic Start on Boot**
  ```bash
  sudo systemctl enable fail2ban
  ```
  *Description:* Ensures Fail2ban starts automatically whenever the server reboots.

---

## 2. Monitoring & Jail Status Commands

Commands used to check which jails are currently active, how many IPs are banned, and detailed jail metrics.

* **List All Active Jails**
  ```bash
  sudo fail2ban-client status
  ```
  *Description:* Displays the total number of enabled jails and lists their names (e.g., `asterisk`, `asterisk-tcp`, `sshd`).

* **Check Specific Jail Status**
  ```bash
  sudo fail2ban-client status asterisk
  sudo fail2ban-client status asterisk-tcp
  ```
  *Description:* Displays detailed metrics for a specific jail including log path, currently banned IP list, and total ban count.

* **Check Currently Banned IPs via Firewall (iptables)**
  ```bash
  sudo iptables -L -n -v
  ```
  *Description:* Lists all active firewall rules directly from kernel level, showing the IPs dropped by Fail2ban chains.

---

## 3. IP Ban & Unban Operations

Commands used to manually ban, unban, or check if a specific IP address is blocked.

* **Unban a Banned IP Address**
  ```bash
  sudo fail2ban-client set asterisk unbanip <IP_ADDRESS>
  ```
  *Example:* `sudo fail2ban-client set asterisk unbanip 203.0.113.50`  
  *Description:* Immediately removes the specified IP address from the ban list of the specified jail.

* **Manually Ban an IP Address**
  ```bash
  sudo fail2ban-client set asterisk banip <IP_ADDRESS>
  ```
  *Example:* `sudo fail2ban-client set asterisk banip 198.51.100.22`  
  *Description:* Manually blocks an IP address on the specified jail without waiting for it to trigger log rules.

* **Check if an IP Address is Banned**
  ```bash
  sudo fail2ban-client isbanned <IP_ADDRESS>
  ```
  *Description:* Queries all active jails to verify if a given IP is currently blocked anywhere.

---

## 4. Testing & Regex Debugging

Commands used to test filter syntax against log files to verify regex match results.

* **Test Filter Against Log File**
  ```bash
  sudo fail2ban-regex /var/log/asterisk/messages.log /etc/fail2ban/filter.d/asterisk.local
  ```
  *Description:* Runs the regex tester to show total matched, missed, or ignored log lines without applying actual firewall bans.

* **Print Matched Lines Only**
  ```bash
  sudo fail2ban-regex --print-all-matched /var/log/asterisk/messages.log /etc/fail2ban/filter.d/asterisk.local
  ```
  *Description:* Displays only the log lines that matched the `failregex` rules for detailed inspection.

---

## 5. Live Log Inspection & Troubleshooting

Commands used to inspect real-time log activity for Fail2ban and Asterisk.

* **View Real-time Fail2ban System Logs**
  ```bash
  sudo journalctl -u fail2ban -f
  ```
  *Description:* Streams live logs for the Fail2ban service to troubleshoot startup errors or socket failures.

* **View Fail2ban Log File Directly**
  ```bash
  sudo tail -f /var/log/fail2ban.log
  ```
  *Description:* Shows live ban and unban events logged directly by Fail2ban.

* **View Asterisk Live Logs**
  ```bash
  sudo tail -f /var/log/asterisk/messages.log
  ```
  *Description:* Streams live Asterisk log entries to observe incoming SIP scans and authentication attempts.