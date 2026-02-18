# Interview Runbooks (Tier 1 / Tier 2 style)

## 1) “User can’t login”
1. Confirm username + exact error message + when it started
2. Check if it’s one user or multiple users
3. Check account state (locked/disabled/password expired)
4. Reset password / unlock if needed
5. Confirm device/network connectivity (if domain sign-in)
6. Verify by asking user to sign in again
7. Document and escalate if repeated lockouts

## 2) “Internet not working”
1. Check scope: only one device or site-wide
2. Check physical (cable/Wi-Fi), IP address, gateway
3. Ping gateway → ping public IP → test DNS
4. Flush DNS / renew DHCP if needed
5. Verify browsing + core app access

## 3) “POS payment failing”
1. Confirm: all cards or one card? all registers or one?
2. Check POS connectivity (network up? POS online?)
3. Check payment terminal status + cable + reboot sequence (if allowed)
4. Attempt test transaction (if policy allows)
5. Escalate to vendor/payment processor if network is healthy

## 4) “Printer not working”
1. Confirm local vs network printer + who is impacted
2. Power cycle + check paper/ink/error status
3. Verify PC can reach printer IP (ping)
4. Clear spooler / reinstall driver (if needed)
5. Print test page and document
