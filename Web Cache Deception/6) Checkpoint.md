# Checkpoint: My Understanding

## The goal

Get a cache to store someone else's **authenticated, sensitive response** under a URL that the attacker can also request **without logging in**.

## The setup you're looking for

An endpoint that:

- Returns session-specific data
- Is normally excluded from caching (no cache headers, or explicit `no-store`/`no-cache`)

## The two techniques

These are **parallel options, not sequential steps**. You try one, and if it fails, try the other — a target can be vulnerable to either, both, or neither.

| Technique | How it works |
| --- | --- |
| **Delimiter trick** | Inject a character the parser treats as "end of real path" (e.g. `;`), followed by a fake static filename. |
| **Path mapping trick** | Append an extra path segment ending in a fake static filename, relying on the router being lenient about trailing segments it doesn't need. |

## The two conditions that must both be true

Whichever technique you use, both of these have to hold — one without the other does nothing:

1. **Origin server** ignores the injected part and still runs the real handler, returning real sensitive data.
2. **Cache** looks at the URL string, sees something that looks like a static file extension, and decides to store it.

## Confirming it worked

- Check response headers — `X-Cache: hit`/`miss`, or `Age > 0` as a fallback.
- Confirm the returned content is the **real sensitive data**, not an error page.

## The actual attack

1. Send the crafted URL to a victim so their authenticated request populates the cache.
2. Request that same URL yourself to pull their data back out, unauthenticated.
