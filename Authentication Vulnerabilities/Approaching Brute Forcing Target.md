# Approaching a Brute Forcing Target 

You want to learn how the login talks, finding the one thing that leaks, get the username, clear the wall and go for the password.

# Step 1 : Baseline Request

    1.Send a for sure wrong input

    2.Send a right username and wrong password

You're trying to see what a completely wrong input looks like, so you can compare it with a normal wrong login.

Things to take note:

> The message text,HTTP status, the response length in bytes, and time it took to receive the response.

# Step 2 : Find your North star
    - work down the list, and take the one that works.

1. Message wording.
    - Check if you get a "Invalid username" or "Incorrect password", different errors means that it can verify that a username does/doesn't exist.

## Response errors look the same..

2. Response length
    - Check the length of the response, they might be subtly different.
    - eg: Incorrect password/username. | Incorrect password/username....
    - use the settings > Grep Extract > "Incorrect password/username."
        - This will let us see the error responses while fuzzing.

3. Status codes
    - See if status codes show up only for certain inputs.
    -(301) Redirects might mean that its accepting the input.

## Using Response messages to find usernames

4. Response timing
    - Send a long password(100+ characters).
    - Valid usernames run the password hash and come back slower..
    - Invalid usernames end up getting stopped immediately, so they often fail faster.

## Using Lockout messages to find usernames

5. Account Lockout message
    - This can itself work in your favor for finding usernames.
    - After too many incorrect login attempts for REAL accounts,it might try to lock you out.
    - This means that you can check if its real by just checking if the username locks out.
    (Think cluster bomb, You can try multiple usernames and see which gets a lock out, you'll need to check the lockout number by using a real username though at first maybe one you have)
    - Cluster bomb > username payload (simple list), password payload (null payload)(generate = # of tries per username).

> Timing and lock messages are lower on the list, and work as a fallback.

# Step 3: Enumerate the username.

Use Intruder to filter using whatever filter you found from step 2.

Out of this you should be able to find a single username/ or even more.


# Step 4: Clear the throttle before the password phase.

You need to figure out which defenses are in your way...

## If you get IP blocked...
    - You'll know you got IP blocked by... getting a cooldown message.

Fastest way to confirm on demand: drop into Repeater and send a request with your own known-good credentials. If that returns the block message instead of a normal success, your IP is currently blocked. If it logs you in, you’re clear.

> IP block after N failures: spoof X-Forwarded-For with a unique value per request, so every attempt looks like a new source (SAME AS SHOWN IN STEP 5).

> Now the trap, because this is the part that fails people on the exam. If you don’t notice the block, your whole brute force is poisoned. Every attempt after the threshold reports “failure” not because the password was wrong but because you were blocked from being checked at all. So the actual correct password, if Intruder tried it after the block kicked in, shows up as just another failure and you walk right past it. That’s why you detect the block first, then either bypass it with the X-Forwarded-For spoof or reset the counter by interleaving a valid login, and only then trust your results.

### If you notice that a successful login resets IP block..

You can interleave your own vlaid credential every few requests to zero the counter, the keep going.


### Possible Bypass: Request stuffing

    - If the endpoint allows it you can try to send multiple passwords in a single request, usually through an array in the JSON body.
    - GRAPHQL has a version of this PLEASE BE AWARE of how do it it since it's the same mechanism but different request.

# ACCOUNT LOCKOUT

> You will need to respect this throughout the brute force if it's present.

> Use it as a north star, like in step 2, and keep password guessing against that account under the lock threshold or try to find a reset(Sometimes a correct credential login resets it).

# Step 5 : Brute force the password

Now we FUZZ the password with the username fixed, and filter by success.

> We want to keep the XFF spoofing or interleave running the whole time.
(X-Forwarded-For) HTTP header, and spoof the IP 
```
X-Forwarded-For: 1.1.1.1      (request 1)
X-Forwarded-For: 1.1.1.2      (request 2)
X-Forwarded-For: 1.1.1.3      (request 3)
```
You can from here Make the IP a FUZZ parameter. and fuzz the password alongside changing the IP.

# At this point you should be able to login after finding the password.





