# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services

Nmap scan results for each machine reveal the below services and OS details:

```bash
$ nmap -sV 192.168.1.0/24
```
![Nmap scan results](/Images/Nmap-scan1.jpg)
![](/Images/Nmap-scan2.jpg)

This scan identifies the services below as potential points of entry:

**Target 1 (192.168.1.110)**

  - Port 22/TCP 	    Open 	SSH
  - Port 80/TCP 	    Open 	HTTP
  - Port 111/TCP 	Open 	rcpbind
  - Port 139/TCP 	Open 	netbios-ssn
  - Port 445/TCP 	Open 	netbios-ssn


_TODO: Fill out the list below. Include severity, and CVE numbers, if possible._

The following vulnerabilities were identified on each target:

**Target 1**

  - User Enumeration (WordPress site) - easily found with `wpscan`
  - Weak User Password - user Michael using "Michael" as their password 
  - Unsalted User Password Hash (WordPress database)
  - Misconfiguration of User Privileges/Privilege Escalation - Python command can be easily used to gain root access. 


_TODO: Include vulnerability scan results to prove the identified vulnerabilities._

![User Enumeration WPscan](/Images/wpscan.jpg)
![User Enumeration Users](/Images.wp-enumeration.jpg)

### Exploitation
_TODO: Fill out the details below. Include screenshots where possible._

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: _TODO: Insert `flag1.txt` hash value_
    - **Exploit Used**
      - _TODO: Identify the exploit used_
      - _TODO: Include the command run_
  - `flag2.txt`: _TODO: Insert `flag2.txt` hash value_
    - **Exploit Used**
      - _TODO: Identify the exploit used_
      - _TODO: Include the command run_
