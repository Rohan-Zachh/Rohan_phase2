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



