Enterprise Linux Infrastructure Deployment
This project details the configuration of a secure, multi-service network infrastructure built from scratch on Rocky Linux (Server) and Ubuntu (Client). It simulates a real-world corporate environment, integrating DNS, Email, Web, and Intrusion Prevention services.

🛠️ Tech Stack
Hypervisor: Oracle VirtualBox
Server OS: Rocky Linux 9
Client OS: Ubuntu Linux
DNS: BIND9
Mail: Postfix (SMTP), Dovecot (IMAP/POP3)
Web: Apache HTTPD (mod_ssl)
Security: Fail2ban, Firewalld, OpenSSL (Self-signed CA)
🏗️ Architecture Overview
Server FQDN: maliserver.bungkus.org (192.168.200.4)
Client FQDN: maliclient.bungkus.org (192.168.200.3)
Domain: bungkus.org
⚙️ Configurations
1. DNS Server (BIND9)
Configured forward (bungkus.org) and reverse (200.168.192.in-addr.arpa) lookup zones.
Implemented MX records for mail routing and A records for web virtual hosts.
Verified resolution from the Ubuntu client using dig, nslookup, and ping after removing static /etc/hosts entries.
Files: DNS Directory
2. Secure Mail Server (Postfix + Dovecot)
Deployed Postfix and Dovecot with Maildir format.
Enabled SSL/TLS encryption for SMTP (Port 465) and IMAP (Port 993).
Integrated Dovecot SASL authentication for Postfix.
Verified end-to-end mail delivery between two distinct users via Mozilla Thunderbird client.
Files: Mail Server Directory
3. Web Server (Apache + SSL)
Hosted two virtual hosts (maliserver.bungkus.org and intranet.bungkus.org).
Enforced HTTP to HTTPS permanent redirects.
Generated and deployed self-signed SSL/TLS certificates.
Files: Web Server Directory
4. Intrusion Prevention (Fail2ban)
Configured sshd, dovecot, and recidive jails.
Integrated with Firewalld using the firewallcmd-ipset backend.
Successfully tested live brute-force attack mitigation from the client machine, confirming automated IP banning and unbanning.
Files: Security Directory
🐛 Troubleshooting Highlights
During deployment, 13 distinct integration issues were identified and resolved independently. Notable examples include:

Thunderbird Snap Confinement: Identified that Ubuntu's Snap version of Thunderbird was sandboxed from the system certificate store. Resolved by migrating to the .deb PPA release.
Postfix SASL Integration: Diagnosed SMTP authentication failures by tracing them to a missing unix_listener socket in Dovecot's 10-master.conf and Cyrus SASL being enabled instead of Dovecot SASL.
Firewall SMTPS Port: Identified that port 465 (SMTPS) was not listening because the service definition was commented out in master.cf, and the port was missing from firewalld rules.
📄 Full Documentation
For a comprehensive, step-by-step breakdown of the installation, configuration, and troubleshooting process, please read the full project report:
https://github.com/tanmay1802-art/-linux-enterprise-infrastructure/blob/main/Project_Report.pdf
