# Testing for server-side parameter pollution in the query string

To test for server-side parameter pollution in the query string, place query syntax characters like `#`, `&`, and `=` in your input and see how the application responds.

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

You can use a URL-encoded `#` character to attempt to truncate the server-side request.

To help you interpret the response, you could also add a string after the `#` character.

```http
GET /userSearch?name=peter%23foo&back=/home
```

This would make the front-end access this instead:

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
