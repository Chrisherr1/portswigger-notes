# What is path traversal?

Path traversal is also known as directory traversal.

These vulnerabilities enable an attacker to read arbitrary files on the server that is running an application, such as:

1. Application code and data.
2. Credentials for back-end systems.
3. Sensitive operating system files.

In some cases, an attacker might be able to write to arbitrary files on the server, allowing them to modify application data or behavior, and ultimately take full control of the server.

---

## Example scenario

A shopping application displays images of items for sale:

```html
<img src="/loadImage?filename=218.png">
```

The `/loadImage` URL takes a `filename` parameter and returns the contents of `218.png`.

The image files are stored on disk in the location:

```
/var/www/images/
```

To return an image, the application appends the requested filename to this base directory and uses a filesystem API to read the contents of the file.

In other words, the application reads:

```
/var/www/images/218.png
```

### The attack

If the application implements no defenses against path traversal attacks, an attacker could request the following URL:

```
https://insecure-website.com/loadImage?filename=../../../etc/passwd
```

This causes the application to read from the following file path:

```
/var/www/images/../../../etc/passwd
```

---

## How `../` works

`../` is valid within a file path. It allows us to move up one level in the directory structure.

They are stackable too:

```
../../../../
```

This would allow us to keep going up until we hit the root.

### Windows

On Windows, you can use both `../` and `..\`.

```
https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini
```

This would be an example of a Windows system traversal.

> **Tip:** Remember to enable images and CSS in the proxy history filter for files being retrieved, or you won't be able to see all your possibilities.
