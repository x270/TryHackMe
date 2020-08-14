# Gotta Catch'em All! writeup
https://tryhackme.com/room/pokemon

### 事前調査
開放ポートの確認  
```shell
nmap -sC -sV -Pn [TargetIP]
```

`22 SSH`と`80 HTTP`が開いていることを確認。  

#### 80番ポート
ブラウザからアクセス。  
Apache2のデフォルト画面のように見えるが、HTMLソースにポケモンの名前が書かれている。  
```HTML
 <script type="text/javascript">
    	const randomPokemon = [
    		'Bulbasaur', 'Charmander', 'Squirtle',
    		'Snorlax',
    		'Zapdos',
    		'Mew',
    		'Charizard',
    		'Grimer',
    		'Metapod',
    		'Magikarp'
    	];
    	const original = randomPokemon.sort((pokemonName) => {
    		const [aLast] = pokemonName.split(', ');
    	});

    	console.log(original);
    </script>
```
また、最下部の方に変なタグと怪しいコメントがある。  
`<pokemon>:<xxxxxxxxxxxxxxx>`が`:`で区切られていることからIDとPWと推測。

```
        <pokemon>:<xxxxxxxxxxxxxxx>
        	<!--(Check console for extra surprise!)-->
```

#### 22番ポート
先ほど見つけたIDとPWでSSHログインできる。  
```shell
ssh pokemon@[TargetIP]
```

## #1	Find the Grass-Type Pokemon
```shell
pokemon@root:~$ ls
Desktop  Documents  Downloads  examples.desktop  Music  Pictures  Public  Templates  Videos
pokemon@root:~$ pwd
/home/pokemon
pokemon@root:~$ cd Desktop/
pokemon@root:~/Desktop$ ls
P0kEmOn.zip
pokemon@root:~/Desktop$ unzip P0kEmOn.zip
Archive:  P0kEmOn.zip
   creating: P0kEmOn/
  inflating: P0kEmOn/grass-type.txt
pokemon@root:~/Desktop$ cd P0kEmOn/
pokemon@root:~/Desktop/P0kEmOn$ ls
grass-type.txt
pokemon@root:~/Desktop/P0kEmOn$ cat grass-type.txt
50 6f 4b 65 4d 6f 4e 7b 42 75 6c 62 61 73 61 75 72 7d
```
`grass-type.txt`に16進コードで書かれた文字列があるので、戻すと正解。


## #2	Find the Water-Type Pokemon
1問目と問題文からファイル名を`water-type.txt`と推測。  
```shell
pokemon@root:~/Desktop/P0kEmOn$ find / -name water-type.txt 2> /dev/null
/var/www/html/water-type.txt
pokemon@root:~/Desktop/P0kEmOn$ cat /var/www/html/water-type.txt
Ecgudfxq_EcGmP{Ecgudfxq}
```
ROT13ではなかったが、単換字式暗号ぽい。  
14文字ずらすと最初に見たHTMLソースに載っているポケモンの名前が出てくる。

## #3	Find the Fire-Type Pokemon
同様に`fire-type.txt`を探すが見つからない。

```shell
pokemon@root:~$ ls -R
:
:
./Videos:
Gotta

./Videos/Gotta:
Catch

./Videos/Gotta/Catch:
Them

./Videos/Gotta/Catch/Them:
ALL!

./Videos/Gotta/Catch/Them/ALL!:
Could_this_be_what_Im_looking_for?.cplusplus
```
いかにもなファイルが見つかるので開いてみる。
```
pokemon@root:~/Videos/Gotta/Catch/Them/ALL!$ cat Could_this_be_what_Im_looking_for\?.cplusplus
# include <iostream>

int main() {
        std::cout << "ash : xxxxxxxx"
        return 0;
```

ユーザを`ash`にスイッチ。  ちなみに`ash`は日本のポケモンで言うところの`サトシ`。
```shell
ash@root:/home$ find / -name *type.txt 2>/dev/null
/var/www/html/water-type.txt
/etc/why_am_i_here?/fire-type.txt
/home/pokemon/Desktop/P0kEmOn/grass-type.txt
ash@root:/home$ cat /etc/why_am_i_here?/fire-type.txt
UDBrM20wbntDaGFybWFuZGVyfQ==
```
Base64で戻すと正解。

## #4	Who is Root's Favorite Pokemon?
```shell
ash@root:/home$ ls
ash  pokemon  roots-pokemon.txt
ash@root:/home$ cat roots-pokemon.txt
```
ここはファイルの内容がそのまま正解。
