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
