# DIGITAL FORENSICS

# 1. Hide and Seek
>Description:

Sakamoto’s at it again with a game of hide and seek, but this time, it’s not with Shin or his daughter. An old friend hid some secret data in this image. Can you find it before the others do?

>Hint:
Even in retirement, Sakamoto never loses at hide and seek. Maybe stegseek can help you keep up.

## Solution:
1. First of all, we are given a .jpg file, in the question.
2. On reading the challenge description and the hint, I felt there was something to do with `steg`, bcz of `stegseek` term used in the descirption.
3. So, I started with executing commands following stegseek's syntax, on the file, sakamoto.jpg. 
```
rozakk@DESKTOP-JPQGP4K:~/custom$ stegseek sakamoto.jpg
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[!] error: could not open the wordlist "/usr/share/wordlists/rockyou.txt".
```
4. The message meant that : StegSeek couldn’t find the list of passwords (rockyou.txt) it needs to try while searching for the hidden message.
5. So after many failing attempts with stegseek, and bit more research on stegseek, I came across `steghide`.
6. The problem with steghide was that it needs a passphrase, for its execution. So, I needed to find the passphrase now.
7. I tried many trial and error methods to try and guess the passphrase from the description and the hint given.
```
rozakk@DESKTOP-JPQGP4K:~/custom$ steghide extract -sf sakamoto.jpg -p sakamoto
steghide: could not extract any data with that passphrase!
rozakk@DESKTOP-JPQGP4K:~/custom$ steghide extract -sf sakamoto.jpg -p secret
steghide: could not extract any data with that passphrase!
rozakk@DESKTOP-JPQGP4K:~/custom$ steghide extract -sf sakamoto.jpg -p flag
steghide: could not extract any data with that passphrase!
rozakk@DESKTOP-JPQGP4K:~/custom$ steghide extract -sf sakamoto.jpg -p stegseek
steghide: could not extract any data with that passphrase!
rozakk@DESKTOP-JPQGP4K:~/custom$ steghide extract -sf sakamoto.jpg -p stegseek
steghide: could not extract any data with that passphrase!
rozakk@DESKTOP-JPQGP4K:~/custom$ steghide extract -sf sakamoto.jpg -p cupnoodles
steghide: could not extract any data with that passphrase!
rozakk@DESKTOP-JPQGP4K:~/custom$ steghide extract -sf sakamoto.jpg -p hideandseek
steghide: could not extract any data with that passphrase!
rozakk@DESKTOP-JPQGP4K:~/custom$ steghide extract -sf sakamoto.jpg -p old
steghide: could not extract any data with that passphrase!
rozakk@DESKTOP-JPQGP4K:~/custom$ steghide extract -sf sakamoto.jpg -p retirement
steghide: could not extract any data with that passphrase!
```
8. But, it didn't work out. 
9. Therefore, I downloaded `rockyou.txt`, inorder to compare more passwords to break-through
```
rozakk@DESKTOP-JPQGP4K:~/custom$ wget https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt
```

10. Then, I decided to create a shell script, to check each password from rockyou.txt and output the working password.
```
rozakk@DESKTOP-JPQGP4K:~/custom$ while read -r pw; do
>   steghide extract -sf sakamoto.jpg -p "$pw" -xf extracted.txt 2>/dev/null
>   [ -f extracted.txt ] && { echo "Found: $pw"; break; }
> done < rockyou.txt
Found: iloveyou1
```
11. Thus, I found that the password is : iloveyou1 . 
12. Now, I pass the passphrase and a file `flag.txt` gets pushed out. 
13. On opening the flag.txt file using cat, the flag is revealed 
<img width="761" height="266" alt="1 1" src="https://github.com/user-attachments/assets/d8035e99-5436-4dbe-8a94-a50d9a9da65e" />



## Flag:
```
nite{h1d3_4nd_s33k_but_w1th_st3g_sdfu9s8}
```

