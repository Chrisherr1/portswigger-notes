# Information disclosure vulnerabilities

## what is information disclosure ?

It is whe information leaks and unintentionally reveals sensitive information.

Information may vary. (User data, business data, technical details about server).

> Usually a starting point for exposing additional attack surface. And may be the last thing you need to do a high severity attack.

Eg of disclosure:

- Robots.txt revealing directories and their content.
- Providing access to source code files via temp backups
- mentioning database table or column names in error messages.
- Exposing high sensitive information like credit card details.
- Hard coding API keys, IP addresses, database credentials, and so on in the source code.
- Hinting the existence or absence of resources, usernames, and so on using the application's behavior.

## How to find an exploit information disclosure vulnerabilities

> It is important not to develop `tunnel vision` during testing.

You should avoid focussing too narrowly on a particular vulnerability.

You will often find sensitive data while testing for other things.

> The trick is recognizing interesting information when you come across it.

### Common places to look:
```
Files for web crawlers
Directory listings
Developer comments
Error messages
Debugging data
User account pages
Backup files
Insecure configuration
Version control history
```

### Expanding : File for web crawlers

Many websites provide files at `/robots.txt and /sitemap.xml` to help crawlers navigate their site.

> These file often list specific directories that crawlers should skip(sensitive data)

> It's worth trying, burp doesn't always do this automatically.
---
### Expanding : Directory listings

Web servers can be configured to automatically list the contents of directories that do not have an index page present.

It increases the exposure of sensitive files within the directory, that are not intended to be accessible to users, such as crash dumps.

Directory listings are not necessarily a security vulnerability.

> However, if the website also fails to implement proper access control, leaking the existence and location of sensitive resources in this way is an issue.
---
### Expanding : Developer Comments

HTML comments sometimes are left in production.

They are usually removed during production but they can sometimes be left or forgotten.

> these comments occasionally contain information that is useful to us like a hidden directory or application logic.
---
### Expanding : Error Messages

A general rule, is to pay attention to all error messages you encounter during auditing.

> The content of error messages can reveal information about what input or data type is expected from a given parameter.

Verbose error messages could disclose information.

Differences between error message can also reveal different application behavior that is occurring behind the scenes.

> Observing differences in error messages is crucial to some techiniques like SQL injection, username enumveration, ect.

### Expanding : Debugging data

For debugging purposes, many websites generate custom error messages and logs that contain large amounts of information about the application's behavior.

> Useful in development, but can leak information in production

Debug messages can often contain info:

1.Values for key session variables that can be manipulated via user input

2.Hostnames and credentials for back-end components

3.File and directory names on the server

4.Keys used to encrypt data transmitted via the client

> Sometimes debugging information may sometimes be logged in a separate file. If an attacker is able to gain access to this file, it can serve as a useful reference for understanding the application.

### Expanding : User account pages

a user's profile or account page usually contains sensitive information,such as..

`User's email address, phone number, API key, and so on`

> Usually only an issue if logic flaws allow attackers to leverage the pages to see other people's data.

Consider : `GET /user/personal-info?user=carlos`

Most website would prevent people from changing the parameter.

However logic for loading individual items of data is not as robust.

> The attacker may not be able to load another user's account page, but could possibly change parameters linking to specific information instead.

### Expanding : Source code disclosure via backup files

Sensitive data is sometimes disclosed within the source code(API keys and credentials for accessing back-end components).

> If you can identify a particular open-source tech is being used, this can be used to see the code, since open source means its freely to see on github.

Sometimes you'll find pages with the extension shown (.php,.html,ect) sometimes you can trick the server to send a file instead.

> Text editiors often generate temporary backup files while the original file is being edited. These temp files are usually indicated in some way (~) to the filename or adding a different file extension.

Requesting a code file using a backup fule extension can sometimes allow you to read the content of the file in the response.

> Source code is a HUGE advantage since you can see things more clearly.

### Expanding : Information disclosure due to insecure configuration

Website are sometimes vulnerable as a result of improper configuration.

Developers might disable various debugging options in the production enviroment.

HTTP TRACE method is used for diagnostic purposes.
    - If enabled, the web server will respond to requests that use the TRACE method by echoing the response the exact request that was received.

This behavior is often harmless, but occasionally lead to information disclosure, such as the name of internal authentication headers that may be appended to requests by reverse proxies.

## Version Control History

Virtually all websites are developed using some form of version control system, such as Git.

> By default a git project stores all version control data in `.git`.

Occasionally website expose this directory in the production environment.

In this case, you might be able to acess it by simply browsing to /.git

> You can end up downloading the `.git` directory. TO look through it, it may have logs containing useful information.

> This will not give you access to full source code, but comparing the diff will allow you to read snippets of code. And some sensitive data might be in some of the lines changed.

## Prevention

> This can be very tricky due to the variet of ways it can occur.

1. Make sure everyone involved in producing the website is fully aware of what information is considered sensitive.

2. Audit any code for potential information disclosure as part of your QA or build processes.
    - Automate stripping developer comments.

3. Double-check that any debugging or diagnonstic features are disabled in production environment.

4. Make sure you fully understand the configuration settings, and security implications, of any third part tech that you implemented.

Take the time to investigate and disable any features and settings you don't actually need.



