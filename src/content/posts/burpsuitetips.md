---
title: Burpsuite tips 1#
published: 2025-12-16
description: 'this is a small tip for burpsuite user'
image: 'guide/bst1_background.jpg'
tags: [HTB,learning,request method,HTTP]
category: 'learning'
draft: false 
lang: 'en'
---

Hi recently i have been learning about **HTTP Verb Tampering (on HTB)** which is you changing the request method to another one and realized that… well take a look at the example below

Here i have a lab and the question is :

> To get the flag, try to bypass the command injection filter through HTTP Verb Tampering, while using the following filename: file; cp /flag.txt ./
> 

![image.png](guide/bst1_1.png)

### In the first situation

When you are turning intercept on, and you want to change request method… you should not do like this  :

![burpsuitetips.gif](guide/bst1_2.gif)

![image.png](guide/bst1_3.png)

Why nothing appear ??? This is because the `POST` method has the different way to add the parameters when compared with `GET`, and moreover, it will not automatically add `content-type` so you have to do this manually.

Instead you can do like this : 

![burpsuitetips2.gif](guide/bst1_4.gif)

Boomm!! chall solved !

![image.png](guide/bst1_5.png)

Flag: HTB{b3_v3rb_c0n51573n7}

😽  tks for reading this one. Hope it will be helpful to you !!!!! seee ya later