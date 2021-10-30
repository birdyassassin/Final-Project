# Blue Team: Summary of Operations

## Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further

### Network Topology

The following machines were identified on the network:

**Kali**
- Operating System: 
    - Debian Kali Linux 5.4.0
- Purpose: 
    - The Penetration Tester
- IP Address: 
    - 192.168.1.90

**ELK**
- Operating System: 
    - Ubuntu 18.04
- Purpose: 
    - The ELK Stack
- IP Address: 
    - 192.168.1.100

**Target 1**
- Operating System: 
    - Debian GNU/Linux 8
- Purpose: 
    - The WordPress Host
- IP Address: 
    - 192.168.1.110

**Capstone**
- Operating System: 
    - Ubuntu 18.04
- Purpose: 
    - The Vulnerable Web Server
- IP Address: 
    - 192.168.1.105

**Network Diagram:**

![Network Diagram](/Images/final-project-network-diagram.png "Network Diagram")


### Description of Targets

The target of this attack was: `Target 1` (192.168.1.110).

Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:

- Excessive HTTP Errors
- HTTP Request Size Monitor
- CPU Usage Monitor



### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

#### Excessive HTTP Errors

Excessive HTTP Errors is implemented as follows:
  - **Metric**: WHEN count() GROUPED OVER top 5 'http.response.status_code'
  - **Threshold**: IS ABOVE 400
  - **Vulnerability Mitigated**: Enumeration/Brute Force
  - **Reliability**: The alert is highly reliable. Measuring by error codes 400 and above will filter out any normal or successful responses. 400+ codes are client and server errors which are of more concern. Especially when taking into account these error codes going off at a high rate.


#### HTTP Request Size Monitor
HTTP Request Size Monitor is implemented as follows:
  - **Metric**: WHEN sum() of http.request.bytes OVER all documents
  - **Threshold**:  IS ABOVE 3500
  - **Vulnerability Mitigated**: Code injection in HTTP requests (XSS and CRLF) or DDOS
  - **Reliability**: TODO: Alert could create false positives. It comes in at a medium reliability. There is a possibility for a large non malicious HTTP request or legitimate HTTP traffic.

#### CPU Usage Monitor
CPU Usage Monitor is implemented as follows:
  - **Metric**: WHEN max() OF system.process.cpu.total.pct OVER all documents
  - **Threshold**: IS ABOVE 0.5
  - **Vulnerability Mitigated**: Malicious software, programs (malware or viruses) running taking up resources
  - **Reliability**: The alert is highly reliable. Even if there isn’t a malicious program running this can still help determine where to improve on CPU usage.


### Suggestions for Going Further

- Each alert above pertains to a specific vulnerability/exploit. Recall that alerts only detect malicious behavior, but do not stop it. For each vulnerability/exploit identified by the alerts above, suggest a patch. E.g., implementing a blocklist is an effective tactic against brute-force attacks. It is not necessary to explain _how_ to implement each patch.

The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats, identified by the alerts above. In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:
- Vulnerability 1
  - **Patch**: Inastall the free plugin `WP-Hardening` for WordPress from `https://wordpress.org/plugins/wp-security-hardening/`. Once installed got to the 'Security Fixers' tab and select `Stop user enumeration` toggle.
  - **Why It Works**: TODO: This plugin selection works by automatically stopping user enumeration and brute force style attacks on the WP site that attackers use to grab the users names. 
- Vulnerability 2
  - **Patch**: The DIY approach is to disable exploitable APIs such as XML-RPC which is only used for interacting with external third-party apps. The simple way is to install a plugin called `Protection Against DDOS` from `https://wordpress.org/plugins/protection-against-ddos/`. This allows you to simply click on known vulnerabilities to restrict.
  - **Why It Works**: This plugin works by doing all it's checks via the `.htaccess` file and stops malicious requests at the web server level before they reach the WordPress site.
- Vulnerability 3
  - **Patch**: Adding a good antivirus or Host Based Intrusion Detection System (eg. Snort)
  - **Why It Works**: Antiviruses specialize in removal, detection and overall prevention of malicious threats against computers and servers. HIDS monitors and analyzes internals of computing systems. 