## Concepts Learnt:
- stegseek is a tool used to extract hidden data from images.
- steghide is a tool used to encode data into an image.
- rockyou.txt is one of the most popular and commonly used password wordlists. It contains millions of real passwords leaked from the old RockYou website breach and is now used in cybersecurity and CTFs for testing or cracking password-protected files.

## Resources:
- https://www.keepersecurity.com/blog/2023/08/04/understanding-rockyou-txt-a-tool-for-security-and-a-weapon-for-hackers/ - To learn about rockyou.txt wordlist

***

# 2. Nutrela Chunks
>Description:

One of my favorite foods is soya chunks. But as I was enjoying some Nutrela today, I noticed a few chunks weren’t quite right. Seems like something’s off with their structure. Could you help me fix these broken chunks so I can enjoy my meal again?

## Solution:
1. From reading the challenge description, I got some suspicious clues like there is something to do with the file structure in this challenge.
2. On downloading and opening the .png file, they gave in the challenge, the content in the file wasn't visible bcz something like file format error is there, as per the messsage from the image viewer software.
3. So, I ran exiftool on the file, and it showed file format error under the error section. 
4. Nothing much helping. So, I opened the .png file in the hexeditor HxD. 
5. First, I was able detect some error in the file signature hex values. So, on rectfying it, I tried opening the file, but I wasn't able to
```
file signature - 89 50 4E 47 0D 0A 1A 0A
```
5. Then, I further analyzed the hex values of the corruped .png file and this is what I was able to find.
- The problem was with IHDR, IDAT and IEND, being initialised as ihdr, idat, iend in the current file. 
6. So, I converted the lower case initialisation to uppercase, by replacing the required hex values at the required position.
7. The corresponding hex changes :
```
49 48 44 52 - IHDR
49 44 41 54	- IDAT
49 45 4E 44 - IEND
```
8. On saving these changes and on opening the file again, the flag was visible in the file.
<img width="1000" height="1000" alt="nutrela (1)" src="https://github.com/user-attachments/assets/b883954e-c287-4c92-a0bc-b18dc2efcdbf" />


## Flag:
```
nite{n0w_y0u_kn0w_ab0ut_PNG_chunk5}
```

## Concepts Learnt:
- I was able to learn about the concepts of File Signature, Chunk Structure and the Critical chunks. 
- Critical chunks :
a) IHDR (Image Header): Stores the image dimensions, bit depth, and color type.
b) IDAT (Image Data): Contains the compressed pixel data (using Zlib).
c) IEND (Image End): Marks the end of the file.

## Resources:
- I used the HxD software to correct the hex values of the png file

***
# 3. RAR the Abyss
> DESCRIPTION: 
Two philosophers peer into the networked abyss and swap a secret. Use the secret to decrypt the Abyss’ RAwR and pull your flag from the void.

## Solution:
1. In the challenge, it is mentioned that a secret is passed, in the description. 
2. A .pcap file is given, which I was able to open in WireShark. 
3. So, on opening it, by reconstructing the TCP streams (the chat traffic) in the file, I was able to see a conversation between "Camus" and "Nietzsche". 
<img width="1907" height="977" alt="3 1" src="https://github.com/user-attachments/assets/c3e7fbae-9ceb-4429-b2d5-78b12631109d" />

4. On following the TCP stream and on changing the streams, I was able to view these messages on 3 consecutive streams : 
```
Camus: whats the password ?
Nietzsche: b3y0ndG00dand3vil
Camus: thanks
```
5. After some trial and error on trying to find what is next step is, I noticed the hint in the challenge's title, which is, "RAR" the abyss.pcap. 
6. So, I converted the .pcap file to .rar file, using WinRAR and opened it using the password that we got earlier.
7. On doing so, a flag.txt file was inside it, which had the flag in text.

## Flag:
```
nite{thus_sp0k3_th3_n3tw0rk_f0r3ns1cs_4n4lyst}
```

## Concepts Learnt:
- With this challenge, I was able to learn about some of the concepts of Network Forensics (PCAP Analysis) and File Carving.
- TCP Stream Reconstruction - This is the process of reassembling all packets from a single network connection into a complete, readable conversation, allowing you to see transferred data like chat, passwords, or files.
- Use Wireshark's "Follow TCP Stream" feature to instantly view the full data exchange between two endpoints, which is crucial for finding embedded secrets.

