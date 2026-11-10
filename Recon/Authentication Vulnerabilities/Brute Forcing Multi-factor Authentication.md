# Vulnerabilities in multi-factor authentication
- If you send the code via email, and the login had the email, it's like you're verifying the same information twice.(it doesnt work)
- SMS (If you can get someone's sim you can get their SMS verification codes.)
- Biometrics are often not used, but effective.

# Bypassing Two factor authentication

Sometimes it's implemented in a way where you can just bypass it entirely.

If the user s first prompted to enter a password and then prompted to enter a verification code on a separate page the user is effectively in a 'logged in' state before they have entered in the verification code.

In this case its worth seeing if you can skip to the logged-in pages after completing the first authentication step.

> Sometimes you will find that one website that doesn't check if you've completed the second step before loading a page lol.


## Try to find a webpage that works for an authenticated user and going to that page when it asks for a Auth token.

eg: /my-account shows my account info once logged in, what if we tried to log in and instead of giving an auth token we go straight to /my-account

# Flawed two-factor verification logic

Sometimes an app won't properly verify the same user is completing the second step.

eg:

User logs in.
```
POST /login-steps/first HTTP/1.1
Host: vulnerable-website.com
...
username=carlos&password=qwerty
```

They are given a cookie that relates to their account WITHOUT doing 2FA yet..
```
HTTP/1.1 200 OK
Set-Cookie: account=carlos

GET /login-steps/second HTTP/1.1
Cookie: account=carlos
```
When submitting the verification code, the request uses this cookie to determine which account the user is trying to access.

```
POST /login-steps/second HTTP/1.1
Host: vulnerable-website.com
Cookie: account=carlos
...
verification-code=123456
```

> If this is the case, the attacker could log in using their own CREDS and just change the value of the cookie to another account when submitting the verification code.

This would make it possible to log in with your login and just change the cookie to be the account you want to go in to, and just do your normal 2FA, once you get past it you'll get in to the victim's account.

> This is extremely dangerous... If the attacker then brute force their own verification code, they could get into multiple victim accounts by just knowing their usernames. THEY WOULD NEVER NEED THE VICTIM'S PASSWORD...

## In regards to the 2FA broken logic Lab..

It had us basically recognize that if you changed the verify parameter within the login request you can change which user gets issued a auth token.

After which we can log into our account like normal but now when it asks for the auth token we send that request to intruder and change the verify again to our victim's name. This way you know an auth token was issued and now you're trying to brute force the code to get into their account.

> Need premium because brute forcing 10000 requests takes way too long...

---

# Brute-forcing 2FA verification codes

As with passwords, websites need to take steps to prevent brute-forcing of the 2FA verifcation code.

This is especially important becuase the code is often a simple 4 or 6 digit number...

**WITHOUT BRUTE FORCE PROTECTION,CRACKING SUCH A CODE IS TRIVIAL**

## Attempt Prevention : Logging a user out if they fail 2FA too many times.
Some website attempt to prevent this by automatically logging a user out if they enter a certain number of incorrect verification codes....

    - This is often ineffective in practice becuase the advanced attacker can even automate this multi-step process by creating macros for burp intruder...

    - The Turbo Intruder extension can also be used for this purpose.

## Precaution : Functionalities along side auth

Some website provide the functionality to allow users to manage their account. SO users can change their password or reset their password when they forget it.
    - This specific functionality can lead to a vulnerability.
    
Websites usually take care to avoid well-known vulnerabilities in their login pages.

BUT it is often very easy to overlook the fact that you need to take similar steps to ensure that the related functionality is equally robust.

> This is very important in cases where an attacker is able to create their own account and, consquently has easy access to study these additional pages.
---

# Vulnerabilities in keeping users logged in

A common feature is the option to stay logged in even after closing a browser session.

This is usually a simple checkbox labeled something like "Remember me" or "Keep me logged in".

### How does it work?

Often implemented by generating a "remmember me" token of some kind, which is then stored in a persistent cookie.(Stays on the browser regardless of browser closing)

If you have this cookie it effectively allows you to bypass the entire login process, it is best practice for this cookie to be impractical to guess.

HOWEVER, some websites generate this cookie based on a predictable concatenation of static values such as `username` and a timestamp.

Some even use the password as part of the cookie...

This approach is particularly dangerous if an attacker is able to create their own account because they can study their own cooke and potentially deduce how it is generated...

> Once they workout the formula, they can try to brute-force other users' cookies to gain access to their accounts.

## It is usually encrypted though..

Some website assume that if the cookie is encrypted it will not be possible to guess the values even if it uses static values..

Sometimes true if done correctly, by using a one way encoding.

But sometimes you find that the value that has been encoded using a 2 way encoding encryption like base64.

> In this case it offers no protection cuz anyone can decode it.

## Even one way encryption isn't completely bulletproof

If the attacker is able to easily identify the hashing algorithm, and no salt is used, they can potentially brute-force the cookie by simply hashing a wordlist they have and checking if they match.

This method can be used to bypass login attempt limits if a similar limit isn't applied to cookie guesses.

> This method can be used to bypass login attempt limits if a similar limit isn't applied to cookie guesses.

Even if the attacker is not able to create their own account, they may still be able to exploit this vulnerability.

Using the usual techniques,such as XSS, the attacker could steal another user's "remember me" cookie and deduce how the cookie is constructed from that....

If the website was built using an open-source framework, they key details of the cookie construction may even be publicly documented.


> You might want to use john the ripper to give the hash and let it try and decode the hash using a wordlist.

> For future me... No good GPU, or a weird file format to crack? John.
> Got a GPU and need to crack a lot, fast? hashcat


## The thing the lab wanted to show..

lets say they encrypt a cookie using base64.. If you noticed that you could decode it since its a one way.

In the example after decoding it you got wiener:hash, after you find out what type of hash it is, you can then compare that hash with a other password's hashes to see if it fits.

Lets say 

wiener:51dc30ddc473d43a6011e9ebba6ca770 = peter

then we would use john the ripper to give it a wordlist of passwords, and a file with the hash in it and t would check if any of the passwords compare to it.

> Note john the ripper might not be allowed on the exam so its smart to try and just check your password and see if its one for one the hash = password.

> In some cases hashes of well known password could just be a google search away, so if you search the hash you might be able to find the cleartext. (hence why salting is important).

# Vulnerabilities in resetting user passwords

Users will forget their passwords, so it is common to have a way for them to reset it.

But, this can be easily done wrong and must be implemeneted correctly.

## WRONG : Sending passwords by email

It should never be possible to send a user's current password if the password is handled securely on the website.

Generally, sending persistent passwords over insecure channels should always be avoided.
Otherwise, this approach is highly suseceptible to man-in-the-middle-attacks.

Email is not considered secure and not designed for sensitive information. Many users end up syncing their inbox between a bunch of devices across insecure channels.


## better : Resetting passwords using URL

A more robust method of resetting passwords is to send a unique URL to users that takes them to a password reset page.

Just don't make the URL parameter guessable.

```
http://vulnerable-website.com/reset-password?user=victim-user

```
This is super guessable, and you can see that someone could possibly change the user to
reset the password of a different user.

## BEST : Generating a High-entropy, hard to guess token && create the reset URL based on that

```
http://vulnerable-website.com/reset-password?token=a0ba0d1cb3b63d13822572fcff1a241895d893f659164d4cc550b421ebdd48a8

```
Best case scenario this shows no hints to which user's password is being reset.

Things that should happen.
    1. System should check whether this token exists in the back-end and,if so, which user's password it is suppose to reset.
        - The token should expire after a short period of time and be destroyed immediately after the password has been reset.

HOWEVER,
    Some website fail to validate the token again when the reset form is submitted.

    In this case, an attacker could simply visit the reset form from their own account,delete the token, and leverage this page to reset an arbitary user's password.


> If the URL in the reset email is generated dynamically, this may also be vulnerable to password reset poisoning. In this case, an attacker can potentially steal another user's token and use it to change their password.

## Vulnerabilities with users changing their passwords

Typically involves entering your current password and then new password twice.

These pages rely on the same process for checking that usernames and current passwords match as a normal login page does.

> This can be vulnerable to the same techniques.

Password change functionality can be particular dangerous if it allows an attacker to access it directly without being logged in as the victim user.

> For example, if the username is provided in a hidden field, an attacker might be able to edit this value in the request to target arbitrary users. This could be used to enumerate usernames and bruteforce passwords.


