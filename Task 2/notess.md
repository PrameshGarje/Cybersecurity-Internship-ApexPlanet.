Internship Task 2: Network Security & Scanning Report
1. Objective
The goal of this task was to perform a comprehensive security audit of a networked host. This involved reconnaissance, vulnerability identification using automated tools, packet-level analysis of network traffic, and implementing host-based firewall rules to secure services.

2. Lab Environment
Attacker Machine: Kali Linux

Target Machine: Metasploitable2 (Vulnerable Linux VM)

Network Type: Host-Only (Isolated Laboratory)

Target IP Address: 192.168.56.101

3. Reconnaissance & Discovery
I began by identifying active hosts and services to map the attack surface.

Passive Reconnaissance
Used tools like whois and nslookup to understand domain structures and public DNS records (simulated for lab environment).

Active Reconnaissance
Executed a ping sweep and banner grabbing to identify the live target.
# Identifying the target on the network
nmap -sn 192.168.56.0/24

# Banner grabbing to identify service versions
nc -v 192.168.56.101 21
Port Scanning (Nmap)
I performed multiple layers of scanning to find entry points.
Scan Type	Purpose	Command Used
Stealth Scan	Identify open ports without finishing handshake	sudo nmap -sS 192.168.56.101
Service Audit	Detect versions of running software	sudo nmap -sV 192.168.56.101
Aggressive Scan	OS Detection, Scripts, and Traceroute	sudo nmap -A -T4 192.168.56.101

Key Findings:

Port 21 (FTP): vsftpd 2.3.4 (Vulnerable to backdoor)

Port 23 (Telnet): Insecure plaintext communication active.

Port 80 (HTTP): Apache 2.2.8 running.

5. Vulnerability Assessment (Nessus)
An uncredentialed scan was conducted using Nessus to categorize system risks.

Critical Risk: Ubuntu 8.04 End-of-Life (EOL). The system no longer receives security updates.

Critical Risk: VNC Server Default Password. The VNC service uses 'password', allowing full remote desktop access.

High Risk: Samba 'Badlock' Vulnerability. Allows for man-in-the-middle attacks on file shares.

Remediation: It is highly recommended to decommission this OS and upgrade to a modern, supported Linux kernel.

6. Packet Analysis (Wireshark)
Using Wireshark, I monitored the network interface to analyze how data is transmitted.

Filter used for HTTP: http

Filter for TCP Handshakes: tcp.flags.syn == 1

I observed that Telnet and HTTP traffic are sent in plaintext, meaning any attacker on the network can see login credentials using a simple packet sniffer.

7. Firewall Implementation (iptables)
To secure the target, I demonstrated the use of iptables to block unauthorized access to the web server (Port 80).

Check Status: nmap -p 80 192.168.56.101 (Result: Open)

Apply Block:
sudo iptables -A INPUT -p tcp --dport 80 -j DROP
Verify Block: nmap -p 80 192.168.56.101 (Result: Filtered)

Restore Access:
sudo iptables -D INPUT -p tcp --dport 80 -j DROP

8. Conclusion
Task 2 successfully demonstrated the workflow of a network security professional. I moved from initial discovery to vulnerability analysis and finished with a defensive configuration to mitigate unauthorized access.