## Resources:
- Wireshark software - I used this to extract the password from the tcp stream in the given .pcap file.

***

# 4. NineTails
>DESCRIPTION
Description:

Looks like I got a little too clever and hid the flag as a password in Firefox, tucked away like one of NineTails’ many tails. Recover the "logins" and the "key4" and let it guide you to the flag.

Hint:
I named my Ninetails "j4gjesg4", quite a peculiar name isn't it?

## Solution:
1. In the challenge, we are given .rar file, which I downloaded it and extracted a .ad1 file from it.
2. To open the .ad1 file, I downloaded a FTK Imager - Exterro FTK Imager. 
3. I opened the .ad1 file in the FTK imager as an evidence item and as an image file.
4. On getting access into the .ad1 file, I was able to see multiple folders in it.
5. Referring the description of the challenge, "hid the flag as a password in Firefox", "Recover the "logins" and the "key4" and "I named my Ninetails "j4gjesg4"".
6. Following the 1st lead, I started searching for the Firefox folder in the folders given and found it. After that, I spotted the "j4gjesg4" tag-folder on one of the folder names in profile section 
7. Now referring the 2nd lead, - "Recover the "logins" and the "key4" - which are key4.db and logins.json files in the "j4gjesg4.default-release" folder.
8. On extracting them, now we needed to open them to get to the flag.
9. For those purposes, we use a python tool - firepwd, I cloned the git which has this tool.
10. Then I used the firepwd tool, to extract the info from the files, by opening it in a virtual environment, installing pycryptodome inside venv and running firepwd using this environment, by passing the command (as shown below) and it gave the output as below:
```bash
(venv) rozakk@DESKTOP-JPQGP4K:~/firepwd$ python firepwd.py -d ~/custom
globalSalt: b'b5dbfec66b891e193f516eccaf39209a93634332'
 SEQUENCE {
   SEQUENCE {
     OBJECTIDENTIFIER 1.2.840.113549.1.5.13 pkcs5 pbes2
     SEQUENCE {
       SEQUENCE {
         OBJECTIDENTIFIER 1.2.840.113549.1.5.12 pkcs5 PBKDF2
         SEQUENCE {
           OCTETSTRING b'eaa234484d176f2f091e1a9b2162b550a9874bf9ced92daa19c43e058b1328cf'
           INTEGER b'01'
           INTEGER b'20'
           SEQUENCE {
             OBJECTIDENTIFIER 1.2.840.113549.2.9 hmacWithSHA256
           }
         }
       }
       SEQUENCE {
         OBJECTIDENTIFIER 2.16.840.1.101.3.4.1.42 aes256-CBC
         OCTETSTRING b'c361eb13322fd6004ad463de0ef2'
       }
     }
   }
   OCTETSTRING b'c2cb6eb12879f4c649458e59d634f355'
 }
clearText b'70617373776f72642d636865636b0202'
password check? True
 SEQUENCE {
   SEQUENCE {
     OBJECTIDENTIFIER 1.2.840.113549.1.5.13 pkcs5 pbes2
     SEQUENCE {
       SEQUENCE {
         OBJECTIDENTIFIER 1.2.840.113549.1.5.12 pkcs5 PBKDF2
         SEQUENCE {
           OCTETSTRING b'ae2720e0964ce4beabedba40345712df4234f9ac9cd86e53a08982b65abbbd9c'
           INTEGER b'01'
           INTEGER b'20'
           SEQUENCE {
             OBJECTIDENTIFIER 1.2.840.113549.2.9 hmacWithSHA256
           }
         }
       }
       SEQUENCE {
         OBJECTIDENTIFIER 2.16.840.1.101.3.4.1.42 aes256-CBC
         OCTETSTRING b'e3f13fa968393e84117de43bee0e'
       }
     }
   }
   OCTETSTRING b'7810f052c04cd95b738f9df37a27fe297c3cc97d3a04ca7a71b89ba111f1dbbf'
 }
clearText b'ab1ccd54c13b1573648a347a6e4c73dcc27af8cb8ad3c74c0808080808080808'
decrypting login/password pairs
https://www.rehack.xyz:b'warlocksmurf',b'GCTF{m0zarella'
 https://ctftime.org:b'ilovecheese',b'CHEEEEEEEEEEEEEEEEEEEEEEEEEESE'
https://www.reddit.com:b'bluelobster',b'_f1ref0x_'
https://www.facebook.com:b'flag',b'SIKE'
https://warlocksmurf.github.io:b'Man I Love Forensics',b'p4ssw0rd}'
```
11. On combining the strings of text given at the end of the list of text, as shown above, the flag was recovered successfully.

