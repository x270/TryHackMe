# Task 7. 

## 開いているポートを確認する。
```bash
[x270@x270]$ rustscan -a 10.10.214.12
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: https://discord.gg/GFrQsGy           :
: https://github.com/RustScan/RustScan :
 --------------------------------------
Please contribute more quotes to our GitHub https://github.com/rustscan/rustscan

[~] The config file is expected to be at "/home/x270/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'.
Open 10.10.214.12:22
Open 10.10.214.12:80
[~] Starting Script(s)
[>] Script to be run Some("nmap -vvv -p {{port}} {{ip}}")

[~] Starting Nmap 7.91 ( https://nmap.org ) at 2021-11-26 23:27 JST
Initiating Ping Scan at 23:27
Scanning 10.10.214.12 [2 ports]
Completed Ping Scan at 23:27, 0.26s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 23:27
Completed Parallel DNS resolution of 1 host. at 23:27, 1.06s elapsed
DNS resolution of 1 IPs took 1.06s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating Connect Scan at 23:27
Scanning 10.10.214.12 [2 ports]
Discovered open port 22/tcp on 10.10.214.12
Discovered open port 80/tcp on 10.10.214.12
Completed Connect Scan at 23:27, 0.25s elapsed (2 total ports)
Nmap scan report for 10.10.214.12
Host is up, received conn-refused (0.25s latency).
Scanned at 2021-11-26 23:27:49 JST for 2s

PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack
80/tcp open  http    syn-ack

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 1.62 seconds
```

## 22番ポートで動いている製品を確認する
```bash
[x270@x270]$ rustscan -a 10.10.214.12 -p 22 -- -A -sC
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: https://discord.gg/GFrQsGy           :
: https://github.com/RustScan/RustScan :
 --------------------------------------
Nmap? More like slowmap.🐢

[~] The config file is expected to be at "/home/x270/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'.

Open 10.10.214.12:22
[~] Starting Script(s)
[>] Script to be run Some("nmap -vvv -p {{port}} {{ip}}")

[~] Starting Nmap 7.91 ( https://nmap.org ) at 2021-11-26 23:30 JST
NSE: Loaded 153 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:30
Completed NSE at 23:30, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:30
Completed NSE at 23:30, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:30
Completed NSE at 23:30, 0.00s elapsed
Initiating Ping Scan at 23:30
Scanning 10.10.214.12 [2 ports]
Completed Ping Scan at 23:30, 0.26s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 23:30
Completed Parallel DNS resolution of 1 host. at 23:30, 1.03s elapsed
DNS resolution of 1 IPs took 1.03s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating Connect Scan at 23:30
Scanning 10.10.214.12 [1 port]
Discovered open port 22/tcp on 10.10.214.12
Completed Connect Scan at 23:30, 0.26s elapsed (1 total ports)
Initiating Service scan at 23:30
Scanning 1 service on 10.10.214.12
Completed Service scan at 23:30, 0.52s elapsed (1 service on 1 host)
NSE: Script scanning 10.10.214.12.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:30
Completed NSE at 23:30, 7.52s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:30
Completed NSE at 23:30, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:30
Completed NSE at 23:30, 0.00s elapsed
Nmap scan report for 10.10.214.12
Host is up, received conn-refused (0.25s latency).
Scanned at 2021-11-26 23:30:36 JST for 10s

PORT   STATE SERVICE REASON  VERSION
22/tcp open  ssh     syn-ack OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   1024 e4:0c:5d:ac:5f:f9:58:9e:03:e4:ef:1c:62:74:24:2e (DSA)
| ssh-dss AAAAB3NzaC1kc3MAAACBAOwNjihF7s4cvOHtN7L07dS87/ZPEGec03h3KjDS9syR+5ndWGo6Z9I68oGAoBnRTNw/9F58k8y4oqVHdg4Xl1peNbBEXSi3hljd++749azOwYxu+VY2OGfrvE60Z5jmyARDqxOVueIWq99FCmQzWaAvJfiz+tO2rAH0uXsnV7jzAAAAFQCmntj0vx/JX21ceiXILD/SWkubUwAAAIBVcqVV9ofOvJOykmlGvms8n1cFo8Yn6yRh/BGJkz7LCAm9JrJSMTpkFd+Z3otq0JrsAiNNTz6yUI8MgQUGJUug4izysXgNvUpMG1x44TDUxiCRYpNdpyxWZ6FkTgPKNy1Iyc0JRjW99YBEJljYunhlUSNuJSP9eyMEOss7P/cZJQAAAIEAhgwDs23lC23GWcxvW6y+GwJhzdgUsQwesAbImPK4AcUH9dnTTlK5JnNDKeTCH4jcnjsNvuTOVWM2Drhbn4CqcEpVio5bjD7A4zBLcmqSuyYP1bW/2Db8k9v/DGKembJqgbXuJH8eKK0CyNV0DtF4Apfv4Fa/URvS+eD2DfoDJSo=
|   2048 2c:28:52:1f:ff:a1:2c:0b:d1:cd:6f:d4:21:0e:68:06 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDvNc9c9eK8mib0VpYPBhJR4WLcr7+ex+aOl2QYVEu3Agp/j5jy4FKyUfU662chXzYstr52qabimi70GuclHQFSDQXjAE4gcR5QFyDaqQSEiN0FqQxOqrQHtS5wczqqbt9gBphaZ34j/3DDMYNfE5Uz/ptiCo0VKkV0X2ybGSnKTiqUbSUu4I48QOSIlXPh7FD9siaENTLSW4q8HDdfWuu8OaQ4fqziQiOCyM8fGSz/6IOnkT6C0vBKxhJt/pq+Waifum6g8WouiXu8frSGQfatEsF7FxxYYZ+1+M5Fthcx4LJhJ608DCRvKMQQvWjjugqBSDDmB/aNmHKzWvrK1JST
|   256 92:fb:4c:de:f2:f3:a9:bb:eb:f2:cb:8f:53:29:43:7b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHgrKtcXjmZ2K2xuLxUQHK9Vx8zqpzXXsnYZGvd42CeHcOk3DYYIv8c6iIRRcBkbA25bKSalKNz1bHc0aXw5DdQ=
|   256 97:8a:86:1f:60:9a:ff:10:85:14:7e:35:8b:24:dc:7c (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMJSHcONqrU1iatZUpuM1pSOolYpjHDTmB7o3/SolANe
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:30
Completed NSE at 23:30, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:30
Completed NSE at 23:30, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:30
Completed NSE at 23:30, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.12 seconds
```

