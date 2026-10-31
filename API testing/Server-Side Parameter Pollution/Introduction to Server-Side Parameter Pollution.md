# Server-side parameter pollution

Some systems contain internal APIs that aren't directly accessible from the internet.

Server-side parameter pollution occurs when a website embeds user input in a server-side request to an internal API without adequate encoding..

---
This means that an attacker may be able to manipulate or inject paramaters,which may enable them to..

### 1. Override existing parameters
### 2. Modify the application behavior
### 3. Access unauthorized data

You can test any user input for any kinda of parameter pollution.

> query parameters, form fields, headers, and URL path parameters may all be vulnerable.

---

> This vulnerability is sometimes called HTTP parameter pollution. However, this term is also used to refer to a web application firewall (WAF) bypass technique. To avoid confusion, in this topic we'll only refer to server-side parameter pollution.

> In addition, despite the similar name, this vulnerability class has very little in common with server-side prototype pollution.