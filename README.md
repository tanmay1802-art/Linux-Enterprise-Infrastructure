# Linux Enterprise Infrastructure

An individual university lab project (SNA), built and documented by me end to end. A secure, multi-service network built from scratch on Rocky Linux (server) and Ubuntu (client), simulating a small corporate environment with DNS, email, web, intrusion prevention, VPN and a honeypot.

## Tech Stack

| Area | Tools |
|---|---|
| Hypervisor | Oracle VirtualBox |
| OS | Rocky Linux 9 (server), Ubuntu Linux (client) |
| DNS | BIND9 |
| Mail | Postfix (SMTP), Dovecot (IMAP/POP3) |
| Web | Apache HTTPD with mod_ssl |
| Security | Fail2ban, Firewalld, OpenSSL (self-signed CA) |
| VPN | WireGuard |
| Honeypot | Cowrie |

## Architecture

- Server: `maliserver.bungkus.org` (192.168.200.4)
- Client: `maliclient.bungkus.org` (192.168.200.3)
- Domain: `bungkus.org`

## Repository Structure

```
Linux-Enterprise-Infrastructure/
├── DNS/
├── Mail_Server/
├── Web_Server/
├── Security/
├── VPN/
├── Honeypot/
└── Project_Report.pdf
```

## What Was Built

### DNS Server (BIND9)
- Forward zone (`bungkus.org`) and reverse zone (`200.168.192.in-addr.arpa`)
- MX records for mail routing and A records for the web virtual hosts
- Verified from the Ubuntu client with `dig`, `nslookup` and `ping` after removing static `/etc/hosts` entries
- Files: [DNS](DNS)

### Secure Mail Server (Postfix + Dovecot)
- Maildir format, SSL/TLS on SMTP (port 465) and IMAP (port 993)
- Dovecot SASL authentication integrated with Postfix
- End-to-end delivery between two users verified with Mozilla Thunderbird
- Files: [Mail_Server](Mail_Server)

### Web Server (Apache + SSL)
- Two virtual hosts: `maliserver.bungkus.org` and `intranet.bungkus.org`
- Permanent HTTP to HTTPS redirects
- Self-signed SSL/TLS certificates
- Files: [Web_Server](Web_Server)

### Intrusion Prevention (Fail2ban)
- Jails for sshd, dovecot and recidive, integrated with Firewalld (firewallcmd-ipset backend)
- Live brute-force attack from the client tested, confirming automatic banning and unbanning
- Files: [Security](Security)

### Site-to-Site VPN (WireGuard)
- Tunnel between `maliserver` and `maliclient`
- Traced a one-directional handshake failure to a mismatched public key using packet capture and kernel debug logs
- Files: [VPN](VPN)

### SSH Honeypot (Cowrie)
- Cowrie deployed as a decoy SSH service, with real admin SSH moved to port 2200
- Full attacker session logs (commands and source IP) captured during a simulated attack
- Files: [Honeypot](Honeypot)

## Challenges and What I Learned

13 integration issues came up during the build. These taught me the most.

| Problem | Cause | Fix | What I learned |
|---|---|---|---|
| WireGuard tunnel worked in one direction only | Public key on one peer did not match the other peer's private key | Found it with packet capture and kernel debug logs, then corrected the key | Check each layer in turn (handshake, keys, firewall) instead of guessing. Logs and captures show the real cause |
| Postfix rejected SMTP logins | Dovecot's `10-master.conf` had no `unix_listener` socket, and Cyrus SASL was enabled instead of Dovecot SASL | Added the socket and switched Postfix to Dovecot SASL | Two services that look fine alone can still fail where they connect |
| Secure SMTP (port 465) not listening | Service line was commented out in `master.cf`, and the port was missing from Firewalld | Enabled the service and opened the port | A service can be blocked in three places: the daemon, the OS and the firewall. Check all three |
| Thunderbird rejected the mail certificate | Ubuntu's Snap version is sandboxed from the system certificate store | Moved to the `.deb` (PPA) release | Packaging format can change how software behaves |
| Honeypot and moved admin SSH | Real SSH moved to port 2200, which SELinux and the firewall had to allow | Labelled the port for SELinux and opened it in Firewalld | Security layers (SELinux, firewall, service config) must all agree |
| WireGuard traffic not routed | Firewalld zone and masquerading were not set for the tunnel | Configured the zone and MASQUERADE | Building a tunnel is not the same as routing traffic through it |

**Overall:** I work through a problem step by step, read logs first, and confirm the fix from the client side, not only on the server.

## Full Documentation

A step-by-step account of installation, configuration and troubleshooting is in [Project_Report.pdf](Project_Report.pdf).

## Skills Demonstrated

Linux server administration, DNS, mail services, TLS/PKI, web hosting, firewalling, intrusion prevention, VPN, honeypots, troubleshooting, technical documentation.
