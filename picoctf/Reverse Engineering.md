# REVERSE ENGINEERING 

# 1. GDB Baby step 1
> Description 
Can you figure out what is in the eax register at the end of the main function? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base.

## Solution:
1. From the title and hint of the question, I understood that we need to implement `gdb` in solving this question.
2. First, I open the downloaded file in the wsl terminal, inorder to run gdb on it. 
3. On running the gdb on `debugger0_a`, I run `info functions` inorder to see the functions in the file, especially for main, as mentioned in the challenge.
```
(gdb) info functions
All defined functions:

Non-debugging symbols:
0x0000000000001000  _init
0x0000000000001030  __cxa_finalize@plt
0x0000000000001040  _start
0x0000000000001070  deregister_tm_clones
0x00000000000010a0  register_tm_clones
0x00000000000010e0  __do_global_dtors_aux
0x0000000000001120  frame_dummy
0x0000000000001129  main
0x0000000000001140  __libc_csu_init
0x00000000000011b0  __libc_csu_fini
0x00000000000011b8  _fini
```
4. Now I disassemble the main function, using `disas main` and then look for the content in the eax register. 
```
(gdb) disas main
Dump of assembler code for function main:
   0x0000000000001129 <+0>:     endbr64
   0x000000000000112d <+4>:     push   %rbp
   0x000000000000112e <+5>:     mov    %rsp,%rbp
   0x0000000000001131 <+8>:     mov    %edi,-0x4(%rbp)
   0x0000000000001134 <+11>:    mov    %rsi,-0x10(%rbp)
   0x0000000000001138 <+15>:    mov    $0x86342,%eax   ----> AS REQ in question
   0x000000000000113d <+20>:    pop    %rbp
   0x000000000000113e <+21>:    ret
End of assembler dump.
```
5. In order to print the hex form of the value in decimal form, we can use gdb to do the conversion. So, I pass the command : `p/d 0x86342`, which outputs the required decimal value as shown below. And the flag is `picoCTF{549698} `
```
(gdb) p/d 0x86342
$1 = 549698
```

## Flag:
```
picoCTF{549698}
```
## Concepts Learnt:
1. Converting between number bases: hexadecimal (0x…) to decimal, because the flag expects a decimal value of the register content, using gdb and commands in the terminal.
2. In this challenge, used debugger to to inspect register values at the end of main function. So, to an extend, I was able to learn the workflow of disassembly + register inspection.

## Notes:
- I actually misread the question. The challenge expects decimal, but the code shows hex. So, I wasted some time going in different tangents from there.

## References:
- https://www.geeksforgeeks.org/c/gdb-step-by-step-introduction/ - Used this as a reference to learn about gdb and the commands associated with it.

***
# 2. ARMssembly 1
> DESCRIPTION
For what argument does this program print `win` with variables 58, 2 and 3? File: chall_1.S Flag format: picoCTF{XXXXXXXX} -> (hex, lowercase, no 0x, and 32 bits. ex. 5614267 would be picoCTF{0055aabb})

## Solution:
1. In this challenge,  we are given an arm assembly file. So, I start analyzing the file and the functions in it.
2. I came across the concept of : stacks(str), loads(ldr), left bit shift and store(lsl), etc
3. On further analyzing, I got to the point where I saw this :
```
.LC0:
	.string	"You win!"
	.align	3
.LC1:
	.string	"You Lose :("
	.text
	.align	2
	.global	main
	.type	main, %function
```
So, To win, I need to activate .LC0 . 
4. For that, We want the w0 variable to be 0. If it's not, we branch to .L4 which branches to .L1, and we don't want the result to be .L1 but .L0 . 
```
main:
	stp	x29, x30, [sp, -48]!
	add	x29, sp, 0
	str	w0, [x29, 28]
	str	x1, [x29, 16]
	ldr	x0, [x29, 16]
	add	x0, x0, 8
	ldr	x0, [x0]
	bl	atoi
	str	w0, [x29, 44]
	ldr	w0, [x29, 44]
	bl	func
	cmp	w0, 0 <------- WHAT'S REQUIRED!!!
	bne	.L4
	adrp	x0, .LC0
	add	x0, x0, :lo12:.LC0
	bl	puts
	b	.L6
```
5. Now, we know that w0 should be 0. But how? On tracking back and analyzing the stacks, I found, w0 should be 77, bcz w1 is 90 and w1 and w0 's subtraction result is stored in w0. So, to make the result of w0 = 0, the user input should be 90.
```
sub w0, w1, w0
```
6. As per question, the flag should be hex, lowercase, no 0x, and 32 bits version of the input. The hex value of 77 in 32 bit form is `0x0000004D`. 
7. Therefore, the flag is : `picoCTF{0000004d}`

