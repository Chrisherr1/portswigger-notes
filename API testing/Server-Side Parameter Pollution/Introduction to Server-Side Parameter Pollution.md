# Server-side parameter pollution

Some systems contain internal APIs that aren't directly accessible from the internet.

Server-side parameter pollution occurs when a website embeds user input in a server-side request to an internal API without adequate encoding.

---

## How it actually works

There are two separate programs:

1. **The front end** is the public website you talk to.
2. **The internal API** is hidden. Only the front end can reach it.

When you send input, the front end pastes it into its own request to the internal API, then adds its own parameters after yours.

Your request:

```http
GET /userSearch?name=peter&back=/home
```

The hidden request the front end builds:

```http
GET /users/search?name=peter&publicProfile=true
```

### Why injecting a parameter does anything

- Parameters don't run code on their own. They're just variables.
- The internal API's handler runs on every request and already reads certain parameters, like `name`, `field`, or `publicProfile`.
- The front end normally fills those in for you, for example by always sending `field=email`.
- Injecting `&field=reset_token` doesn't add new logic. The same handler runs, just with a value the developers never expected an outsider to control.
- If you inject a parameter the internal API doesn't know, it usually just gets ignored.

> Think of a vending machine with a locked panel. The front end only ever presses button A for you. You're not rewiring the machine, you're finding a way to press button B.

### Why `&` and `#` matter

Because the front end adds its parameters after your input:

- `&` (`%26`) lets you add your own parameter.
- `#` (`%23`) cuts off everything after your input, so the front end's parameters get dropped.

You URL-encode them so the front end treats them as part of your value and passes them along. The internal API then reads them as real URL syntax.

### What kind of attack is this?

It's an injection attack, the same family as SQLi and XSS. The only difference is the language you're injecting into.

| Attack | What you break out of |
| --- | --- |
| SQL injection | SQL syntax |
| XSS | HTML / JavaScript |
| Server-side parameter pollution | URL syntax (query string or path), or JSON / XML |

The common thread: untrusted input gets dropped into a structured format without encoding, so your characters get read as syntax instead of data.

---

This means that an attacker may be able to manipulate or inject parameters, which may enable them to:

1. Override existing parameters.
2. Modify the application behavior.
3. Access unauthorized data.

You can test any user input for any kind of parameter pollution.

> Query parameters, form fields, headers, and URL path parameters may all be vulnerable.

---

> **Note:** This vulnerability is sometimes called HTTP parameter pollution. However, this term is also used to refer to a web application firewall (WAF) bypass technique. To avoid confusion, in this topic we'll only refer to server-side parameter pollution.

> **Note:** Despite the similar name, this vulnerability class has very little in common with server-side prototype pollution.