## Flag:
```
GCTF{m0zarella_f1ref0x_p4ssw0rd}
```
## Concepts Learnt:
- firepwd tool - Firepwd is used because Firefox stores saved passwords in an encrypted format spread across multiple files (key4.db, logins.json, etc).
- You cannot directly open these files and read the passwords — they are cryptographically protected. Firepwd automates the entire decryption process.

## Resources:
- Exterro FTK Imager software - I used it to extract key4.db and logins.json files from .ad1 file
- https://github.com/lclevy/firepwd - Github repo which I used to clone firepwd tool.

***

# 5. Re:Draw
> DESCRIPTION
description:

Her screen went black and a strange command window flickered to life, lines of text flashed across before everything went silent. Moments later, the system crashed. By sheer luck, we recovered a memory dump. 

Note: There are three stages to this challenge and you will find three flags.

What we know: just before the crash, a black command window flickered across the screen, something in its output might still be visible if you dig through memory. She was drawing when it happened, and remnants of a painting program linger, which could reveal more if inspected in the right way. Finally, a mysterious archive hides deeper in memory, likely holding the last piece of her work.

Hint:
Learn up on volatility 2 and its various plugins and what they are used for.

## Solution:
1. On reading the description of the challenge, "There are three stages to this challenge and you will find three flags."
2. From the description, I needed to use volatility 2 inorder to solve the challenge
3. Then, I started to breakdown the story in the description and connect it with the matching commands.
4. I downloaded the file given in the challenge and opened it in bash
5. First, I identified the operating system profile of the memory dump, using the volatility command, bcz we need this profile for all subsequent commands' extensions.
```
rozakk@DESKTOP-JPQGP4K:~/picoctf$ volatility -f MemoryDump_Lab1.raw imageinfo
Volatility Foundation Volatility Framework 2.6.1
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : Win7SP1x64, Win7SP0x64, Win2008R2SP0x64, Win2008R2SP1x64_24000, Win2008R2SP1x64_23418, Win2008R2SP1x64, Win7SP1x64_24000, Win7SP1x64_23418
                     AS Layer1 : WindowsAMD64PagedMemory (Kernel AS)
                     AS Layer2 : FileAddressSpace (/home/rozakk/picoctf/MemoryDump_Lab1.raw)
                      PAE type : No PAE
                           DTB : 0x187000L
                          KDBG : 0xf800028100a0L
          Number of Processors : 1
     Image Type (Service Pack) : 1
                KPCR for CPU 0 : 0xfffff80002811d00L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2019-12-11 14:38:00 UTC+0000
     Image local date and time : 2019-12-11 20:08:00 +0530
```
6. Now, I started breaking down the description: 
7. From the lines - Her screen went black and a strange command window flickered to life, lines of text flashed across before everything went silent., I understood that we needed to look for command-line history. Fir that, I execute the command : `volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 consoles`
```
rozakk@DESKTOP-JPQGP4K:~/picoctf$ volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 consoles
Volatility Foundation Volatility Framework 2.6.1
**************************************************
ConsoleProcess: conhost.exe Pid: 2692
Console: 0xff756200 CommandHistorySize: 50
HistoryBufferCount: 1 HistoryBufferMax: 4
OriginalTitle: %SystemRoot%\system32\cmd.exe
Title: C:\Windows\system32\cmd.exe - St4G3$1
AttachedProcess: cmd.exe Pid: 1984 Handle: 0x60
----
CommandHistory: 0x1fe9c0 Application: cmd.exe Flags: Allocated, Reset
CommandCount: 1 LastAdded: 0 LastDisplayed: 0
FirstCommand: 0 CommandCountMax: 50
ProcessHandle: 0x60
Cmd #0 at 0x1de3c0: St4G3$1
----
Screen 0x1e0f70 X:80 Y:300
Dump:
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Users\SmartNet>St4G3$1
ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0=
Press any key to continue . . .
**************************************************
ConsoleProcess: conhost.exe Pid: 2260
Console: 0xff756200 CommandHistorySize: 50
HistoryBufferCount: 1 HistoryBufferMax: 4
OriginalTitle: C:\Users\SmartNet\Downloads\DumpIt\DumpIt.exe
Title: C:\Users\SmartNet\Downloads\DumpIt\DumpIt.exe
AttachedProcess: DumpIt.exe Pid: 796 Handle: 0x60
----
CommandHistory: 0x38ea90 Application: DumpIt.exe Flags: Allocated
CommandCount: 0 LastAdded: -1 LastDisplayed: -1
FirstCommand: 0 CommandCountMax: 50
ProcessHandle: 0x60
----
Screen 0x371050 X:80 Y:300
Dump:
  DumpIt - v1.3.2.20110401 - One click memory memory dumper
  Copyright (c) 2007 - 2011, Matthieu Suiche <http://www.msuiche.net>
  Copyright (c) 2010 - 2011, MoonSols <http://www.moonsols.com>


    Address space size:        1073676288 bytes (   1023 Mb)
    Free space size:          24185389056 bytes (  23064 Mb)

    * Destination = \??\C:\Users\SmartNet\Downloads\DumpIt\SMARTNET-PC-20191211-
143755.raw

    --> Are you sure you want to continue? [y/n] y
    + Processing...
```
8. Among the output given in the terminal, I was able to find a unusual large string, which was actually Base64 and on decoding, I got the first flag
<img width="1087" height="605" alt="5 1" src="https://github.com/user-attachments/assets/3f581502-e215-4bf4-b5a1-ad3171b49963" />

