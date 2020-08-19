# Custom Wordlist writeup
https://tryhackme.com/room/customwordlists  
設問ごとにパスワード付きzipが渡されるので、クラックして、中のファイルに書いているFLAGを回答する。

## Task 1
No question.

## Task 2
No question.

## Task 3

### #1 Get flag 1
パスワードは8文字以上。rockyouから8文字以上のものだけ抜き出した新しいwordlistを作る。
```shell
[root@kali]# cat rockyou.txt | grep -x '.\{8,\}' > uppoer8_len_list
```

作成したwordlistで総当たりする。
```shell
[root@kali]# zip2john flag1.txt.zip > flag1hash
ver 81.9 flag1.txt.zip/flag1.txt is not encrypted, or stored with non-handled compression type
[root@kali]# john flag1hash -wordlist=uppoer8_len_list
Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 256/256 AVX2 8x])
Press 'q' or Ctrl-C to abort, almost any other key for status
🆖🆖🆖🆖🆖🆖🆖🆖🆖🆖    (flag1.txt.zip/flag1.txt)
1g 0:00:00:35 DONE (2020-08-19 22:25) 0.02805g/s 21213p/s 21213c/s 21213C/s megstir26..megmeg05
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

### #2 Get flag 2
パスワードは大文字を1つ以上含む。
```shell
[root@kali]# cat rockyou.txt | grep -o '[^ ]*[A-Z][^ ]*' > contains_uppercase
[root@kali]# zip2john flag2.txt.zip > flag2hash
[root@kali]# john flag2hash -wordlist=contains_uppercase
```

### #3 Get flag 3
パスワードは10文字以上、大文字を含み、記号を含む。
```shell
[root@kali]# cat rockyou.txt | grep -x '.\{10,\}' | grep -o '[^ ]*[A-Z][^ ]*' | grep -v "^[A-Za-z0-9]*$" > newlist
[root@kali]# zip2john flag3.txt.zip > flag3hash
[root@kali]# john flag3hash -wordlist=newlist
```

## Task 4

### #1 Get flag 4
最初の1文字だけ大文字に置換する。
```shell
[root@kali]# sed -e "s/\b\(.\)/\u\1/g" rockyou.txt > 1upper.txt
[root@kali]# zip2john flag4.txt.zip > flag4hash
[root@kali]# john flag4hash -wordlist=1upper.txt
```

### #2 Get flag5
記号を含むと言われるが、ヒントを見ると`!`だけ試せばよいらしい。  
末尾に`!`を付加する。
```shell
[root@kali]# sed -e 's/$/!/' rockyou.txt > exclamation.txt
[root@kali]# zip2john flag5.txt.zip > flag5hash
ver 81.9 flag5.txt.zip/flag5.txt is not encrypted, or stored with non-handled compression type
[root@kali]# john flag5hash -wordlist=exclamation.txt
```

## Task 5 Generating Wordlists

手順に沿って`HashcatRulesEngine`を導入。
```shell
[root@kali]# git clone https://github.com/llamasoft/HashcatRulesEngine
Cloning into 'HashcatRulesEngine'...
remote: Enumerating objects: 52, done.
remote: Total 52 (delta 0), reused 0 (delta 0), pack-reused 52
Unpacking objects: 100% (52/52), 40.32 KiB | 166.00 KiB/s, done.
[root@kali]# cd HashcatRulesEngine/
[root@kali]# make
gcc -O2 -std=c99 -march=native   -Wall -Wextra -funsigned-char -Wno-pointer-sign -Wno-sign-compare -c rules.c -o rules.o
rules.c: In function ‘parse_rule’:
rules.c:745:28: warning: implicit declaration of function ‘asprintf’; did you mean ‘vsprintf’? [-Wimplicit-function-declaration]
  745 |             new_rule_len = asprintf(&new_rule,
      |                            ^~~~~~~~
      |                            vsprintf
gcc -O2 -std=c99 -march=native   -Wall -Wextra -funsigned-char -Wno-pointer-sign -Wno-sign-compare -c hcre.c -o hcre.o
gcc -O2 -std=c99 -march=native   -Wall -Wextra -funsigned-char -Wno-pointer-sign -Wno-sign-compare rules.o hcre.o -o hcre
```
手順に沿ってRuleを2つ入手。
```shell
[root@kali]# wget https://contest-2010.korelogic.com/hashcat-rules/KoreLogicRulesL33t.rule
[root@kali]# wget https://contest-2010.korelogic.com/hashcat-rules/KoreLogicRulesAppendMonthCurrentYear.rule
```
`HorseShark`を元としたパスワードリストを要求されるので、2つのルールを適用して作成する。
```shell
[root@kali]# echo HorseShark > HorseShark.txt
[root@kali]# ./hcre KoreLogicRulesL33t.rule KoreLogicRulesAppendMonthCurrentYear.rule < HorseShark.txt > new_wordlist.txt
[root@kali]# cat new_wordlist.txt
horsesh@rk
Horsesh@rk
horsesh4rk
Horsesh4rk
hors3shark
：
HorseSharkjan2010
HorseSharkJan2010
HorseSharkfeb2010
HorseSharkFeb2010
：
HorseSharkDec2010
```
作成したリストを使ってクラックする。
```shell
[root@kali]# zip2john flag6.txt.zip > flag6hash
ver 81.9 flag6.txt.zip/Flag6.txt is not encrypted, or stored with non-handled compression type
[root@kali]# john flag6hash -wordlist=new_wordlist.txt
```