## Flag:
```
picoCTF{0000004d}
```

## Concepts Learnt:
- I was able to learn the concepts of Stack & registers in ARM assembly.
- In ARM assembly, registers are small, fast-access storage locations (like w0, w1, sp) used to hold data and intermediate values, while the stack is a region of memory accessed via the stack pointer register (sp, or r13/R13) that stores local variables, return addresses and saved registers when functions are called.

## References:
- https://azeria-labs.com/writing-arm-assembly-part-1/ - To learn the concepts of ARM assembly.

***


# 3. Vault door-3
> This vault uses for-loops and byte arrays. The source code for this vault is here.

## Solution:
1. First, I opened the java code in VS Code, which they gave in the challenge and started analysing the code.
2. The code takes our input in the form of a flag as shown 
```
String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
```

And checks if the input is same as what's required accordingly, by calling the function checkPassword(input).
```
if (vaultDoor.checkPassword(input)) {
	    System.out.println("Access granted.");
	} else {
	    System.out.println("Access denied!");
        }
    }
```
3. In the checkPassword function, I came across some hints 
a) The length of the string should be 32
```
 if (password.length() != 32) {
            return false;
```
b) If it passes that condition, the code runs the password through a set of code such that it gets rearranged according to whats required.
```
for (i=0; i<8; i++) {
            buffer[i] = password.charAt(i);
        }
        for (; i<16; i++) {
            buffer[i] = password.charAt(23-i);
        }
        for (; i<32; i+=2) {
            buffer[i] = password.charAt(46-i);
        }
        for (i=31; i>=17; i-=2) {
            buffer[i] = password.charAt(i);
        }
```
c) Finally, the code 'unlocks the vault', if it matches with the condition as shown : AND that input that we gave is the FLAG!!!
```
return s.equals("jU5t_a_sna_3lpm18g947_u_4_m9r54f");
```
4. Now, I run the code, with the same string given in the code ( as shown above) : picoCTF{jU5t_a_sna_3lpm18g947_u_4_m9r54f} . But it was wrong.
<img width="615" height="157" alt="3 1" src="https://github.com/user-attachments/assets/580ca59c-5c77-47c6-998b-53b482dec47b" />


5. After some more trial and error, I thought, why not print the rearranged format of the flag, which we need to input along with the denying message. So I added a `System.out.println(s);` along with the code. So, on execution, I got the required input 
```
Enter vault password: picoCTF{jU5t_a_sna_3lpm18g947_u_4_m9r54f}
jU5t_a_s1mpl3_an4gr4m_4_u_79958f ----> REQUIRED FORMAT OF CONTENT IN THE FLAG!!!
Access denied!
```
6. On giving the input as picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_79958f}, the access was granted. Thus, this input is the required flag.
```
Enter vault password: picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_79958f}
jU5t_a_sna_3lpm18g947_u_4_m9r54f -----> This is the final form of the string that is bring compared.
Access granted.
```

## Flag:
```
picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_79958f}
```

## Concepts Learnt:
1. This challenge took me through the concept used in reverse engineering, that is to restructure/add your inputs into the code, so that you can understand what data is being passed on. Here, I was able to get the right input bcz I gave the output as the input and the required input was outputted with my improvisation in the code, which solved the challenge.

## Notes:
- I tried to give the output string, which was given in the code as the input and multiple trial and error versions of that string. This was not actaully what was required in solving the challenge.

## Resources:
- NIL

***


