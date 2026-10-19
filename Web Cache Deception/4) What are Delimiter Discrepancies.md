# Delimiter discrepancies

## Context: What are delimiters?

Delimiters specify boundaries between different elements in URLs.

The use of characters and strings as delimiters is generally standardized.

Eg:
- `?` - is generally used to separate the URL path from the query string

> *However, URL RFC is quite permissive, variations still occur between different frameworks or technologies.*

Discrepancies in how the cache and origin server use characters and strings as delimiters can result in web cache deception vulnerabilities.

Consider: `/profile;foo.css`

---

## Specific: Java Spring

The Java Spring framework uses the `;` character to add parameters known as matrix variables.

- An origin server that uses Java Spring would therefore interpret `;` as a delimiter.
- It truncates the path after `/profile` and returns profile information.

Most other frameworks don't use `;` as a delimiter...

- Therefore a cache that doesn't use Java Spring is likely to interpret `;` and everything after it as part of the path.
- If the cache has a rule to store responses for requests ending in `.css`, it might cache and serve the profile information as if it were a CSS file.

> The same is true for other characters that are used inconsistently between frameworks or technologies.

---

## Specific: Ruby on Rails

Ruby uses `.` as a delimiter.

therefore...

| Request | Result |
| --- | --- |
| `/profile` | default |
| `/profile.css` | request is rejected since there isn't a CSS formatter. |
| `/profile.ico` | Ruby doesn't recognize this extension. |

In this situation, if the cache is configured to store responses for requests ending in `.ico`, it would cache and serve the profile information as if it were a static file.

---

## Encoded characters as delimiters

Encoded characters may also sometimes be used as delimiters.

`/profile%00foo.js`

- The OpenLiteSpeed server uses the encoded Null `%00` character as a delimiter.
- Most other frameworks respond with an error if `%00` is in the URL.
- However, if the cache uses Akamai or Fastly, it would interpret `%00` and everything else after it as the path.

---

## Exploiting delimiter discrepancies

You might be able to use a delimiter discrepancy to add a static extension to the path that is viewed by the cache, **BUT NOT** the origin server.

To do this, you're going to need to identify a character that is used as a delimiter by the origin server but not the cache.

### Step 1: Find characters that are used as delimiters by the origin server.

Start this process by adding a random string to the URL of your target endpoint.

eg: Modify `/settings/users/list` -> `/settings/users/listaaa`

You can then use this response as a reference when you start testing delimiter characters.

> *If the response is identical to the original response, this indicates that the request is being redirected. You'll need to choose a different endpoint to test.*

### Step 2: Add a possible delimiter character between the original path and the arbitary string.

eg: `/settings/users/list;aaa`

Possible outcomes:

1. The response is identical to the base response.
   This indicates that the `;` character is used as a delimiter and origin server interprets the path as `/settings/users/list`

2. If it matches the response to the path with the arbitrary string, this indicates that the `;` character isn't used as a delimiter and the origin server interprets the path as `/settings/users/list;aaa`

### Step 3: Once you've identified delimiters that are used by the origin server, test whether they're also used by the cache.

To do this, ADD a static extension to the end of the path.

If the response is cached, this indicates:

1. That the cache doesn't use the delimiter and interprets the full URL path with the static extension.
2. That there is a cache rule to store responses for requests ending in `.js`.

**IMPORTANT**
> *Make sure to test all ASCII characters AND a range of common extensions including `.css`,`.ico`,`.exe`.*

Portswigger gave us a starter list:
https://portswigger.net/web-security/web-cache-deception/wcd-lab-delimiter-list

> **TURN OFF AUTOmATED CHARACTER ENCODING when using the list.**

### Step 4: You can then construct an exploit that triggers the static extension cache rule.

consider: `/settings/users/list;aaa.js`

eg: We determined that the origin server uses `;` as a delimiter.

- The cache interprets the path as `/settings/users/list;aaa.js`
- The origin server interprets the path as `/settings/users/list`

The origin server therfore returns the synamic profile information which is stored in cache.

Because delimiters are generally used consistently within each server, you can often use this attack on many different endpoints.

> Some delimiter characters may be processed by the victim's browser before it forwards the request to the cache. This means that some delimiters can't be used in an exploit. For example, browsers URL-encode characters like `{`, `}`, `<`, and `>`, and use `#` to truncate the path.

If the cache or origin server decodes these characters, it may be possible to use an encoded version in an exploit.