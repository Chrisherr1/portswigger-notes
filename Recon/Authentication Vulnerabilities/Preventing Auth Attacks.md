# Preventing attacks on your own authentication mechanisms

1. Always use HTTPS and redirect HTTP requests to use HTTPS.

2. Make sure No username/emails are shown publicly on profiles or in HTTP responses.

3. make sure users are using a strong password, use a password checker (zxcvbn javascript library)

4. Never reveal a username exists, always use identical,generic error message, and make sure they really are identical.

5. Always return the same HTTP status code with each login request and make response times are indistinguishable as possible.

6. Implement IP-based user rate limiting. It should prevent attackers from manipulating their apparent IP address, force them to complete a CAPTCHA with every login attempt, after a certain time.
    - not full proof, but makes it annoying for the attacker.

7. TRIPLE CHECK your logic, a check that can by bypassed is, not much better than no check at all.

8. Remember that a password reset or change is just as valid an attack surface as the main login mechanism and, consequently, must be equally as robust.

9. Sending verification codes via email is essentially just a more long-winded form of single-factor authentication.

10. SMS-based 2FA is technically verifying two factors (something you know and something you have). However, the potential for abuse through SIM swapping, for example, means that this system can be unreliable.

11. Ideally, 2FA should be implemented using a dedicated device or app that generates the verification code directly.

12. Finally,make sure your 2FA checks is sound so it cant be bypassed.