# HARDWARE

# 1. IQ TEST
> DESCRIPTION
let your input x = 30478191278.
wrap your answer with nite{ } for the flag.
As an example, entering x = 34359738368 gives (y0, ..., y11), so the flag would be nite{010000000011}.

## Solution:
1. So, we are provided with an Input x = 30478191278
2. From this I understood, we need to convert the above X value to binary no. . And this X value transforms into a 36 bit binary number.
3. So, x = 30478191278 = 011100011000101001000100101010101110
4. Also, The circuit drawing is somewhat repetitive for 6 blocks (36 inputs to 12 outputs). It appears each 6‐input block yields 2 outputs (36/6 = 6 blocks to 12 outputs). The pattern is consistent in the diagram.
5. On solving it manually, we get  y = 100010011000 . 
![1 1](https://github.com/user-attachments/assets/026436ce-3c1f-4d3a-a4b8-7a9fb07199b6)

6. Therefore, the flag is : nite{100010011000}.

## Flag:
```
nite{100010011000}
```

## Resources:
- NIL 

***

# 2. I like Logic
> Description
i like logic and i like files, apparently, they have something in common, what should my next step be.

## Solution:
1. First, I was given a .sal file was given in the question. 
2. When I googled on how to open it, I found that you can do it using a saleae logic analyzer. This was the interface 
<img width="1920" height="1035" alt="2 1" src="https://github.com/user-attachments/assets/1520694a-535e-4a09-a69c-9cbaf7fd476b" />

3. In the channel 3, it showed it had some data in it, so I used the analyzer to generate the ascii value of the data, the Async Serial in the following settings:
   
![2 2](https://github.com/user-attachments/assets/d6936bf3-0f63-4475-8f45-272de28d82d7)

4. On executing it, I got this as the output.
   
![2 3](https://github.com/user-attachments/assets/937ebe88-2661-44ee-abf1-b2eafa2429f2)

![2 4](https://github.com/user-attachments/assets/f8062686-a692-4d0e-a31e-6bb692912016)

![2 5](https://github.com/user-attachments/assets/abca0414-46f1-4d92-9481-51d993a52371)


5. In midst of these texts, I found the flag : `FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}`

## Flag:
```
FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}
```
## Concept Learnt:
- I learnt how to use hardware analysis tools like Saleae Logic 2 to interpret and extract data from logic analyzer files.

## Notes:
- I overcomplicated the challenge a little bit, by using a lot of other options in the software and lost a bit of time. But circled back what was supposed to be done. 

## Resources:
- https://www.educative.io/answers/what-is-bit-plane-slicing - To refer the concepts used in the challenge.
- Saleae Logic Software

***

# 3. Bare Metal Alchemist
> Description:
my friend recommended me this anime but i think i've heard a wrong name.

## Solution:
1. First, I got firmware.elf file from the challenge. I was unsure what it meant.
2. So, I ran `file firmware.elf`, this was given as output:
<img width="1132" height="85" alt="3 1" src="https://github.com/user-attachments/assets/40cb049f-fdbf-47da-9402-ea94813e8d88" />

3. From this I understood it is an ELF32 binary for an Atmel AVR microcontroller.
4. So I somewhat understood that this was a firmware reverse-engineering challenge.
5. I started by listing the sections using: `avr-objdump -h firmware.elf` and found .bin, .data, .text, etc extensions.
6. So, I decided to dump the .bin section so we can see any embedded strings:
```
dd if=firmware.elf bs=1 skip=$((0x94)) count=$((0x2e4)) of=text_section.bin 2>/dev/null
hexdump -C text_section.bin | sed -n '1,40p'
```
which gave the output as : 
<img width="1121" height="841" alt="3 2" src="https://github.com/user-attachments/assets/537441de-abc4-4592-b547-91d97b80d406" />

7. After this, I wasted a lot of time coding a lot of non-related stuff and went in different directions.
8. Then I constructed a python code, inorder to :
a) The script opens text_section.bin and reads 200 bytes starting at offset 0x68.
b) It removes any trailing zeros (stops at the first 0x00).
c) If the result decodes to text containing { and }, it prints that string and stops. 
d) If nothing matches, it prints "No flag found with this model."
```
rozakk@DESKTOP-JPQGP4K:~/picoctf$ python3 - <<'PY'
> import sys
> try:
>     text=open('text_section.bin','rb').read()
> except FileNotFoundError:
>     sys.exit("text_section.bin not found. Create it with dd from firmware.elf first.")
> data=text[0x68:0x68+200]
> if 0 in data: data=data[:data.index(0)]
> for K in range(256):
>     key=(K*2)&0xFF
>     out=bytes(((b^0xA5)^key) for b in data)
>     try:
>         s=out.decode()
>     except:
>         continue
>     if '{' in s and '}' in s:
>         print(s)
>         sys.exit(0)
> print("No flag found with this model.")
> PY
```
9. On executing this, I got the FLAG as the output!!!
<img width="912" height="502" alt="3 3" src="https://github.com/user-attachments/assets/fc3dd695-d9ed-41e3-8e2c-58d5e2ebd2d7" />


## Flag:
```
TFCCTF{Th1s_1s_som3_s1mpl3_4rdu1no_f1rmw4re}
```

## Concept Learnt:
- I was able to learn a little bit about of Data Encoding/Decoding.
- I was able to learn some of the concepts of Reverse Engineering Microcontrollers, like Understanding Arduino memory layout and analyzing .hex or .bin dumps.

## Resources:
- https://github.com/NationalSecurityAgency/ghidra - glanced through this while solving this.

***







