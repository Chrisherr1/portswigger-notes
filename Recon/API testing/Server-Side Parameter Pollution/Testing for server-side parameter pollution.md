# Testing for server-side parameter pollution in the query string

To test for server-side parameter pollution in the query string, place query syntax characters like `#`, `&`, and `=` in your input and see how the application responds.

### Quick reference

| Character | Encoded | What it does |
| --- | --- | --- |
| `#` | `%23` | Truncates (cuts off) everything after your input |
| `&` | `%26` | Adds a new parameter |
| `=` | `%3D` | Sets a parameter's value |
| `/` | `%2f` | Adds path segments; `../` walks up the path (REST paths) |
| `"` | `%22`, or `\"` inside a JSON body | Breaks out of a JSON string so you can add new keys (structured data) |

> Always URL-encode these in your input. The front end decodes them once and pastes them raw into the internal request, where the internal API reads them as real syntax.

### Example scenario

A web app lets you search for other users using their username.

Your browser does:

```http
GET /userSearch?name=peter&back=/home
```

The server queries an internal API with the following request:

```http
GET /users/search?name=peter&publicProfile=true
```

---

## Truncating query strings

> **Truncate** means cut off the end. Here it means chopping off the rest of the server-side request so the internal API never sees the parameters the front end added after your input (like `&publicProfile=true`).

You can use a URL-encoded `#` character to attempt to truncate the server-side request.

To help you interpret the response, you could also add a string after the `#` character.

```http
GET /userSearch?name=peter%23foo&back=/home
```

This results in the following server-side request to the internal API:

```http
GET /users/search?name=peter#foo&publicProfile=true
```

> **Note:** It's essential that you URL-encode the `#` character. Otherwise, the front-end application will interpret it as a fragment identifier and it won't be passed to the internal API.

### Reviewing the response

Review the response for clues about whether the query has been truncated.

For example:

- If the response returns the user `peter`, the server-side query may have been truncated.
- If an `Invalid name` error message is returned, the application may have treated `foo` as part of the username.
  - This suggests that the server-side request may not have been truncated.

If you're able to truncate the server-side request, this removes the requirement for the `publicProfile` field to be set to `true`.

You may be able to exploit this to return non-public user profiles.

---

## Injecting invalid parameters

You can use a URL-encoded `&` character to attempt to add a second parameter to the server-side request.

For example: `&` = `%26`

```http
GET /userSearch?name=peter%26foo=xyz&back=/home
```

This results in the following server-side request to the internal API:

```http
GET /users/search?name=peter&foo=xyz&publicProfile=true
```

You'll have to review the response for clues about how the additional parameter is parsed.

For example, if the response is unchanged, this may indicate that the parameter was successfully injected but ignored by the application.

To build up a more complete picture, you'll need to test further.

---

## Injecting valid parameters

If you're able to modify the query string, you can then attempt to add a second valid parameter to the server-side request.

For example, if you've identified the `email` parameter, you could add it to the query string as:

```http
GET /userSearch?name=peter%26email=foo&back=/home
```

This results in the following server-side request to the internal API:

```http
GET /users/search?name=peter&email=foo&publicProfile=true
```

From here, you want to review the response for clues about how the additional parameter is parsed.

> **See also:** For information on how to identify parameters that you can inject into the query string, see the *Finding hidden parameters* section ([Mass assignment Vulns.md](../API%20Recon/Mass%20assignment%20Vulns.md)).

---

## Overriding existing parameters

To confirm whether the application is vulnerable to server-side parameter pollution, you could try to override the original parameter.

Do this by injecting a second parameter with the same name:

```http
GET /userSearch?name=peter%26name=carlos&back=/home
```

This results in the following server-side request to the internal API:

```http
GET /users/search?name=peter&name=carlos&publicProfile=true
```

The internal API interprets two `name` parameters.

The impact of this depends on how the application processes the second parameter. This varies across different web technologies:

| Technology | Behavior | Result |
| --- | --- | --- |
| **PHP** | Parses the last parameter only | A user search for `carlos` |
| **ASP.NET** | Combines both parameters | A user search for `peter,carlos`, which might result in an `Invalid username` error message |
| **Node.js / Express** | Parses the first parameter only | A user search for `peter`, giving an unchanged result |

