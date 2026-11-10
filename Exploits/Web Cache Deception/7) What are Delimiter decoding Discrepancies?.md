# Delimiter decoding discrepancies

## Context

Websites sometimes need to send data in the URL that contains characters with special meaning in URLs, such as delimiters. To ensure these characters are interpreted as data (not structure), they're usually **encoded**.

However, some parsers **decode** certain characters before processing the URL. If a delimiter character gets decoded, it may then be treated as a delimiter — truncating the URL path.

> Differences in *which* delimiter characters are decoded by the cache vs. the origin server can cause discrepancies in how each interprets the URL path, **even if they both use the same characters as delimiters**.

---

## Case 1: Origin decodes, cache doesn't

Consider: `/profile%23wcd.css`

| Server | Behavior |
| --- | --- |
| **Origin server** | Decodes `%23` → `#`. Uses `#` as a delimiter → interprets path as `/profile` → returns profile information. |
| **Cache** | Also uses `#` as a delimiter, but does **not** decode `%23` → interprets path as `/profile%23wcd.css` (literal string). |

If there's a cache rule for the `.css` extension, it will store the response — caching the profile data under a `.css`-looking URL.

---

## Case 2: Cache applies rules on encoded URL, then decodes and forwards

Some cache servers decode the URL first, *then* forward the decoded request. Others apply cache rules to the **encoded** URL first, then decode and forward it to the next server. This ordering difference can also create discrepancies.

Consider: `/myaccount%3Fwcd.css`

1. **Cache server** applies cache rules to the encoded path `myaccount%3Fwcd.css` → decides to store the response (matches the `.css` extension rule). It then decodes `%3F` → `?` and forwards the rewritten request.
2. **Origin server** receives `/myaccount?wcd.css`. It uses `?` as a delimiter → interprets the path as `/myaccount`.

---

## Exploiting a decoding discrepancy

You may be able to exploit this by using an **encoded delimiter** to add a static extension to the path as seen by the cache, but not by the origin server.

- Use the same testing methodology as for plain delimiter discrepancies, but test a range of **encoded** characters instead.
- Be sure to test encoded **non-printable characters** too, particularly:
  - `%00`
  - `%0A`
  - `%09`

If these are decoded by either server, they can also truncate the URL path.

> **Why aren't these three in the standard delimiter list?**
>
> The official PortSwigger delimiter list only covers *printable* ASCII characters (`!` through `~`), paired with their encoded form (`;` / `%3B`, `#` / `%23`, etc.). `%00`, `%0A`, and `%09` don't have a meaningful raw/literal form you can type into a request, so they don't fit that raw+encoded pairing.
>
> They're also a different bug class under the hood:
> - The printable list tests **framework routing quirks** — characters with defined meaning in URL/path syntax (matrix variables, format extensions, fragments).
> - The control characters test **decoder/parser-level quirks** — null-terminated string handling, line-based HTTP header parsing, etc. This is closer to classic null-byte injection / CRLF injection than to "delimiter" behavior.
>
> Treat them as a required add-on to the list, not an oversight in it — see [the delimiter wordlist](delimiter-wordlist.txt) in this folder, which appends them to the base set.
