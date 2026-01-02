---
title: HTB University CTF 2025
published: 2026-01-02
description: 'This is my blog'
image: 'guide/htbctf2025.jpg'
tags: [learning,CTF,HTB]
category: 'Competition'
draft: false 
lang: 'en'
---

Hi!! Well, this one is a CTF event that I participated in with my little bro at the same university. We all have to try hard for everything… but we lack knowledge about the forensic and blue team aspects, which contain all left flags. Feeling really sad about the fact that we are unable to solve all of them :((. We will perform better next year!!!

Damn, all I want is just to share this with you, which is really cool!!!!

![image.png](guide/cert/htbctf.png)

https://github.com/TZkYkunaga/UNIVERSITY-HTB-CTF-2025




Below are just some write-ups that I did. I have to move on to new things, so chillll

# SHL33T

`D4rkn1ght` solved this

```c
└─$ file shl33t 
shl33t: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=d6bd527d1e1ffe23cd8cde17a97cf771c50738e7, for GNU/Linux 3.2.0, not stripped

```

```c
└─$ checksec --file=shl33t --output=xml 
<?xml version="1.0" encoding="UTF-8"?>
<file relro="full" canary="yes" nx="yes" pie="yes" rpath="no" runpath="no" symbols="yes" fortify_source="no" fortified="0" fortify-able="3" filename='shl33t'/>

```

```c
b *0x555555555a9e
```

```c
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣀⣀⣠⣤⣤⣤⣤⣤⣤⣀⣀⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣠⣶⠟⠛⠋⠉⠉⠉⠉⠉⠉⠉⠉⠙⠛⠳⢶⣦⣄⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⣴⡿⠋⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠙⠻⣶⣄⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⢀⣾⠏⠀⠀⠀⠀⠀⢾⣦⣄⣀⡀⣀⣠⣤⡶⠟⠀⠀⠀⠀⠀⠀⠈⠻⣷⡄⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⣀⣀⣤⣤⣴⡿⠃⠀⣶⠶⠶⠶⠶⣶⣬⣉⠉⠉⠉⢉⣡⣴⡶⠞⠛⠛⠷⢶⡆⠀⠀⠈⢿⠶⠖⠚⠛⠷⠶⢶⣶⣤⣄⡀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⢀⣠⣴⡾⠛⠋⠉⡉⠀⠀⠀⠀⠀⢀⣠⣤⣤⣤⡀⠉⠙⠛⠛⠛⠛⠉⠀⠀⢀⣤⣭⣤⣀⠀⠀⠀⠀⠈⠀⠀⢀⣴⣶⣶⣦⣤⣌⡉⠛⢷⣦⣄⠀⠀⠀⠀⠀⠀
⠀⠀⢠⣶⠟⠋⣀⣤⣶⠾⠿⣶⡀⠀⠀⠀⣴⡟⢋⣿⣤⡉⠻⣦⠀⠀⠀⠀⠀⠀⢀⣾⠟⢩⣿⣉⠛⣷⣄⠀⠀⠀⠀⢰⡿⠑⠀⠀⠀⠈⠉⠛⠻⣦⣌⠙⢿⣦⠀⠀⠀⠀
⠀⣴⡟⠁⣰⡾⠛⠉⠀⠀⠀⢻⣇⡀⠀⢸⣿⠀⣿⠋⠉⣿⠀⢻⡆⠀⠀⠀⠀⠀⣾⡇⢰⡟⠉⢻⣧⠘⣿⠀⠀⠀⠀⣼⠇⠀⠀⠀⠀⠀⠀⠀⠀⠈⠻⡇⠀⠙⢷⣆⠀ 
⢰⡟⠀⢼⡏⠀⠀⠀⠀⠀⠀⠈⠛⠛⠀⠈⢿⣆⠙⠷⠾⠛⣠⣿⠁⠀⠀⠀⠀⠀⠹⣧⡈⠿⣶⠾⠋⣼⡟⠀⠀⠀⢀⣿⠀⠀⠀⠀⠀⠀⠀⠀⣠⣶⠶⠶⣶⣤⣌⡻⣧⡀
⢸⣧⣯⣬⣥⣄⣀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠛⠿⢶⡶⠾⠛⠁⠀⠀⠀⠀⠀⠀⠀⠙⠻⢶⣶⣶⠿⠋⠀⠀⠀⠰⣼⡏⠀⠀⠀⠀⠀⠀⢠⣾⠏⠀⠀⠀⠀⠈⠉⠛⠛⠃
⠀⠀⠀⠈⠉⠉⠉⠛⠿⣶⣄⠀⠀⠀⠀⠀⠀⠀⠀⣲⣖⣠⣶⣶⣶⠀⠀⠀⠀⣀⣤⣤⡂⡀⠀⠀⠀⠀⠀⠀⠀⢸⠟⠀⠀⠀⠀⠀⢀⣴⡟⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠻⣷⣄⠀⠀⠀⠀⢠⣾⠋⠁⢿⣇⠀⠀⠀⠀⠀⠀⢙⠉⣹⡇⠻⠷⣶⣤⡀⠀⠀⠀⠀⠀⠀⠀⠀⣠⣴⠟⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠻⣷⣤⣀⡀⠘⠟⠃⠀⠈⢙⣷⡄⠀⠀⠀⣠⣶⠿⠋⠁⠀⠀⠀⠙⣿⠀⠀⢠⣤⣤⣶⠶⠟⠋⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠙⣿⡄⠀⠀⠀⠀⠀⢸⣿⠀⠀⢰⡿⠁⠀⠀⠀⠀⠀⠀⣠⡿⠀⢠⡿⠃⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠘⣿⣄⠀⠀⠀⠀⠀⢻⣧⣠⡿⠁⠀⠀⠀⠀⠀⠀⠀⠉⠁⣴⡿⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⢿⣆⠀⢿⣦⡀⠀⠉⠉⠀⠀⠀⠀⠀⣀⣄⠀⠀⢠⣾⠏⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠻⣧⡀⠙⠻⢷⣦⣄⣀⣤⣤⣶⠾⠛⠁⢀⣴⠟⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠈⠻⣷⣄⡀⠀⠀⠀⠀⠀⠀⢀⣠⣾⠟⠁⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀
⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠉⠛⠿⠷⠶⠶⠾⠟⠛⠉⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀⠀

[Nibbletop] These elves are playing with me again, look at this mess: ebx = 0x00001337

[Nibbletop] It should be ebx = 0x13370000 instead!

[Nibbletop] Please fix it kind human! SHLeet the registers!

$ 
[Nibbletop] HOORAY! You saved Christmas again!! Here is your prize:
HTB{sh1ft_2_th3_l3ft_sh1ft_2_th3_r1ght_26134083d983ef02760344ff6ed39d9d}   
```

```c
echo -ne "\xc1\xe3\x10\xc3" | nc 154.57.164.64 32018
```

### 1. Allocating executable memory (`mmap`)

`pcVar3 = (code *)mmap((void *)0x0, 0x1000, 7, 0x22, -1, 0);`

This line is extremely important. The program creates a new memory region. The `7` parameter corresponds to permissions **RWX** (Read, Write, Execute). That means this region can be written to and also executed as code.

### 2. Receiving a "command" from you (`read`)

`sVar2 = read(0, pcVar3, 4);`

The program waits for you to provide exactly **4 bytes** of data and stores them directly into the memory region created above (`pcVar3`).

### 3. Directly executing your input

`uVar8 = (*pcVar3)();`

This is the crux. The program treats the 4 bytes you provided as a function and jumps to execute them.

- If you send the string "AAAA", it will attempt to execute the machine code for `0x41414141` (which will usually crash).
- To succeed, you must send actual machine instructions (assembly) that modify registers as Nibbletop expects.

### 4. Why was your earlier `echo` criticized?

When you ran `echo -ne "\x37\x13"`, you only sent meaningless bytes into the register (or memory). Nibbletop expects you to **"SHLeet the registers!"** — i.e., use the **SHL** (shift left) instruction.

The `ebx` register currently holds `0x1337`. To make it `0x13370000`, you need to shift it left by 16 bits.

- **Assembly instruction:** `shl ebx, 16`
- **Terminating instruction:** `ret` (to return control to main and let it print the success message).

### Summary: the bytes you need to send

You need to send the bytes that correspond to `shl ebx, 16` followed by `ret`.

| **Instruction** | **Hex Opcode** | **Explanation** |
| --- | --- | --- |
| `shl ebx, 16` | `C1 E3 10` | Shift EBX left by 16 bits (append four hex zero digits) |
| `ret` | `C3` | Return control to main so it prints success |

# SnowSignal - SleighComms

### Step 1: Analyze the sound sequence

In the source, the `Et` array defines the captured signal sequence:

```jsx
const Et=[
  {id:1, dingCount:1, ...}, // 1 ding
  {id:2, dingCount:2, ...}, // 2 dings
  {id:3, dingCount:1, ...}, // 1 ding
  {id:4, dingCount:3, ...}, // 3 dings
  {id:5, dingCount:2, ...}  // 2 dings
];
```

=> The numeric sequence is: **1 - 2 - 1 - 3 - 2**.

---

### Step 2: Lookup the Tone Decoder Manual

The `bf` array serves as the conversion table from ding counts to character pairs:

- **1 DING** = `ST`
- **2 DINGS** = `FR`
- **3 DINGS** = `EV`
- **4 DINGS** = `HO`
- **5 DINGS** = `PE`

Concatenate the code sequence **1 - 2 - 1 - 3 - 2** using this table:
`1(ST)` + `2(FR)` + `1(ST)` + `3(EV)` + `2(FR)` = **STFRSTEVFR**

---

### Step 3: Match against the Landmark Database

In the `ep` (Landmark Database) array we search for an entry whose `prefix` matches the string:

```jsx
{
  id: "stfrstev-waypoint",
  prefix: "STFRSTEVFR", // MATCH!
  name: "Starfrost Chapel Way",
  fullName: "Starfrost Chapel Waypoint",
  ...
}
```

---

Flag format is `HTB{LOCATION_PREFIX}`.
The matched location is **Starfrost Chapel Waypoint** with prefix **STFRSTEVFR**.

**Your flag is:** `HTB{STFRSTEVFR}`

# FrostFleet - RiverWatch

This challenge includes a map and docks labeled `D-001` through `D-004`.

### 1. Analyze the data from the code

- **AIS ping location (distressed vessel):** `42.3456°N, -71.0892°W`.
- **Captain's log:**
  - The captain reports being near a "historic dock" and a "chapel".
  - Water depth is only **2.1m**. The vessel ran aground due to shallow draft.
  - The area is currently marked **MAINTENANCE**.
- **Dock registry lookup:**
  - We examine dock **D-004 (Everlight Landing)**:
    - Coordinates: `42.3458, -71.0890` (only **~27 meters** from the AIS ping — within the <50m threshold).
    - Depth: **2m** (matches the reported shallow depth ~2.1m).
    - Features: "Historic site", "Near chapel".
    - Status: **MAINTENANCE**.

**Conclusion:** The correct dock is **Everlight Landing** with ID **D-004**.

---

### 2. Flag format

According to the code: `HTB{DOCK_NAME_DOCK_ID}`
Example given: `HTB{HARBOR_POINT_D042}` (note the example shows no hyphen in the ID portion).

Based on this, try these options (HTB flags are often sensitive to hyphens):

- **Option 1 (closest to registry):** `HTB{EVERLIGHT_LANDING_D-004}`
- **Option 2 (remove hyphen in ID as in example):** `HTB{EVERLIGHT_LANDING_D004}`

---

### Selected flag

`HTB{EVERLIGHT_LANDING_D004}`