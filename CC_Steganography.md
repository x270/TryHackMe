# CC: Steganography writeup
https://tryhackme.com/room/ccstego  
Learning steganography!
## [Task 1] Intro
#### Download zip file.
#### #1 (No question.)
No answer needed.
## [Task 2] Steghide
#### How to install.
```sh
# apt install steghide
```
#### View help.
```sh
# steghide --help
```
#### #1 What argument allows you to embed data(such as files) into other files?
embed
#### #2 What flag let's you set the file to embed?
-ef
#### #3 What flag allows you to set the "cover file"?(i.e the jpg)
-cf
#### #4 How do you set the password to use for the cover file?
-p
#### #5 What argument allows you to extract data from files?
extract
#### #6 How do you select the file that you want to extract data from?
-sf
#### #7 Given the passphrase "password123", what is the hidden message in the included "jpeg1" file.
```shell
# steghide extract -sf jpeg1.jpeg
Enter passphrase:
wrote extracted data to "a.txt".
```
``` cat a.txt```, then get flag.
## [Task 3] zsteg
#### How to install.
```sh
# apt install ruby-dev
# gem install zsteg
```
#### View help.
```sh
# zsteg -h
```
#### #1	How do you specify that the least significant bit comes first
--lsb
#### #2 What about the most significant bit?
--msb
#### #3 How do you specify verbose mode?
-v
#### #4 How do you extract the data from a specific payload?
-E
#### #5 In the included file "png1" what is the hidden message?
```sh
# zsteg png1.png
```
You can find the 9 letter FLAG.
#### #6 What about the payload used to encrypt it.
Displayed to the left of #5-Flag.
## [Task 4] Exiftool
#### How to install.
```sh
# apt install exiftool
```
#### View help.
```sh
# exiftool
```
#### #1	In the included jpeg3 file, what is the document name
```sh
# exiftool jpeg3.jpeg
```
Displayed in the "Document Name" line.
# [Task 5] Stegoveritas
How to install.
```sh
# pip3 install stegoveritas 
# stegoveritas_install_deps
```
View help.
```sh
# stegoveritas -h
```
#### #1 How do you check the file for metadata?
-meta
#### #2 How do you check for steghide hidden information
-steghide
#### #3 What flag allows you to extract LSB data from the image?
-extractLSB
#### #4 In the included image jpeg2 what is the hidden message?
```sh
# stegoveritas -steghide jpeg2.jpeg
```
```cat ./restlts/hoge.bin```, then get flag.
## [Task 6] Spectrograms
#### How to install.
Download and Install Sonic Visualiser from https://www.sonicvisualiser.org/download.html
#### #1	What is the hidden text in the included wav2 file?
Open wav2.wav -> Layer->Add Spectrogram
## [Task 7] The Final Exam
#### #1 What is key 1?
Get exam1.jpeg.
```shell
# steghide extract -sf exam1.jpeg
Enter passphrase:
steghide: could not extract any data with that passphrase!
```
I don't know the passphrase...  
Next, try exiftool.
```shell
# exiftool exam1.jpeg
```
Passphrase is displayed in the "Document Name" line.  
Then, try steghide again.
```shell
# steghide extract -sf exam1.jpeg
Enter passphrase:
wrote extracted data to "a.txt".
```
```cat a.txt```, then get Key 1.🙂
#### #2 What is key 2?
Get exam2.wav and open Sonic Visualiser.  
You can get the URL of imgur, so access it.  

Note! The end of the URL is `I5`. Neither `15` nor `l5`.😭😭😭😭😭
```shell
# zsteg KTrtNI5.png
```
Get the Key 2!😌

#### #3 What is key 3?
Get qrcode.png.
```sh
# stegoveritas ./qr/qrcode.png
```
There are many files in the results directory.  
Let's scan the QR code that looks proper with your smartphone.😅