## 80番ポートを同様に確認する。
```bash
[x270@x270]$ rustscan -a 10.10.214.12 -p 80 -- -A -sC
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: https://discord.gg/GFrQsGy           :
: https://github.com/RustScan/RustScan :
 --------------------------------------
😵 https://admin.tryhackme.com

[~] The config file is expected to be at "/home/x270/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'.

Open 10.10.214.12:80
[~] Starting Script(s)
[>] Script to be run Some("nmap -vvv -p {{port}} {{ip}}")

[~] Starting Nmap 7.91 ( https://nmap.org ) at 2021-11-26 23:32 JST
NSE: Loaded 153 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:32
Completed NSE at 23:32, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:32
Completed NSE at 23:32, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:32
Completed NSE at 23:32, 0.00s elapsed
Initiating Ping Scan at 23:32
Scanning 10.10.214.12 [2 ports]
Completed Ping Scan at 23:32, 0.25s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 23:32
Completed Parallel DNS resolution of 1 host. at 23:32, 1.02s elapsed
DNS resolution of 1 IPs took 1.02s. Mode: Async [#: 1, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating Connect Scan at 23:32
Scanning 10.10.214.12 [1 port]
Discovered open port 80/tcp on 10.10.214.12
Completed Connect Scan at 23:32, 0.25s elapsed (1 total ports)
Initiating Service scan at 23:32
Scanning 1 service on 10.10.214.12
Completed Service scan at 23:32, 6.55s elapsed (1 service on 1 host)
NSE: Script scanning 10.10.214.12.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:32
Completed NSE at 23:32, 5.21s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:32
Completed NSE at 23:32, 1.01s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:32
Completed NSE at 23:32, 0.00s elapsed
Nmap scan report for 10.10.214.12
Host is up, received syn-ack (0.25s latency).
Scanned at 2021-11-26 23:32:02 JST for 15s

PORT   STATE SERVICE REASON  VERSION
80/tcp open  http    syn-ack Apache httpd 2.4.7 ((Ubuntu))
| http-cookie-flags:
|   /:
|     PHPSESSID:
|_      httponly flag not set
|_http-favicon: Unknown favicon MD5: 69C728902A3F1DF75CF9EAC73BD55556
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 1 disallowed entry
|_/
|_http-server-header: Apache/2.4.7 (Ubuntu)
| http-title: Login :: Damn Vulnerable Web Application (DVWA) v1.10 *Develop...
|_Requested resource was login.php

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 23:32
Completed NSE at 23:32, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 23:32
Completed NSE at 23:32, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 23:32
Completed NSE at 23:32, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.68 seconds
```
