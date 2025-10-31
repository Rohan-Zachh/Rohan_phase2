# EXTRAS

# 1. So Meta
> DESCRIPTION 
Find the flag in this picture.
> HINTS
- What does meta mean in the context of files?
- Ever heard of metadata?

## Solution:
1. A picture is given in the challenge. 
2. From the hints, its given that we need to check the metadata of the file, to find the FLAG
3. On checking the metadata on a metadata viewer website, I was able to find the flag

## Flag:
```
picoCTF{s0_m3ta_eb36bf44}
```

## Concepts Learnt:
- Metadata of a file means the data that describes information about the file itself, such as its name, size, type, creation date, and permissions.

## Resources
- https://www.metadata2go.com/result#j=fd4af859-443c-47d6-a3c8-c692b56aad0b - Website to search the metadata of a file.

***

# 2. Extensions
> DESCRIPTION
This is a really weird text file TXT? Can you find the flag?

## Solution:
1. From the title itelf, I was able to understand we need to do something with the file's extension. 
2. On opening it, the file content was not somewhat is some form of code, with PNG at the first line of the file.
3. So, I changed the extension of the file to .png. Then, On opening it, I found the flag as the image inside the file.

## FLag:
```
picoCTF{now_you_know_about_extensions}
```

# 3. shark on wire 1
> DESCRIPTION

## Solution:
1. First, I opened the file that was given in the challenge, in wireshark, bcz the file had .pcap extension.
2. Then, I went to Statistics -> Protocol Hierarchy, where I was able to see the data stream listed with other contents in the file's stream.
3. Bcz data section is the usual space where these flags are usually hidden, I opened the data stream as filter.
4. Now, we follow the UDP stream from there on. In the UDP stream, there are multiple streams that one can look into.
5. I started going through all the streams that were available, and finally, I reached the stream - 6, where the flag was displayed.
![alt text](file:///d%3A/Cryptonite-Taskphase/TaskPhase-2/Xtras/3.1.png)

## Flag:
```
picoCTF{StaT31355_636f6e6e}
```

## Concepts Learnt:
1. UDP (User Datagram Protocol) - It groups packets with the same source and destination IPs and ports, showing how data travels in that specific communication session.

## Resources:
- Wireshark Platform - To extract the flag from the data stream.

***

# 4. Login
> DESCRIPTION
My dog-sitter's brother made this website but I can't get in; can you help?
login.mars.picoctf.net 

## Solution:
1. When I opened the link and went into the website, I was able to find a plain login page.
2. So, the most obvious first step we do in web exploitation is inspect the website.
3. On doing so, I found the website's js and css code.
4. So, I went through the js code, where I was able to find something like this :
```
 return "YWRtaW4" !== t.u ? alert("Incorrect Username") : "cGljb0NURns1M3J2M3JfNTNydjNyXzUzcnYzcl81M3J2M3JfNTNydjNyfQ" !== t.p ? alert("Incorrect Password") : void alert(`Correct Password! Your flag is ${atob(t.p)}.`)
```
5. Well, we can clearly spot 2 BAse 64 strings here. So, I ran the base 64 decoder on cyberchef, to decode these strings. 
6. On doing so, I found that :
```
cGljb0NURns1M3J2M3JfNTNydjNyXzUzcnYzcl81M3J2M3JfNTNydjNyfQ = picoCTF{53rv3r_53rv3r_53rv3r_53rv3r_53rv3r} --> FLAG
```

## Flag:
```
picoCTF{53rv3r_53rv3r_53rv3r_53rv3r_53rv3r}
```

## Concepts Learnt:
- Base 64 - A Base64 string is a way of encoding binary data (like images or files) into readable text using only letters, numbers, and a few symbols.

## Resources:
- https://gchq.github.io/CyberChef/ - Used cyberchef for decoding the base 64 strings.

***

# 5. Search Source
> DESCRIPTION:
The developer of this website mistakenly left an important artifact in the website source, can you find it?
> HINT:
- How could you mirror the website on your local machine so you could use more powerful tools for searching ?

## Solution:
1. Well, from the title of the challenge itself, we know that we need to search the source page of the website.
2. ON doing so, in the beginning of the source page itself, we have some css code links. 
3. Then I started trying trial and error efforts for finding the flag / anything related to it in these css codes.
4. And in "css/style.css", I found the flag, when I searched up picoCTF in the code's page.
![alt text](5.1-1.png)

## Flag:
```
picoCTF{1nsp3ti0n_0f_w3bpag3s_8de925a7}
```

## Resources:
- NIL

***





