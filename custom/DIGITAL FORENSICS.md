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






