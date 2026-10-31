# API Recon

To start, we first need to find out as much information about the API as possible.

The first step is usually to identify all the API endpoints. This is where the API receives requests about a specific resource on its server.

```http
GET /api/books HTTP/1.1
Host: example.com
```

Once you find all the endpoints, you can test them.

---

## What you should be trying to find out

1. The input data the API processes, including both compulsory and optional parameters.
2. The types of requests the API accepts, including supported HTTP methods and media formats.
3. Rate limits and authentication mechanisms.

---

## API documentation

APIs are usually documented so developers know how to use them and integrate with them.

API documentation is often publicly available, especially if the API is public-facing and meant to be used by developers (unless it's meant for an internal app).

## What if there's no documentation?

- You can make Burp crawl the API via **Burp Scanner**, or just browse the website and watch the endpoints pop up in the proxy history (you might miss some stuff, though).
- If you find an endpoint like `/api/swagger/v1/users/12`, remember to try every directory in it (`/api`, `/api/swagger/v1/users/12`).
- You can use a list of common paths with Intruder to find documentation.

> **Tip:** For heavyweight extraction, use the **JS Link Finder** BApp.

---

## Test the endpoint (GET, PUT, POST, PATCH, OPTIONS, HEAD)

An API endpoint may support different HTTP methods.

This may enable you to identify additional endpoint functionality, opening up more attack surface.

For example, `/api/tasks` may support `GET`, `PUT`, `POST`, and `DELETE` versions that you can test.

---

## Changing the content type

API endpoints will often expect data in a specific format (e.g., JSON).

But sometimes changing the content type may enable you to:

- Trigger errors that disclose useful information.
- Bypass flawed defenses.
- Take advantage of differences in processing logic. For example, an API may be secure when handling JSON data but susceptible to injection attacks when dealing with XML.

> **Tip:** You can use the **Content type converter** BApp to automatically convert data submitted within requests between XML and JSON.

---

## Finding hidden parameters with wordlists

1. **Burp Intruder** enables you to automatically discover hidden parameters, using a wordlist of common parameter names to replace existing parameters or add new parameters. Make sure you also include names that are relevant to the application, based on your initial recon.
2. The **Param Miner** BApp enables you to automatically guess up to 65,536 parameter names per request. Param Miner automatically guesses names that are relevant to the application, based on information taken from the scope.
3. The **Content discovery** tool enables you to discover content that isn't linked from visible content that you can browse to, including parameters.
