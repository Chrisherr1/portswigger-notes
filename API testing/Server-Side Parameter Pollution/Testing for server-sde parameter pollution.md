# Testing for server-side parameter pollution in the query string

To test for server-side parameter pollution in the query string, place query syntax characters like `#,&,=` in your input and see how the application responds.
---

Consider this This...

A web app lets you search for other users using their username.

Your browser does:
```
GET /userSearch?name=peter&back=/home
```

The server queries an internal API with the following request.
```
GET /users/search?name=peter&publicProfile=true

```

## Truncating query strings

You can use URL-encoded `#` character to attempt to truncate the server-side request.

To help you interpret the response, you could also add a string after the `#` character.

```
GET /userSearch?name=peter%23foo&back=/home
```

This would make the frontend access this instead:
```
GET /users/search?name=peter#foo&publicProfile=true

```
> It's essential that you URL-encode the # character. Otherwise the front-end application will interpret it as a fragment identifier and it won't be passed to the internal API.

---

Review the response for clues whether the query has been truncated..

Eg:

    If the response returns the user peter, the server-side query may have been truncated.

    If an Invalid name error message is returned, the application may have treated `foo` as part of of the username.

        - This suggests that the server-side request may not have been truncated.

If you're able to truncate the server-side request, this removes the requirement for the `publicProfile` field to be set to `true`.

You may be able to exploit this to return non-public user profiles.


## Injecting Invalid Paramters 

You can use an URL-encoded `&` character to attempt to add a second parameter to the server side request.

eg:
`&` = `%26`

```
GET /userSearch?name=peter%26foo=xyz&back=/home

````
This results in the following server-side request to the internal API:
```text
GET /users/search?name=peter&foo=xyz&publicProfile=true
```
---

You'll have to review the response for clues about how the additional parametere is parsed.

For example, if the response is unchanged this may indicate that the parameter was successfully injected bu ignored by the application.

To build up a more complete picture you'll need to test further.