If you're able to override the original parameter, you may be able to conduct an exploit.

For example, you could change the name to `name=administrator`, and this may allow you to log in as the admin.

---

# Testing for server-side parameter pollution in REST paths

A RESTful API may place parameter names and values in the URL path, rather than the query string.

```
/api/users/123
```

- `/api` is the root API endpoint.
- `/users` represents a resource, in this case users.
- `/123` represents a parameter, here an identifier for a specific user.

### Example scenario

Consider an application that enables you to edit user profiles based on their username:

```http
GET /edit_profile.php?name=peter
```

This results in the following server-side request:

```http
GET /api/private/users/peter
```

An attacker may be able to manipulate server-side URL path parameters to exploit the API.

### Testing

To test for this vulnerability, add path traversal sequences to modify parameters and observe how the application responds.

For example, you could submit a URL-encoded `peter/../admin` as the value of the `name` parameter:

```http
GET /edit_profile.php?name=peter%2f..%2fadmin
```

This may result in the following server-side request:

```http
GET /api/private/users/peter/../admin
```

If the server-side client or back-end API normalizes this path, it may resolve to:

```
/api/private/users/admin
```

---

# Testing for server-side parameter pollution in structured data formats

An attacker may be able to manipulate parameters to exploit vulnerabilities in the server's processing of other structured data formats, such as JSON or XML. To test for this, inject unexpected structured data into user inputs and see how the server responds.

### Example scenario: form input

Consider an application that enables users to edit their profile, then applies their changes with a request to a server-side API. When you edit your name, your browser makes the following request:

```http
POST /myaccount

name=peter
```

This results in the following server-side request:

```http
PATCH /users/7312/update

{"name":"peter"}
```

You can attempt to add the `access_level` parameter to the request as follows:

```http
POST /myaccount

name=peter","access_level":"administrator
```

If the user input is added to the server-side JSON data without adequate validation or sanitization, this results in the following server-side request:

```http
PATCH /users/7312/update

{"name":"peter","access_level":"administrator"}
```

This may result in the user `peter` being given administrator access.

### Example scenario: JSON input

Consider a similar example, but where the client-side user input is in JSON data. When you edit your name, your browser makes the following request:

```http
POST /myaccount

{"name": "peter"}
```

This results in the following server-side request:

```http
PATCH /users/7312/update

{"name":"peter"}
```

You can attempt to add the `access_level` parameter to the request as follows:

```http
POST /myaccount

{"name": "peter\",\"access_level\":\"administrator"}
```

If the user input is decoded, then added to the server-side JSON data without adequate encoding, this results in the following server-side request:

```http
PATCH /users/7312/update

{"name":"peter","access_level":"administrator"}
```

Again, this may result in the user `peter` being given administrator access.

### Injection in responses

Structured format injection can also occur in responses. For example, this can occur if user input is stored securely in a database, then embedded into a JSON response from a back-end API without adequate encoding. You can usually detect and exploit structured format injection in responses in the same way you can in requests.

> **Note:** The examples above are in JSON, but server-side parameter pollution can occur in any structured data format. For an example in XML, see the *XInclude attacks* section in the XML external entity (XXE) injection topic.

---

# Testing with automated tools

Burp includes automated tools that can help you detect server-side parameter pollution vulnerabilities.

- **Burp Scanner** automatically detects suspicious input transformations when performing an audit. These occur when an application receives user input, transforms it in some way, then performs further processing on the result. This behavior doesn't necessarily constitute a vulnerability, so you'll need to do further testing using the manual techniques outlined above. For more information, see the *Suspicious input transformation* issue definition.
- **Backslash Powered Scanner** BApp can also identify server-side injection vulnerabilities. The scanner classifies inputs as *boring*, *interesting*, or *vulnerable*. You'll need to investigate interesting inputs using the manual techniques outlined above. For more information, see the *Backslash Powered Scanning: hunting unknown vulnerability classes* whitepaper.
