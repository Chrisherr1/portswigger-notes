# Mass assignment vulnerabilities

Mass assignment (AKA auto-binding) can inadvertently create hidden parameters.

It occurs when software frameworks automatically bind request parameters to fields on an internal object.

Mass assignment may therefore result in the application supporting parameters that were never intended to be processed by the developer.

---

## Finding hidden parameters

Since mass assignment creates parameters from object fields, you can often identify these hidden parameters by manually examining objects returned by the API.

### Example

`PATCH /api/users/` is used to update a username and email:

```json
{
    "username": "wiener",
    "email": "wiener@example.com",
}
```

When it gets sent, a `GET /api/users/123` request gets sent with it.

The response comes back with:

```json
{
    "id": 123,
    "name": "John Doe",
    "email": "john@example.com",
    "isAdmin": "false"
}
```

This may indicate that the hidden `id` and `isAdmin` parameters are bound to the internal user object, alongside the updated username and email parameters.

---

## Testing the example for the vulnerability

To test whether you can modify the enumerated `isAdmin` parameter value, add it to the `PATCH` request:

```json
{
    "username": "wiener",
    "email": "wiener@example.com",
    "isAdmin": false,
}
```

And send an invalid version too:

```json
{
    "username": "wiener",
    "email": "wiener@example.com",
    "isAdmin": "foo",
}
```

> If the application behaves differently, this may suggest that the invalid value impacts the query logic, but the valid value doesn't. This may indicate that the parameter can be successfully updated by the user.

You can then send a `PATCH` request with the `isAdmin` parameter value set to `true` to try to exploit the vulnerability:

```json
{
    "username": "wiener",
    "email": "wiener@example.com",
    "isAdmin": true,
}
```

If the `isAdmin` value in the request is bound to the user object without adequate **validation and sanitization**, the user `wiener` may be incorrectly granted admin privileges.

To determine whether this is the case, browse the application as `wiener` to see whether you can access admin functionality.
