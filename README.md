# PortSwigger Web Security Academy Notes

My personal study notes from the PortSwigger Web Security Academy, organized by vulnerability class and topic.

---

## At a Glance

| | |
|---|---|
| **Source** | [PortSwigger Web Security Academy](https://portswigger.net/web-security) |
| **Focus** | Hands-on web exploitation — theory, methodology, and solved labs |
| **Tooling** | Burp Suite (Proxy, Repeater, Param Miner) |
| **Format** | Concept notes + attack construction + walkthrough per topic |

---

## Topics

| Topic | Status |
|---|---|
| Web Cache Deception | 🚧 In progress |

---

## Contents

### Web Cache Deception

**Concepts**

- [1) What are Web Caches?](<Web Cache Deception/1) What are Web Caches?.md>) — how caches work, cache keys, cache rules, static extension / directory / file-name rules
- [2) Constructing an Attack](<Web Cache Deception/2) Constructing an Attack.md>) — attack methodology, Burp cache setup with Param Miner, detecting cached responses, path mapping discrepancies and how to test for them
- [4) What are Delimiter Discrepancies](<Web Cache Deception/4) What are Delimiter Discrepancies.md>) — framework-specific delimiter quirks (Java Spring `;`, Ruby `.`), testing methodology, encoded delimiters
- [7) What are Delimiter Decoding Discrepancies?](<Web Cache Deception/7) What are Delimiter decoding Discrepancies?.md>) — decoding mismatches between cache and origin, why `%00`/`%0A`/`%09` need separate testing
- [8) Exploiting Static Cache Rules](<Web Cache Deception/8) Exploiting static cache rules.md>) — directory-prefix cache rules (`/static`, `/assets`, etc.)
- [9) What are Normalization Discrepancies?](<Web Cache Deception/9) What are Normalization Descrepancies?.md>) — path traversal via cache/origin normalization mismatches
- 10) Exploiting Normalization by the Origin Server — 🚧 not yet written

**Labs**

- [3) Lab: Exploiting path mapping for web cache deception](<Web Cache Deception/3) Lab: Exploiting path mapping for web cache deception.md>) — walkthrough and breakdown of *why* the path-mapping exploit works
- [5) Lab: Exploiting path delimiters for web cache deception](<Web Cache Deception/5) Lab: Exploiting path delimiters for web cache deception.md>) — walkthrough using a delimiter discrepancy

**Reference**

- [6) Checkpoint: My Understanding](<Web Cache Deception/6) Checkpoint.md>) — self-check summary of the attack logic so far
- [Key Considerations](<Web Cache Deception/Key Considerations.md>) — practical gotchas that don't fit a single lesson (e.g. CDN/PoP cache locality)
- [delimiter-wordlist.txt](<Web Cache Deception/delimiter-wordlist.txt>) — PortSwigger's delimiter test list plus non-printable control characters

---
