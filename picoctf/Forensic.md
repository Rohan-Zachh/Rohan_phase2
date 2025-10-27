# FORENSIC

# 1. Trivial Flag Transfer Protocol
> DESCRIPTION:
Figure out how they moved the flag.
HINT: What are some other ways to hide data?

## Solution:
1. First, I open the tftp file in WIRESHARK. In there, I filter out the tftp data in the file and export it separately out.
2. About 7 files got exported as part of the above process. Mainly 2 txt files with some encryption on it.
3. Also, as .dev file got exported with steghide, which might have been used to hide the flag in the images given or something. ( initial assumption )
4. Bcz we have a encrypted message, I decided why not put it in the cipher identifier. 
5. On running it, I was able to identify it as ROT-13 encrytion 
![alt text](1.1-1.png)
6. The first part of the message: TFTP DOESNT ENCRYPT OUR TRAFFIC SO WE MUST DISGUISE OUR FLAG TRANSFER
7. The sencond part of the message : 
![alt text](1.2-1.png)
8. Then from the plan.txt file's message was also ROT-13 and it gave the key inorder to solve the challenge, ie KEY: DUEDILIGENCE - the password used for steghide.
9. Now, I ran steghide on all the 3 image files and the 3rd image, picture3.bmp had hidden the flag, which hid the data in it. On being invoked by steghide, it transferred the flag into flag.txt file. 
```
rozakk@DESKTOP-JPQGP4K:~$ steghide extract -sf picture3.bmp -p DUEDILIGENCE
wrote extracted data to "flag.txt".
```
10. On opening the opening flag.txt , the flag was revealed!!!
```
rozakk@DESKTOP-JPQGP4K:~$ cat flag.txt
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```

## Flag:
```
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```

## Concepts Learnt:
- Trivial File Transfer Protocol (TFTP) works: it uses UDP, no authentication/encryption, simple file-transfer protocol.
- Learned how to use a packet-capture tool (Wireshark) to export files transferred over TFTP.

## Resources:
- https://www.dcode.fr/cipher-identifier - used this to decode the message in the .txt file.

***

# 2. tunn3l v1s10n
> Description
We found this file. Recover the flag.
## Solution:
1. First, I download the file that's given in the challenge. But, when I tried to open it, I wasn't able to do so.
2. I also noticed the file downloaded had no extension. So, I ran exiftool on it.
![alt text](file:///d%3A/Cryptonite-Taskphase/TaskPhase-2/Forensic/2.1.png)
I found that the file in a bmp file. So, I added the bmp extension to it.
3. Now, I tried opening it. But on opening it, I wasn't able to see the content in it. So, its more than likely to be corrupted.
4. On doing some research, I understood that we can use a hex editor to examine what's corrupted in the file.
5. To uncorrupt the file, I compared it with a normal bmp file, on the hex editor.
6. On comparison, I found that the info header had characters like : BA D0, instead of numbers like a normal bmp file. So, I converted the characters to the nos given in the normal bmp file like : ` 36 00 00 00 28 00 `. 
7. On saving it, I got :
![alt text](file:///d%3A/Cryptonite-Taskphase/TaskPhase-2/Forensic/2.2.png)
which had a fake flag in it.
8. Here, I came to a hault. So, I focused on the basics, ie, to look into what the challenge name is hinting at? Tunnel vision means to narrow down your vision on something. What if the image we uncorrupted is a cropped version of an even bigger picture? How can we uncrop it? - By increasing the height and width of the image file content.
9. We can do this process in the hex-editor. So, referencing the website (that I mentioned in the resources), I did this.
10. The 2nd row is where the bytes of the height and the width are located, ie 4 byted for each height and width. 
11. I started with changing the height's value, bcz height's value was way less than width's value. I started with 1000's hex value, working my way down. Well, 1000 was wrong, so I couldn't see the content in the image file.
12. At a height of around 850, I was able to see the content in the file and the flag was revealed in the image as shown !!
![alt text](file://wsl.localhost/Ubuntu/home/rozakk/picoctf/tunn3l_v1s10n.bmp)


## Flag:
```
picoCTF{qu1t3_a_v13w_2020}
```

## Concepts Learnt:
- A hex editor shows the exact bytes inside the file, in hexadecimal format on the left, and ASCII characters on the right. Bcz in this challenge, you can’t see it using a normal image viewer, but you can see it by examining the raw bytes of the file.

## Resources:
-https://www.ece.ualberta.ca/~elliott/ee552/studentAppNotes/2003_w/misc/bmp_file_format/bmp_file_format.htm - For referring the concept of bitmap headers 

***

# 3. m00nwalk
> DESCRIPTION:
Decode this message from the moon.

HINTS:
1. How did pictures from the moon landing get sent back to Earth?
2. What is the CMU mascot?, that might help select a RX option. 

## Solution:
1. On opening the audio file given in the challenge, the audio file had some strange audio in it. 
2. Referring the hints and doing a bit research on it, I was able to understand that it was a SSTV signal. 
3. In order to convert the sstv signal to an image file, you can run it in a simple command as given in the resources
```
$ sstv -d (audio_file.wav) -o result.png
```
4. On installing sstv in my wsl, I ran the command ` sstv -d message.wav -o result.png `, which converted the sstv signal into an image file as result.png. On opening result.png, the flag was in it.
![alt text](result-1.png)
## Flag:
```
picoCTF{beep_boop_im_in_space}
```

## Concepts Learnt:
- This challenge highlights the importance of signal processing and data transmission techniques used in real-world applications. SSTV was used in early space missions to send images back to Earth.
-sstv stands for Slow Scan Television — a way to send images as audio signals over radio frequencies.

## Resources:
-https://github.com/colaclanth/sstv - To refer to the sstv command, to convert the audio file into an image file.

***