<img width="1537" height="877" alt="5 2" src="https://github.com/user-attachments/assets/4cfeda58-48e8-4268-bb19-8da3be21f541" />


9. In the description: She was drawing when it happened, and remnants of a painting program linger, which could reveal more if inspected in the right way., this is given next. From this, I understood that we needed to dump the memory of the paint process and reconstruct the image to see what was written on it. Inorder to recover the image, we need to get the PID of mspaint. For that, I run:
```
rozakk@DESKTOP-JPQGP4K:~/picoctf$ volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 pslist
Volatility Foundation Volatility Framework 2.6.1
Offset(V)          Name                    PID   PPID   Thds     Hnds   Sess  Wow64 Start                          Exit
------------------ -------------------- ------ ------ ------ -------- ------ ------ ------------------------------ ------------------------------
0xfffffa8000ca0040 System                    4      0     80      570 ------      0 2019-12-11 13:41:25 UTC+0000
0xfffffa800148f040 smss.exe                248      4      3       37 ------      0 2019-12-11 13:41:25 UTC+0000
0xfffffa800154f740 csrss.exe               320    312      9      457      0      0 2019-12-11 13:41:32 UTC+0000
0xfffffa8000ca81e0 csrss.exe               368    360      7      199      1      0 2019-12-11 13:41:33 UTC+0000
0xfffffa8001c45060 psxss.exe               376    248     18      786      0      0 2019-12-11 13:41:33 UTC+0000
0xfffffa8001c5f060 winlogon.exe            416    360      4      118      1      0 2019-12-11 13:41:34 UTC+0000
0xfffffa8001c5f630 wininit.exe             424    312      3       75      0      0 2019-12-11 13:41:34 UTC+0000
0xfffffa8001c98530 services.exe            484    424     13      219      0      0 2019-12-11 13:41:35 UTC+0000
0xfffffa8001ca0580 lsass.exe               492    424      9      764      0      0 2019-12-11 13:41:35 UTC+0000
0xfffffa8001ca4b30 lsm.exe                 500    424     11      185      0      0 2019-12-11 13:41:35 UTC+0000
0xfffffa8001cf4b30 svchost.exe             588    484     11      358      0      0 2019-12-11 13:41:39 UTC+0000
0xfffffa8001d327c0 VBoxService.ex          652    484     13      137      0      0 2019-12-11 13:41:40 UTC+0000
0xfffffa8001d49b30 svchost.exe             720    484      8      279      0      0 2019-12-11 13:41:41 UTC+0000
0xfffffa8001d8c420 svchost.exe             816    484     23      569      0      0 2019-12-11 13:41:42 UTC+0000
0xfffffa8001da5b30 svchost.exe             852    484     28      542      0      0 2019-12-11 13:41:43 UTC+0000
0xfffffa8001da96c0 svchost.exe             876    484     32      941      0      0 2019-12-11 13:41:43 UTC+0000
0xfffffa8001e1bb30 svchost.exe             472    484     19      476      0      0 2019-12-11 13:41:47 UTC+0000
0xfffffa8001e50b30 svchost.exe            1044    484     14      366      0      0 2019-12-11 13:41:48 UTC+0000
0xfffffa8001eba230 spoolsv.exe            1208    484     13      282      0      0 2019-12-11 13:41:51 UTC+0000
0xfffffa8001eda060 svchost.exe            1248    484     19      313      0      0 2019-12-11 13:41:52 UTC+0000
0xfffffa8001f58890 svchost.exe            1372    484     22      295      0      0 2019-12-11 13:41:54 UTC+0000
0xfffffa8001f91b30 TCPSVCS.EXE            1416    484      4       97      0      0 2019-12-11 13:41:55 UTC+0000
0xfffffa8000d3c400 sppsvc.exe             1508    484      4      141      0      0 2019-12-11 14:16:06 UTC+0000
0xfffffa8001c38580 svchost.exe             948    484     13      322      0      0 2019-12-11 14:16:07 UTC+0000
0xfffffa8002170630 wmpnetwk.exe           1856    484     16      451      0      0 2019-12-11 14:16:08 UTC+0000
0xfffffa8001d376f0 SearchIndexer.          480    484     14      701      0      0 2019-12-11 14:16:09 UTC+0000
0xfffffa8001eb47f0 taskhost.exe            296    484      8      151      1      0 2019-12-11 14:32:24 UTC+0000
0xfffffa8001dfa910 dwm.exe                1988    852      5       72      1      0 2019-12-11 14:32:25 UTC+0000
0xfffffa8002046960 explorer.exe            604   2016     33      927      1      0 2019-12-11 14:32:25 UTC+0000
0xfffffa80021c75d0 VBoxTray.exe           1844    604     11      140      1      0 2019-12-11 14:32:35 UTC+0000
0xfffffa80021da060 audiodg.exe            2064    816      6      131      0      0 2019-12-11 14:32:37 UTC+0000
0xfffffa80022199e0 svchost.exe            2368    484      9      365      0      0 2019-12-11 14:32:51 UTC+0000
0xfffffa8002222780 cmd.exe                1984    604      1       21      1      0 2019-12-11 14:34:54 UTC+0000
0xfffffa8002227140 conhost.exe            2692    368      2       50      1      0 2019-12-11 14:34:54 UTC+0000
0xfffffa80022bab30 mspaint.exe            2424    604      6      128      1      0 2019-12-11 14:35:14 UTC+0000
0xfffffa8000eac770 svchost.exe            2660    484      6      100      0      0 2019-12-11 14:35:14 UTC+0000
0xfffffa8001e68060 csrss.exe              2760   2680      7      172      2      0 2019-12-11 14:37:05 UTC+0000
0xfffffa8000ecbb30 winlogon.exe           2808   2680      4      119      2      0 2019-12-11 14:37:05 UTC+0000
0xfffffa8000f3aab0 taskhost.exe           2908    484      9      158      2      0 2019-12-11 14:37:13 UTC+0000
0xfffffa8000f4db30 dwm.exe                3004    852      5       72      2      0 2019-12-11 14:37:14 UTC+0000
0xfffffa8000f4c670 explorer.exe           2504   3000     34      825      2      0 2019-12-11 14:37:14 UTC+0000
0xfffffa8000f9a4e0 VBoxTray.exe           2304   2504     14      144      2      0 2019-12-11 14:37:14 UTC+0000
0xfffffa8000fff630 SearchProtocol         2524    480      7      226      2      0 2019-12-11 14:37:21 UTC+0000
0xfffffa8000ecea60 SearchFilterHo         1720    480      5       90      0      0 2019-12-11 14:37:21 UTC+0000
0xfffffa8001010b30 WinRAR.exe             1512   2504      6      207      2      0 2019-12-11 14:37:23 UTC+0000
0xfffffa8001020b30 SearchProtocol         2868    480      8      279      0      0 2019-12-11 14:37:23 UTC+0000
0xfffffa8001048060 DumpIt.exe              796    604      2       45      1      1 2019-12-11 14:37:54 UTC+0000
0xfffffa800104a780 conhost.exe            2260    368      2       50      1      0 2019-12-11 14:37:54 UTC+0000
```
10. The PID is 2424. Now I run the command : `volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 memdump -p 2424 -D .`, to create a dmp file. 
```
rozakk@DESKTOP-JPQGP4K:~/picoctf$ volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 memdump -p 2424 -D .
Volatility Foundation Volatility Framework 2.6.1
************************************************************************
Writing mspaint.exe [  2424] to 2424.dmp
```
11. Then I changed the .dmp file to .data, and opened it in GIMP.
12. On adjusting the width and height of image, I was able to retrieve the flag from the image. The flag : `flag{G00d_Boy_good_girL}`
13. 'Finally, a mysterious archive hides deeper in memory, likely holding the last piece of her work.' - these lines we get the hint, ie mysterious archieve. For that we need to find a .rar file in memory and open it. For that I run the command : `volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 filescan | grep .rar`
14. I found \Device\HarddiskVolume2\Users\Alissa Simpson\Documents\Important.rar at multiple offsets.
15. Inorder to dump the RAR file, I run this command using the offset 0x000000003fa3ebc0, command : ` volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 dumpfiles -Q 0x000000003fa3ebc0 -D .`
16. On doing so, I rename it as a .rar file.
17. This .rar file has the flag, but inorder to open it, we need a password. FOr that, I run the hashdump command. 
```
rozakk@DESKTOP-JPQGP4K:~/picoctf$ volatility -f MemoryDump_Lab1.raw --profile=Win7SP1x64 hashdump
Volatility Foundation Volatility Framework 2.6.1
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SmartNet:1001:aad3b435b51404eeaad3b435b51404ee:4943abb39473a6f32c11301f4987e7e0:::
HomeGroupUser$:1002:aad3b435b51404eeaad3b435b51404ee:f0fc3d257814e08fea06e63c5762ebd5:::
Alissa Simpson:1003:aad3b435b51404eeaad3b435b51404ee:f4ff64c8baac57d22f22edc681055ba6:::
```
18. When we look into Alissa Simpson's documents folder, we can identify a NTML string `f4ff64c8baac57d22f22edc681055ba6` as the last text string.
19. On unrar-ing the NTML string, I got the 3rd flag.

## Flags:
```
1. flag{th1s_1s_th3_1st_st4g3!!}
2. flag{G00d_Boy_good_girL}
3. flag{w3ll_3rd_stage_was_easy}

```

## Concepts Learnt:
1. I was able to learn the concept that RAM contains a complete snapshot of the system state, including things that never touched the hard drive.
2. I was able to learn some new concepts in Volatility Framework & Profiles, User Activity Reconstruction, etc

## Resources:
- https://github.com/volatilityfoundation/volatility - Referred this github repo, to find some of the commands used in this challenge.

***






