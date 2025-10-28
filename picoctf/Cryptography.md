# CRYPTOGRAPHY

# 2. Custom Encryption 
> DESCRIPTION
Can you get sense of this code file and write the function that will decode the given encrypted file content.
Find the encrypted file here flag_info and code file might be good to analyze and get the flag.

> HINT:
- Understanding encryption algorithm to come up with decryption algorithm.

## Solution:
1. In the challenge, we are given a encrypted file and python code. 
2. Let's breakdown the encryption :
a) generator establishes a shared numeric key via modular exponentiation.
```
def generator(g, x, p):  
	return pow(g, x) % p  
```
b) dynamic_xor_encrypt takes your plaintext, reverses it, and XORs it with a repeating text_key.
```
def dynamic_xor_encrypt(plaintext, text_key):  
	cipher_text = ""  
	key_length = len(text_key)  
	for i, char in enumerate(plaintext[::-1]):  
		key_char = text_key[i % key_length]  
		encrypted_char = chr(ord(char) ^ ord(key_char))  
		cipher_text += encrypted_char  
	return cipher_text  
```
c) encrypt takes the result of the XOR step, converts each character to ASCII, and multiplies by (shared_key * 311) to produce a list of large numbers.
```
def encrypt(plaintext, key):
	cipher = []  
	for char in plaintext:  
		cipher.append(((ord(char) * key*311)))  
	return cipher 
```
3. Going through the python code, I was able to understand that the encryption takes the plaintext, reverses it, XORs each character with a repeating key string (trudeau), then converts the result to numbers by multiplying each character’s ASCII code by a shared key (derived by modular exponentiation) × 311.

4. Now, we know that how to encrypt it, we just need to undo it to decrypt it.
5. To create a decryption function, I make the following changes :
a) First I derive the shared key using leak_shared_key.
```
def leak_shared_key(a, b):
    p = 97
    g = 31
    if not is_prime(p) and not is_prime(g):
        print("Enter prime numbers")
        return
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)
    shared_key = None
    if key == b_key:
        shared_key = key
    else:
        print("Invalid key")
        return
 
    return shared_key
```
b) Next you undo the multiplication step via decrypt.
```
def decrypt(ciphertext, key):
    semi_ciphertext = []
    for num in ciphertext:
        semi_ciphertext.append(chr(round(num / (key * 311))))
    return "".join(semi_ciphertext)
```
c) Then you undo the XOR + reversal step via dynamic_xor_decrypt.
```
def dynamic_xor_decrypt(semi_ciphertext, text_key):
    plaintext = ""
    key_length = len(text_key)
    for i, char in enumerate(semi_ciphertext):
        key_char = text_key[i % key_length]
        decrypted_char = chr(ord(char) ^ ord(key_char))
        plaintext += decrypted_char
    return plaintext[::-1]
```
6. After some errors and its corrections in the decryption code, on running the right , as required, I got the flag.
<img width="516" height="172" alt="2 1" src="https://github.com/user-attachments/assets/dd3c8151-0600-43f9-b2b1-e03e99a31a80" />


## Flag:
```
picoCTF{custom_d2cr0pt6d_751a22dc}
```

## Concept Learnt:
- Understanding how a shared secret key can be derived using modular exponentiation (a Diffie-Hellman-style calculation).
- Diffie–Hellman key exchange is a method by which two parties agree on a shared secret key over a public channel without ever sending that key itself, so even if someone is listening they can’t figure it out.

## Resources:
- [text](../../../Python_codes/decryption.py) - The python code for decryption.
- https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange - To research on Diffie–Hellman key exchange

***

# 3. miniRSA
>Description
Let's decrypt this: ciphertext? Something seems a bit small.

## Solution:
1. In the challenge, we are given, 
a) An RSA modulus N (bcz its a large integer)
b) A public exponent e = 3.
c) A ciphertext c (another large integer) that is the encryption of the plaintext message m under the public key (N, e).
2. A hint: the exponent is small; something “seems a bit small”.
3. On placing these respective values on a RSA decoder, I was able to decode the flag
<img width="1458" height="783" alt="3 2" src="https://github.com/user-attachments/assets/38bcb43b-9305-4a45-8039-91456413ecff" />

## Flag:
```
picoCTF{n33d_a_lArg3r_e_d0cd6eae}
```

## Concept Learnt:
- Normally, RSA encryption is performed using a large exponent and modulus to ensure security. However, with a small exponent and a plaintext that is small enough, the encryption becomes insecure because the ciphertext is simply the cube of the plaintext. This allows for a straightforward decryption by taking the cube root of the ciphertext.

- To solve this challenge, one could compute the cube root of the ciphertext to recover the plaintext message. If the plaintext is slightly larger than the cube root of the ciphertext, one might need to adjust the ciphertext by adding multiples of the modulus (N) and then taking the cube root to find the correct plaintext. This approach exploits the weakness introduced by the small exponent and lack of proper padding in the RSA encryption.

## Notes: An Alternate Method
1. In the challenge, we are given, 
a) An RSA modulus N (bcz its a large integer)
b) A public exponent e = 3.
c) A ciphertext c (another large integer) that is the encryption of the plaintext message m under the public key (N, e).
2. A hint: the exponent is small; something “seems a bit small”.
3. Equation : c=m^e(modN)
4. ( Refer the Concept learnt portion), Inspect the value of e. You see e = 3. That’s unusually small. That triggers a red‐flag: maybe a low‐exponent attack is possible.
5. Look at the size of N and c and estimate: if m is reasonably small (e.g., ASCII flag message) then 𝑚^3 might be less than N.
6. Therefore, Given the small exponent e=3 and the nature of the encryption, the ciphertext c is close to the cube of the plaintext message m. By calculating the cube root of c, we can retrieve m, then convert it to a readable format to obtain the flag.
7. The code I wrote for solving the above question:
![alt text](../Cryptonite-Taskphase/TaskPhase-2/Cryptography/3.1.png)

## Resources:
- https://en.wikipedia.org/wiki/RSA_cryptosystem - To look into the concept of RSA.
- https://github.com/RsaCtfTool/RsaCtfTool - To refer some tools that can be used in solving RSA challenges
- https://www.dcode.fr/rsa-cipher - RSA decoder


***
