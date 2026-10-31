# Common obstacles to exploiting path traversal vulnerabilities

> Many applications that place user input into file paths implement defenses against path traversal attacks, but these can often be bypassed.

> **Tip:** Enable images in the proxy history. Some image parameters might not show if you don't.

If an application strips or blocks directory traversal sequences from the user-supplied filename, it might be possible to bypass it.

---

## 1. Referencing the filesystem root

*Use when you can't use `../` to begin with.*

```
filename=/etc/passwd
```

- This directly references the file without using any traversal sequences (might work).
- Only works if the server/back end treats the parameter as being relative to a default working directory.

## 2. Nested traversal sequences

*Use when `../` is being removed by the server.*

Use `....//` or `....\/`.

- These turn into normal traversal sequences when the inner sequence is stripped.
- `....//` → `../`
- This works because the server usually removes instances of `../`, so doubling them up leaves one behind.

## 3. URL encoding

*Use when web servers are stripping any directory traversal sequences before passing your input to the application.*

URL-encode your payload, and double-encode it if needed.

- `../` → `%2e%2e%2f`
- Unorthodox encodings may work as well:
  - `..%c0%af` or `..%ef%bc%8f`

> **Tip:** Burp Suite offers a predefined payload list that makes it easier to try these.
> - File name: **Fuzzing - path traversal**

## 4. Validation of the start of the path

If an application requires the user-supplied filename to start with the expected base folder, such as `/var/www/images`, you would use:

```
filename=/var/www/images/../../../etc/passwd
```

## 5. Validation of the file extension

Sometimes an application may require the user-supplied filename to end with an expected file extension, such as `.png`.

In this case, we can use a null byte to effectively terminate the file path before the required extension:

```
filename=../../../etc/passwd%00.png
```
