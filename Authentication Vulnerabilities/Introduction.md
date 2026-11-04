# Authentication

Authentication is the process of verifying the identity of a user or client

3 Types of authentication.
    1. Something you know(password,security question)
    2. Something you have(physical objects, security token)
    3. Something you are(Biometrics, face scanner)

## Authentication and Authorization.

Authentication is checking that the user is who they claim to be.

Authorization is checking if the user is authorized to do an action.

    1. logging in(authentication)
    2. Once logged in we check if the user is able to post,ect.(authorization)

## How do authentication vulnerabilities arise?

Two ways:

1. The authentication mechanisms are weak because they fail to adequately protect against brute force attacks.
    - No Rate Limiting mechanism or Weak passwords

2. Logic flaws or poor coding in the implementation allow the authentication mechanisms to be bypassed entirely by an attacker.
    - AKA **Broken Authentication**

## what is the impact of a vulnerable authentication?

The impact of authentication vulnerabilties can be **SEVERE**.

1. Using a user's account
    If an attacker **bypasses authentication** or **brute-forces** their wya into another user's account they have access to all data and functionaility that the compromised account has.

2. Using admin account
    If an attacker bypasses authentication or brute-forces their way into a **HIGH PRIV** account like a system administrator,
    They could take **full control** over the **entire application** and potentially gain access to **internal infrastructure**.

3. Gain access to more attack surface

    If an attacker gain access to a low privilege account, it could help them access additional pages that may be only accessible via an internal page.

## Vulnerabilities in password-based logins

Basically if you can figure out the password of a user, you can user their account.

## Brute-forcing Logins

Brute forcing is when you try to fuzz the password in order to guess the user's valid login.

> You often want to try and make a wordlist that caters to that user, using what you know about them..

> If websites don't have any brute force protections, kinda cooked not gonna lie.

### Brute forcing Usernames

They can sometimes be obvious (admin or administrator).

During looking through the website try and see if usernames can be found anywhere on the site..

Sometimes the username shown on posts are the same for their logins.

> Good to check HTTP responses to see if any **Email Addresses** are disclosed. Sometimes you can find responses that contain email addresses from **HIGH PRIV users*! such as Admins or IT support.

## Brute-forcing passwords

Websites often have requirements for passwords(min characters,uppercase letter,least one special character) which is ment to make it harder to brute force a password..

> Sometimes though users will just use a "whatever" password to get past the requirements... if they are forced to change it often.
    (Mypassword1!,Mypassword2?,Mypassword2!)

This makes it easier to make a wordlist for the user  if we know what their previous password was since we know they are only changing maybe a character or two.

## What is Username Enumeration ?

Username enumeration is when an attacker is able to observe changes in the website's behavior in order to identify whether a given username is valid.

> This occurs either on a **Login Page** **Registration Page**.

> It happens when you get a valid username but incorrect password, or on registration forms when a username is taken.
    - This tells us that a username exists.

This reduces the time and effort required to brute force a login because we can generate a shortlist of valid usernames this way.

Things you should ay attention to:

1. Status codes.
    - The returned HTTP status code is likely to be the same for the vast majority of guesses because most of them will be wrong.

    - If the guess returns a different status code, this is a strong indication that the username was correct.

2. Error Messages.
    - Sometimes the returned error message is different depending on whether both the username AND password are incorrect or only the password was incorrect.

    -Usually its best to give generic messages in both cases but small typing errors sometimes creep in. Making it easy to see that it isnt giving the same error.

3. Response times.
    - If most requests were handled with similar response time, any that deviate from this suggest that something different was happening behind the scenes.

    This might indicate that the guessed username might be correct.

    eg: A website might only check if the password is correct if the username is valid.

    Making it smart to check if the username is even valid in the first place.

## Flawed brute-force protection

It is highly likely that a brute-force attack will involve many failed guesses before the attacker successfully compromises an account.

Logically brute-force protection revolves around trying to make it as tricky as possible to automate the process and slow down the rate at which an attacker can attempt logins.

The Two most common way to prevent brute-force attacks are:

    1.Locking the account that the remote user is trying to access if they failed too many login attempts

    2.Blocking the remote user's IP address if they make too many login attempts in quick succession

Both approaches offer varying degrees of protection, but neither is invulnerable, especially if implemented using flawed logic.

Eg: For example, you might sometimes find that your IP is blocked if you fail to log in too many times.

In some implementations, the counter for the number of failed attempts resets if the IP owner logs in successfully.

This means an attacker would simply have to log in to their own account every few attempts to prevent this limit from ever being reached.

> In this case, merely including your own login credentials at regular intervals throughout the wordlist is enough to render this defense virtually useless.

## Account locking

One way in which websites try to prevent brute-forcing is to lock the account if certain suspicious criteria are met, usually a set number of failed login attempts.

> Sometimes lock outs can tell us information about a specific account.

> Locking an account offers a little protection for an account being brute forced, but doesn't prevent an attacker that is just trying to gain access to any account they can.


Eg:

1. Establish a list of candidate usernames that are likely to be valid.

2. Decide on a very small shortlist of passwords that you think at least one user is likely to have.(Don't exceed # of attempts allowed, before timeout)

3. Using a tool such as Burp Intruder, try each of the selected passwords with each of the candidate usernames.
This way, you can attempt to brute-force every account without triggering the account lock.

4. You only need a single user to use one of the three passwords in order to compromise an account.


Account locking also fails to protect against credential stuffing attacks, which relies on using leaked username:passwords from other sites to test if they're the same for other sites.

Account locking does not protect against credential stuffing because each username is only being attempted once
