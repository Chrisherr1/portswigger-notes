# What is Path Traversal ?

Path Traversal is just directory traversal.

These vulnerabilities enable an attacker to read arbitrary files on a server that is running on application.

    1. Application code and data.
    2. Credentials for back-end systems.
    3. Sensitive operating system files.

In some cases, an attacker might be able to write to arbitrary files on the server, allowing them to modify application data or behavior, and ultimately take full control of the server.

---
## Consider this...

A shopping application that displays images of items for sale.

```
<img src="/loadImage?filename=218.png">
```

the /loadImage URl takes `filename` parameter and returns the content 218.png.

The image files are stored on disk in the location...

/var/www/images/
    to return an image, the application appends the requested filename to this base directory and uses a file system API to read the conents of the file. 

    In other words, the application reads...

    /var/www/images/218.png

If the application implements no defenses against path traversal attacks,

The result is that an attacker could request the following URL..

https://insecure-website.com/loadImage?filename=../../../etc/passwd

This causes the application to read from the following file path:

/var/www/images/../../../etc/passwd

```
../
```
Is valid within the file path, it allows us to move up one level in the directory structure.

They are stackable too..

```
../../../../
```
This would allow us to continue going up until we hit the root.


In windows, you can use both ../ and ..\

```
https://insecure-website.com/loadImage?filename=..\..\..\windows\win.ini
```
This would be an example of a windows system traversal.

> *Please remember to enable photos and css for files being retrieved or you wont be able to see all your possibilities.
---

