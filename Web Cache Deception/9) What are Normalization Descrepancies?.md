# What are normalization discrepancies?

## Context

**Normalization** converts various representations of a URL path into a standardized format. This sometimes includes decoding encoded characters and resolving dot-segments (`..`) — but how much normalization happens, and how, varies significantly from parser to parser.

> Discrepancies in how the cache and origin server normalize the URL can let an attacker construct a **path traversal payload** that each parser interprets differently.

---

## Example

Consider: `/static/..%2Fprofile`

| Server | Behavior |
| --- | --- |
| **Origin server** | Decodes `%2F` → `/` and resolves the `..` dot-segment → normalizes the path to `/profile` → returns profile information. |
| **Cache** | Does **not** decode slashes or resolve dot-segments → interprets the literal path as `/static/..%2Fprofile`. |

Since the cache sees a path that still starts with `/static`, if there's a cache rule for that prefix, it will cache and serve the profile information under the static-looking URL.

---

## Key requirement: encode the dot-segment

Each dot-segment in the path traversal sequence needs to be **encoded**. Otherwise, the victim's **browser** will resolve it itself before the request even reaches the cache — defeating the whole trick.

> **Therefore:** an exploitable normalization discrepancy requires that either the cache *or* the origin server:
> 1. Decodes the character in the path traversal sequence, **and**
> 2. Resolves the dot-segments.
