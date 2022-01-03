# Subdomain enumeration

## Task1 Brief

サブドメインを探す主な手法は3つ。
- Brute force
- OSINT
- Virtual Host

## Task2 OSINT SSL/TLS証明書

認証局（CA）によってドメインに対するSSL/TLS証明書が作成されると、CAは証明書の透明性ログ（CT）を作成する。CTは下記のサイトなどで確認できる。

- https://crt.sh/   
- https://transparencyreport.google.com/https/certificates

## Task3 OSINT 検索エンジン

Googleでは`site:`や`-site:`を使用した検索ができる。  
`-site:www.tryhackme.com  site:*.tryhackme.com`

## Task4 DNS Bruteforce

一般的に使われるサブドメインを総当たりで試して特定する。  
Kalilinuxには`dnsrecon`が組み込まれている。

```shell
user@thm:~$ dnsrecon -t brt -d acmeitsupport.thm
[*] No file was specified with domains to check.
[*] Using file provided with tool: /usr/share/dnsrecon/namelist.txt
[*]     A api.acmeitsupport.thm 10.10.10.10
[*]     A www.acmeitsupport.thm 10.10.10.10
[+] 2 Record Found
```

## Task5  OSINT Sublist3r

OSINTによるサブドメイン検出の高速化のために`Sublist3r`を使う。  
- https://github.com/aboul3la/Sublist3r

```shell  
user@thm:~$ ./sublist3r.py -d acmeitsupport.thm

          ____        _     _ _     _   _____
         / ___| _   _| |__ | (_)___| |_|___ / _ __
         \___ \| | | | '_ \| | / __| __| |_ \| '__|
          ___) | |_| | |_) | | \__ \ |_ ___) | |
         |____/ \__,_|_.__/|_|_|___/\__|____/|_|

         # Coded By Ahmed Aboul-Ela - @aboul3la

[-] Enumerating subdomains now for acmeitsupport.thm
[-] Searching now in Baidu..
[-] Searching now in Yahoo..
[-] Searching now in Google..
[-] Searching now in Bing..
[-] Searching now in Ask..
[-] Searching now in Netcraft..
[-] Searching now in Virustotal..
[-] Searching now in ThreatCrowd..
[-] Searching now in SSL Certificates..
[-] Searching now in PassiveDNS..
[-] Searching now in Virustotal..
[-] Total Unique Subdomains Found: 2
web55.acmeitsupport.thm
www.acmeitsupport.thm
user@thm:~$
```

## Task6 Virtual Host
ファジングツール`ffuf`を使用した調査。
- https://github.com/ffuf/ffuf
- https://jpn.nec.com/cybersecurity/blog/210604/index.html

サブメインのnamelistはここから拝借。
- https://github.com/danielmiessler/SecLists

```shell
[root@405g6]# ./ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://10.10.117.123

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.3.1-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.117.123
 :: Wordlist         : FUZZ: /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt
 :: Header           : Host: FUZZ.acmeitsupport.thm
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
________________________________________________

0                       [Status: 200, Size: 2395, Words: 503, Lines: 52, Duration: 246ms]
about                   [Status: 200, Size: 2395, Words: 503, Lines: 52, Duration: 247ms]
20                      [Status: 200, Size: 2395, Words: 503, Lines: 52, Duration: 247ms]
17                      [Status: 200, Size: 2395, Words: 503, Lines: 52, Duration: 248ms]
ilmi                    [Status: 200, Size: 2395, Words: 503, Lines: 52, Duration: 248ms]
abc                     [Status: 200, Size: 2395, Words: 503, Lines: 52, Duration: 249ms]
```

そのまま実行すると、存在しないサブドメインも結果に含まれてしまう。  
Words 503より、エラーと思われる場合にSize: 2395なので、これを無視する`-fs 2395`を付加して再実行。

```shell
[root@405g6]# ./ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://10.10.117.123  -fs 2395

        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.3.1-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.10.117.123
 :: Wordlist         : FUZZ: /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt
 :: Header           : Host: FUZZ.acmeitsupport.thm
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
 :: Filter           : Response size: 2395
________________________________________________

delta                   [Status: 200, Size: 51, Words: 7, Lines: 1, Duration: 246ms]
yellow                  [Status: 200, Size: 56, Words: 8, Lines: 1, Duration: 250ms]
:: Progress: [1907/1907] :: Job [1/1] :: 160 req/sec :: Duration: [0:00:12] :: Errors: 0 ::
```
