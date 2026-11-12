# Essential Skills: Encoding

These are broadly applicable skills that will help you apply what you've learned from the labs to other live agents.

## Obfuscating attacks using encodings

Most websites will already be hardened so don't expect it to be easy.

You'll need to use techniques to overcome blocks.

### In this section we will go over ways to obfuscate harmful payload to evade input filters and other flawed defenses.

Specifically, you'll learn how to use standard encodings to **take advantage of misconfigurations** and **handling discrepancies between connected systems**.


> Burp Scanner can help do this, it automates the tedious testing, and helps you not over look things.

> This helps you concentrate your time and effort on things that can't be easily automated, such as working out how to exploit the vulnerable behavior or chain it with your other findings.


> Use Burp AT, with the labs to discuss testing ideas, understand unexpected results, and review your evidence.

## Obfuscating attacks using encodings

These are common ways to evade **input filters** and **inject harmful payloads** for a variety of attacks such as **XSS and SQL injection**.

Both Client and servers use different encodings to pass data between systems.

When they want to use the data they often have to decode it first.

The sequence is often dependednt in the context the data appears.

Eg:

a query parameter is typically URL decoded server-side.

A text content of a HTML element may be HTML decoded client side.

> When Constructing an attack you should think about where your payload is being injected.

> If you can infer how your input is being decoded based on this context you can potentially identify alternative ways to represent the same payload.

## Decoding Discrepancies

Injection attacks often involve injecting payloads that use recognizable patterns such as **HTML tags, Javascript functions, or SQL statements.**

> Inputs for these never expect a user to give code, so they often will defend these against code.

However, these kinda of input filters also need to decode the input in order to check if its safe or not.

> It is vital that the input it decoded FIRST before the data is used, as any encoded value could be code that is being smuggled in past the filters.(Whether its being used front or back)


## Obfuscating via URL encoding

Tools:
&(used as a delimiter to separate paramters in the query string)

We can use this to our advantage..

Often Browsers will automatically URl encode any characters hat may cause ambiguity for parsers.

> This often means subsituting them with a `%` character and their hexcode. & -> %26

This is ment to make the special character not effect the query.

> Space is often turned into a +, instead of being encoded into `%20`

The server often decodes the incoming URL-based input before using it..

So %22,%3C and %3E often get turned back into `",<,>`

> In some cases WAFs will fail to URL decode your input when checking it. In this case you might be able to smuggle payloads to the back-end application simply by encoding any characters or words that are blacklisted.

> SQL injection might encode the banned word `SELECT` into `%53%45%4C%45%43%54`.

## Obfuscation via double URL encoding

It is very common for back-end servers to preform 2 rounds of URl decoding on any URLs they receive.

This isn't an issue usually, and is often used to make sure that the query is FULLY decoded before being used.

Otherwise the attacker could just encode twice and get past the filters.

> Only Double encode the injection

## Obfuscation via HTML encoding

In HTML documents, certain characters need to be escaped or encoded to prevent the browser from incorrectly interpreting them as part of the markup.

Tools:

Prefix: `&`
Terminated : `;`

eg: `&colon;`

In specific locations within the HTML,such as the `text content` of the `element` or `value of an attribute`, browser will automatically decode these references when they parse the document.

> This is mostly used to obfuscate payloads for client side attacks, where you're attacking the code in the frontend.

Eg: XSS Payload

We are injecting a payload into a HTML attribute, onerror event handler.

If the server-side checks are looking for the `alert()` payload explicitly. They might not spot this HTML encoded version.

`<img src=x onerror="&#x61;lert(1)">`

> When the browser renders the page, it will decode and execute the injected payload.

## Leading Zeros in HTML encoding

When using decimal or hex style encoding, you can optionally include zeros.

Some WAFs don't account for this.

If your payload get blocked after HTML encoding it, you may find that you can evade the filter by adding a prefix of zeros.

`<a href="javascript&#00000000000058;alert(1)">Click me</a>`


## Obfuscation via XML encoding

XML is closely tied to HTML and supports character encoding using the same numeric escape sequences.

This enables you to include special characters in the text content of elements without breaking the syntax, which come in handy when testing for XSS via XML-based input, for example.

> Even if you don't need to encode any special characters to avoid syntax errors, you can use this behavior to obfuscate payloads the same way you do with HTML encoding.

The difference is that your payload is decoded by the server itself,rather than client side by the browser.

> This is useful for Bypassing WAFs and other filters, which may block your requests if they detect certain keywords associated with SQL injection attacks.

Eg:

```
<stockCheck>
    <productId>
        123
    </productId>
    <storeId>
        999 &#x53;ELECT * FROM information_schema.tables
    </storeId>
</stockCheck>
```

## Obfuscation via unicode escaping


