# API Recon

To start, we'd need to first find out as much information about the API as possible.

First step is usually identify all the API endpoints, this is where the APi recieves request about a specific resource on its server.

```text
GET /api/books HTTP/1.1
Host: example.com
```

Once you find all the endpoints, you can now test them.
---
You should be trying to find out...

### 1. The input data the API processes, including both compulsory and optional parameters.

### 2. The types of requests the API accepts, including supported HTTP methods and media formats

### 3. Rate limits and authentication mechanisms.
---

APIs are usually documented so developers know how to use them and integrate with them.

API documentation is often publicly available, epically if its a public facing and is meant to be used by developers. (unless its meant for an internal app.)


## What if there's no documentation?

You can make burp crawl the API. Via `Burp Scanner` or just browse the website and see the endpoints pop up(you might miss some stuff doe) in the proxy history.

And if you find a endpoint like this `/api/swagger/v1/users/12`, remmeber to try every directory in it(/api, /api/swagger/v1/users/12).

You can use a list of common paths to find documentation using intruder.

> Heavyweight extraction, use the JS Link Finder BApp.


## Test the Endpoint (GET,PUT,POST,PATCH,OPTIONS,HEAD)

An API endpoint may support different HTTP methods.

This may enable you to identify additional endpoint functionality, opening up more attack surface.

/api/tasks may support  GET,PUT,POST,DELETE versions that you can test.


## API endpoints will often expect data in a specific format 
eg: JSON

but.. sometimes changing the content type may enable you to:

    - Trigger errors that disclose useful information.
    - Bypass flawed defenses.
    - Take advantage of differences in processing logic. For example, an API may be secure when handling JSON data but susceptible to injection attacks when dealing with XML.

> You can use the Content type converter BApp to automatically convert data submitted within requests between XML and JSON.

## You can now use intruder to find hidden endpoints by using wordlists

    1. Burp Intruder enables you to automatically discover hidden parameters, using a wordlist of common parameter names to replace existing parameters or add new parameters. Make sure you also include names that are relevant to the application, based on your initial recon.

    2. The Param miner BApp enables you to automatically guess up to 65,536 param names per request. Param miner automatically guesses names that are relevant to the application, based on information taken from the scope.

    3. The Content discovery tool enables you to discover content that isn't linked from visible content that you can browse to, including parameters.


