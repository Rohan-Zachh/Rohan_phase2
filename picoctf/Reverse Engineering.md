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

