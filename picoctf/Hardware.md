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
![alt text](../Cryptonite-Taskphase/TaskPhase-2/Hardware/1.1.jpg)
6. Therefore, the flag is : nite{100010011000}.

## Flag:
```
nite{100010011000}
```

## Resources:
- NIL 

***


