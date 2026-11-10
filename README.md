# PortSwigger Web Security Academy Notes

My personal study notes from the PortSwigger Web Security Academy, organized by vulnerability class and topic.

---

## At a Glance

| | |
|---|---|
| **Source** | [PortSwigger Web Security Academy](https://portswigger.net/web-security) |
| **Focus** | Hands-on web exploitation — theory, methodology, and solved labs |
| **Tooling** | Burp Suite |
| **Format** | Concept notes + attack construction + walkthrough per topic |

---

## Topics

| Topic | Status | Learning path progress |
|---|---|---|
| Web Cache Deception | 🚧 In progress | 23 / 36 |
| WebSockets Vulnerabilities | DONE | 19 / 19 (29 Oct 2026) |
| Path Traversal | DONE | 14 / 14 (31 Oct 2026) |
| API Testing | DONE | 29 / 29 (31 Oct 2026) |
| GraphQL API Vulnerabilities | DONE | 29 / 29 (02 Nov 2026) |
| Authentication Vulnerabilities | DONE | 55 / 55 (06 Nov 2026) |
| Web LLM Attacks | DONE | 30 / 30 (09 Nov 2026) |

> Some completed topics don't have notes for every section yet. I may revisit those later.

### Other learning paths (no notes yet)

| Learning path | Level | Progress |
|---|---|---|
| Server-side vulnerabilities | Apprentice | 52 / 52 (05 Apr 2026) |
| Cross-origin resource sharing (CORS) | Practitioner | 21 / 21 (01 Apr 2026) |
| SQL injection | Practitioner | 36 / 51 |
| File upload vulnerabilities | Practitioner | 21 / 35 |
| Clickjacking (UI redressing) | Practitioner | 1 / 19 |
| NoSQL injection | Practitioner | 1 / 24 |
| Cross-site request forgery (CSRF) | Practitioner | 0 / 46 |
| Server-side request forgery (SSRF) | Practitioner | 0 / 23 |
| Prototype pollution | Practitioner | 0 / 65 |
| Race conditions | Practitioner | 0 / 29 |

---

## Roadmap

PortSwigger has two formats: **learning paths** (guided, with a progress counter) and **topics** (article pages with labs, no path counter). Some vulnerability classes only exist as topics. This roadmap uses both.

### Phase 1 — Recon & attack surface (before exploitation)

Learn what the app exposes, who can do what, and how its workflows run.

| # | Section | Format | Status |
|---|---|---|---|
| — | API Testing | Learning path | DONE |
| — | GraphQL API Vulnerabilities | Learning path | DONE |
| — | Authentication Vulnerabilities | Learning path | DONE |
| — | Web LLM Attacks | Learning path | DONE |
| 1 | Information disclosure | Topic (~5 labs) | 🚧 In progress |
| 2 | Essential skills | Topic | Not started |
| 3 | Access control | Topic (~13 labs) | Not started |
| 4 | Business logic vulnerabilities | Topic (~11 labs) | Not started |
| opt | JWT / OAuth | Topic | Optional (could wait for Phase 2) |

> Lab counts are approximate. Check each topic page.

### Phase 2 — Exploitation

Start with the paths that are closest to done, then work through the rest.

1. SQL injection (36 / 51)
2. File upload vulnerabilities (21 / 35)
3. Web Cache Deception (23 / 36)
4. Remaining: SSRF, CSRF, NoSQL injection, Clickjacking, Race conditions, Prototype pollution

---

## Contents

### Web Cache Deception

**Concepts**

- [1) What are Web Caches?](<Exploits/Web Cache Deception/1) What are Web Caches?.md>)
- [2) Constructing an Attack](<Exploits/Web Cache Deception/2) Constructing an Attack.md>)
- [4) What are Delimiter Discrepancies](<Exploits/Web Cache Deception/4) What are Delimiter Discrepancies.md>)
- [7) What are Delimiter Decoding Discrepancies?](<Exploits/Web Cache Deception/7) What are Delimiter decoding Discrepancies?.md>)
- [8) Exploiting Static Cache Rules](<Exploits/Web Cache Deception/8) Exploiting static cache rules.md>)
- [9) What are Normalization Discrepancies?](<Exploits/Web Cache Deception/9) What are Normalization Descrepancies?.md>)
- 10) Exploiting Normalization by the Origin Server — 🚧 not yet written



### WebSockets Vulnerabilities

- [Intro To Web Sockets](<Exploits/WebSocketsVulnerabilities/Intro To Web Sockets.md>)
- [Understanding Exploitation](<Exploits/WebSocketsVulnerabilities/Understanding Exploititation.md>)
- [Securing a WebSocket](<Exploits/WebSocketsVulnerabilities/Securing a WebSocket.md>)

### Path Traversal

- [Intro to Path Traversal](<Exploits/Path Traversal/Intro to Path Traversal.md>)
- [Common Obstacles](<Exploits/Path Traversal/Things in your way.md>)
- [Prevention](<Exploits/Path Traversal/Prevention.md>)

### API Testing

**API Recon**

- [Introduction](<Recon/API testing/API Recon/Introduction.md>)
- [Mass Assignment Vulnerabilities](<Recon/API testing/API Recon/Mass assignment Vulns.md>)
- [Preventing Vulnerabilities in APIs](<Recon/API testing/API Recon/Preventing Vulnerabilities in APIs.md>)

**Server-Side Parameter Pollution**

- [Introduction to Server-Side Parameter Pollution](<Recon/API testing/Server-Side Parameter Pollution/Introduction to Server-Side Parameter Pollution.md>)
- [Testing for Server-Side Parameter Pollution](<Recon/API testing/Server-Side Parameter Pollution/Testing for server-side parameter pollution.md>)
- [Preventing Server-Side Parameter Pollution](<Recon/API testing/Server-Side Parameter Pollution/Prevention.md>)

### GraphQL API Vulnerabilities

- [1) Finding GraphQL Endpoints](<Recon/GraphQl API vulnerabilities/1.GraphQL.md>)
- [2) Bypassing Introspection Defenses](<Recon/GraphQl API vulnerabilities/2.Bypassing Introspection defences.md>)
- [3) Bypassing Rate Limiting Using Aliases](<Recon/GraphQl API vulnerabilities/3.Bypassing rate limiting using aliases.md>)
- [4) GraphQL CSRF](<Recon/GraphQl API vulnerabilities/4. GraphWL CSRF.md>)
- [5) Prevention](<Recon/GraphQl API vulnerabilities/5. Prevention.md>)

### Authentication Vulnerabilities

- [Introduction](<Recon/Authentication Vulnerabilities/Introduction.md>)
- [Approaching a Brute Forcing Login](<Recon/Authentication Vulnerabilities/Approaching Brute Forcing Login.md>)
- [Vulnerabilities in Multi-Factor Authentication](<Recon/Authentication Vulnerabilities/Brute Forcing Multi-factor Authentication.md>)
- [Preventing Auth Attacks](<Recon/Authentication Vulnerabilities/Preventing Auth Attacks.md>)

### Web LLM Attacks

- [1) Introduction](<Recon/Web LLM Attacks/1. Introduction.md>)
- [2) Training Data Poisoning](<Recon/Web LLM Attacks/2. Training Data Poisoning.md>)
- [3) AI-Powered Security Scanners](<Recon/Web LLM Attacks/3. AI Security Scanners.md>)
- [Prevention](<Recon/Web LLM Attacks/Prevention.md>)

### Smaller Topics

- [Information Disclosure](<Smaller Topics/Information Disclosure.md>)

---
