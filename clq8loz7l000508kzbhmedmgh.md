---
title: "The scanning phase"
seoTitle: "Scanning Phase Unveiled: Navigating Cybersecurity with Powerful Tools"
seoDescription: "Delve into the Scanning Phase of cybersecurity using cutting-edge tools like Nmap,Nikto ... Uncover open ports, vulnerabilities, and network intricacies."
datePublished: Sat Dec 16 2023 21:59:13 GMT+0000 (Coordinated Universal Time)
cuid: clq8loz7l000508kzbhmedmgh
slug: the-scanning-phase
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/lVF2HLzjopw/upload/49c9e4bcd294ef392eccd166351d8d1e.jpeg
tags: security, hacking, cybersecurity, cybersecurity-1, cyberattack

---

Following reconnaissance, the scanning phase is a pivotal step in penetration testing, aimed at identifying open ports, services, and potential vulnerabilities within the target system or network. During this phase, attackers use various tools to scan the target network and identify vulnerabilities. Here are some of the most useful tools for the scanning phase:

## Nmap

NMap, short for "Network Mapper" is a popular network scanning tool used in penetration testing. It performs host discovery, port scanning, and service enumeration to gather information about the target system's network infrastructure.

The Nmap command is hi\`ghly versatile, allowing users to customize scans based on specific needs and objectives.

1. `nmap <ip adress>`
    
    This will perform a default scan of the system at the given IP address. It will detect open ports and the services running on those ports.
    
2. `nmap -sn [192.168.1.0/24]`
    
    This is a no port scan or ping scan. Nmap will only look for active hosts within the network, without scanning any ports.
    
3. `nmap -sS <ip adress>`
    
    This performs a stealthier SYN scan. It is slower but can evade some firewalls and IDS systems.
    
4. `nmap -sV <ip adress>`
    
    This performs service and version detection to identify the exact services and versions running on open ports.
    
5. `nmap -A <ip adress>`
    
    This performs an aggressive scan that enables OS detection (-O), version detection (-sV), script scanning (-sC) and traceroute (--traceroute).
    
6. `nmap -p80,443 <ip adress>`
    
    This will scan only port 80 and 443 to check if a web server is running.
    
7. `nmap — script vuln <ip adress>`
    
    This command will look for potentials vulnerabilities on the machine with more informations including the CVE associated. Noted that — before the script in the command is two dashes.
    
    These are just a few examples, and Nmap provides a multitude of options and configurations. <mark>Ensure proper authorization before scanning any network, as unauthorized scanning may be considered malicious and could lead to legal consequences</mark>. Always follow ethical guidelines and respect the privacy and security of the systems you're scanning.
    
    Nmap has many other useful options to perform more stealthy scans, detect firewall settings, exclude hosts, scan IPv6 addresses, and more. More information can be found on the official [site](https://nmap.org/).
    

## Nikto

Nikto is an open-source web server scanner that performs comprehensive tests against web servers for multiple items, including potential security vulnerabilities.

Nikto can be downloaded from the official GitHub repository [Nikto GitHub](https://github.com/sullo/nikto).

1. `nikto <ip adress>`
    
    By default, Nikto performs a basic scan against the specified target. This includes checks for common vulnerabilities, outdated server software, and misconfigurations.
    
2. `nikto -h <ip adress> -o nikto_scan.txt`
    
    This command saves the output to a file named nikto\_scan.txt.
    
3. `nikto -h <target> -p 8080`
    
    Nikto allows you to customize the scan based on your requirements. Here you can specify a custom port for the scan (e.g., port 8080)
    
4. `nikto -h`
    
    For a full list of options and command
    

## Wireshark

Wireshark is a network protocol analyzer that allows you to capture and analyze the data traveling back and forth on a network. It's useful for understanding network traffic and identifying potential security issues

## Zenmap (Nmap GUI)

Zenmap is the official graphical user interface for Nmap. It provides a visual representation of the data collected during a network scan and allows for easier analysis.

## Conclusion

In short, the scanning phase is super important in penetration testing. Whether you're using the flexible Nmap or the powerful Metasploit, penetration testers are key to boosting cybersecurity. Staying responsible, always learning, and keeping ethics in mind are the main rules as we dive into the constantly changing world of cybersecurity.