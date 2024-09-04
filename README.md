# DVWA Vulnerability Exploration

Welcome to the DVWA (Damn Vulnerable Web Application) exploration repository. This repository documents the process of discovering and exploiting vulnerabilities within DVWA, a web application designed for learning and practicing web security.

## Table of Contents

1. [Introduction](#introduction)
2. [Setup and Installation](#setup-and-installation)
3. [Vulnerabilities Overview](#vulnerabilities-overview)
4. [Exploitation Techniques](#exploitation-techniques)
5. [References](#references)
6. [Contributing](#contributing)

## Introduction

DVWA is an intentionally vulnerable web application used for testing and learning about web application security. It contains a variety of vulnerabilities and provides an environment to practice and understand security flaws.

## Setup and Installation

### Prerequisites

- A web server (e.g., Apache)
- PHP
- MySQL or MariaDB
- DVWA source code

### Installation Steps

1. **Download DVWA:**

   ```sh
   git clone https://github.com/digininja/DVWA.git
   cd DVWA
   ```

2. **Configure DVWA:**

   - Copy the configuration file:

     ```sh
     cp config/config.inc.php.dist config/config.inc.php
     ```

   - Edit `config/config.inc.php` with your database details:

     ```php
     $_DVWA['db_server'] = 'localhost';
     $_DVWA['db_user']   = 'root';
     $_DVWA['db_password'] = 'password';
     $_DVWA['db_database'] = 'dvwa';
     ```

3. **Set up the database:**

   - Import the database schema from `dvwa.sql`:

     ```sh
     mysql -u root -p dvwa < dvwa.sql
     ```

4. **Set permissions:**

   ```sh
   chmod -R 777 hackable/
   ```

5. **Start the web server and navigate to DVWA in your browser.**

## Vulnerabilities Overview

This section outlines the main vulnerabilities present in DVWA and their descriptions. 

1. **SQL Injection**
2. **XSS (Cross-Site Scripting)**
3. **CSRF (Cross-Site Request Forgery)**
4. **File Upload Vulnerabilities**
5. **Command Injection**
6. **Insecure Direct Object References (IDOR)**

## Exploitation Techniques

For each vulnerability, document the exploitation techniques and steps:

### SQL Injection

- **Objective:** Extract data from the database.
- **Steps:**
  1. Access the SQL Injection vulnerability page.
  2. Input a malicious SQL payload in the input field.
  3. Observe the output and refine the payload to extract data.

### XSS (Cross-Site Scripting)

- **Objective:** Inject and execute scripts in the browser.
- **Steps:**
  1. Access the XSS vulnerability page.
  2. Input a JavaScript payload in the input field.
  3. Observe the script execution and test different payloads.

### CSRF (Cross-Site Request Forgery)

- **Objective:** Perform unauthorized actions on behalf of a user.
- **Steps:**
  1. Create a CSRF exploit page.
  2. Include a form that performs an action in DVWA.
  3. Trick the victim into submitting the form.

### File Upload Vulnerabilities

- **Objective:** Upload and execute malicious files.
- **Steps:**
  1. Access the file upload functionality.
  2. Upload a file with a malicious payload.
  3. Execute the uploaded file to verify its execution.

### Command Injection

- **Objective:** Execute arbitrary commands on the server.
- **Steps:**
  1. Access the command injection vulnerability page.
  2. Input a payload that executes system commands.
  3. Observe the command output and refine your payload.

### Insecure Direct Object References (IDOR)

- **Objective:** Access unauthorized resources.
- **Steps:**
  1. Identify object references in URLs or forms.
  2. Modify the references to access different resources.
  3. Observe the results and test different references.

## References

- [DVWA Official GitHub Repository](https://github.com/digininja/DVWA)
- [OWASP](https://owasp.org/)
- [Common Vulnerability Exploitation Techniques](https://www.owasp.org/index.php/Category:OWASP_Top_Ten)

## Contributing

Feel free to contribute by creating issues, submitting pull requests, or improving documentation.

---

**Disclaimer:** This repository is intended for educational purposes only. Use DVWA and the techniques discussed here in a legal and ethical manner.

```
