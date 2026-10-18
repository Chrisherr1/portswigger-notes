# Constructing a web cache deception attack

Things that are involved to construct an attack:

1. **Identify a target endpoint that returns a dynamic response.**
   - Focus on endpoints that support the `GET`,`HEAD`,or `OPTIONS` methods as requests that alter the origin server's state are generally not cached.

2. **Identify a discrepancy in how the cache and origin server parse the URL path.**
   - This could be a discrepancy in how they..
     - Map URLs to resources
     - Process delimiter characters
     - Normalize paths

3. **Craft a malicious URL that uses the discrepancy to trick the cache into storing a dynamic response.**
   - This works by when the victim accesses the URL, their response is stored in the cache.

   Using Burp, you can send a request to the same URL to fetch the cached response containing the victim's data.

> *Avoid doing this directly in the browser as some applications redirect users without a session or invalidate local data which could hide a vulnerability.*

---

## Burp Cache setup

While testing for discrepancies and crafting a deception exploit, make sure that each request you send has a **different cache key**.

Otherwise, you may be served cached responses, which will impact your test results.

As both URL path and any query parameters are typically included in the cache key, you can change the key by adding a query string to the path and changing it each time you send a request.

It is recommended to automate this process using the `Param Miner` extension.

To do this, once you've installed the extension, click on the top-level **Param miner > Settings** menu, then select **Add dynamic cachebuster**.

Burp now adds a unique query string to every request that you make. You can view the added query strings in the **Logger** tab.

---

## Detecting Cached Responses

During testing, it's important you're able to idenitify cached responses.
Look at the response headers and response times.

Various headers may indicate that it is cached.

### `X-Cache`

`X-Cache` header provides information about whether a response was served from the cache.

| Header | Meaning |
| --- | --- |
| `X-Cache: hit` | Response was served from cache |
| `X-Cache: miss` | The cache did not contain a response for the request's key, so it was fetched from the  origin server. |
| `X-Cache: dynamic` | The response is dynamically generated and thus is not suitable for caching. |
| `X-Cache: refresh` | The cached content was outdated and needed to be refreshed or revalidated. |

### `Cache-Control`

`Cache-Control` may include a directive that indicates caching.

- `Public` with `max-age` higher than 0. suggests that the resource is cacheable.
- Not always indicated of caching tho as cache may sometimes override this header.

> *Cached responses are often fast, so if the response is fast it could be cached.*

---

## Static extension cache rules context

Cache rules often target static resources by matching common extensions like

`.css` or `.js`

These are often static.

If there are discrepancies on how the cache and origin server map the URL path to resources or delimiters,
An attacker amy be able to craft a request for a dynamic resource with a static extension that is ignored by the origin server but viewed by the cache.

---

## Path Mapping Context

URL path mapping is the process of associating URl paths with resources on a server, such as files,scripts, or command executions.

Mappings may differ depending on what frameworks you're dealing with.

Two of the most common styles are..
1. Traditional URL mapping
2. RESTful URL mapping

### Traditional URL mapping

Traditional URL mapping usually represents a direct path to a resource located on the file system.

```
http://example.com/path/in/filesystem/resource.html
```

It points to the server, and then looks for it using a directory path in the server's file system.

### REST-style URLs

REST-style URLs don't directly match the physical file structure.

They abstract file paths into logical parts of the API

```
http://example.com/path/resource/param1/param2
```

It points to the server then /param/param are path parameters used by the server to process the request.

---

## Path mapping discrepancies

Discrepancies in how the cache and origin server map the URL path to resources can result in web cache deception vulnerabilities.

```
http://example.com/user/123/profile/wcd.css
```

- **Origin server (REST-style):** A origin server using REST-style URL mapping may interpret this as a request for the `/user/123/profile` endpoint.
  It therefore returns the profile information for `user=123`
  ignoring the `wcd.css` as a non-significant parameter.

- **Cache (traditional):** A cache that uses traditional URL mapping may view this as a request for a file named...
  `wcd.css` located in the directory `/user/123/profile`.

If the cache is configured to store responses for requests where the path ends in `.css`, it would cache and serve the profile information as if it were a CSS file.

---

## Testing for path mapping discrepancies

### Origin server

To test how the origin server maps the URL path to resources, add an arbitrary path segment to the URL of your target endpoint.

If the response still contains the same sensitive data as the base response, it indicates that the origin server abstracts the URL path and ignores the added segment.

eg: `/api/orders/123` -> `/api/orders/123/foo`
- This will still return the order information.

### Cache

To test how the cache maps the URL path to resources, you'll need to modify the path to attempt to match the cache rules by adding a static extension.

```
/api/orders/123/foo.js
```

The response for this will indicate (if the response is cached)

1. That the cache interprets the full URL path with the static extension
2. That there is a cache rule to store responses for requests ending in `.js`

Caches may have rules based on specific static extension.
Try a range of extensions, including `.css`,`.ico`, `.exe`

You can then craft a URL that returns a dynamic response that is stored in the cache.

Not that this attack is limited to a specific endpoint that you tested, as the origin server often has different abstraction rules for different endpoints.

> *Burp Scanner automatically detects web cache deception vulnerabilities that are caused by path mapping discrepancies during audits. You can also use the Web Cache Deception Scanner BApp to detect misconfigured web caches.*
