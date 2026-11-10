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

