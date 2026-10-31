# Mass assignment vulnerabilities

Mass Assignment (AKA auto-binding) can inadvertently create hidden parameters.

It occurs when software frameworks automatically bind request parameters to fields on an internal object.

Mass assignment may therefore result in the application supporting parameters that were never indented to be processed by the developer.

---

## Finding hidden parameters.

Since mass assignment creates parameters from object fields you can often identify these hidden parameters by manually examining objects returned by the API.

Eg: PATCH /api/users/ (This is used to update a username and email)

```text
{
    "username": "wiener",
    "email": "wiener@example.com",
}
```
It gets sent and a GET /api/users/123 gets sent with it. 
The response comes back with..
```text
{
    "id": 123,
    "name": "John Doe",
    "email": "john@example.com",
    "isAdmin": "false"
}
```

This may indicate that the hidden `id` and `isAdmin` parameters are bound in the internal user object, alongside the updated username and email parameters.

## Testing the Example for the vulnerability

To test if you can modify the enumerated `isAdmin` parameter value add it to the aptch request:

```
{
    "username": "wiener",
    "email": "wiener@example.com",
    "isAdmin": false,
}

AND 
 a invalid version too..
{
    "username": "wiener",
    "email": "wiener@example.com",
    "isAdmin": "foo",
}
```
> If the application behaves differently, this may suggest that the invalid value impacts the query logic, but the valid value doesn't. This may indicate that the parameter can be successfully updated by the user.


You can then send a PATCH request with the isAdmin parameter value set to true, to try and exploit the vulnerability:

```
{
    "username": "wiener",
    "email": "wiener@example.com",
    "isAdmin": true,
}
```

If the `isAdmin` value in the request is bound to the user object without adequate `validation and sanitization`, the user wiener may be incorrectly granted admin privileges.

To determine whether this is the case, browse the application as wiener to see whether you can access admin functionality.

