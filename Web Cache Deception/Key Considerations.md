# Key Considerations

Practical caveats and "gotchas" worth keeping in mind that don't fit neatly into a single lesson file.

---

## CDN topology: the cache isn't one cache

Exploiting a web cache (deception or poisoning) doesn't require **physical proximity** to the cache — anyone on the internet can send a request to a CDN edge node, there's no network-level barrier. What it *does* require is that the attacker and the victim end up hitting the **same cache instance**.

### Why this matters

A CDN usually isn't one single cache — it's many separate caches, one (or a small cluster) per **edge location / PoP (point of presence)**. CDNs route each user to whichever PoP is geographically/network-closest to *them*.

- If you populate a cache entry at the PoP closest to **you**, and the victim is in a different region, their request may route to a **different PoP** with its own separate cache — one that never saw your crafted request. The attack silently fails even though it appeared to work from your own vantage point.
- If the victim happens to be routed to the **same PoP** as you (same region, same ISP, or the CDN's routing logic overlaps), the attack works cleanly.

### Practical implications

1. **Recon the CDN topology first** — is it a single centralized cache, or many regional ones?
2. **Check for "tiered caching" / "cache shielding"** — Cloudflare, Fastly, and Akamai all offer configs where regional edge caches fall back to one shared "shield" cache. If enabled, the effective cache is centralized and PoP locality stops mattering.
3. **Use multiple vantage points if the victim's location is unknown** — trigger the request from several regions (VPN / cloud nodes in different geographies) to maximize the odds of landing on the same PoP the victim will hit. Verify per-location using `X-Cache` / `Age` response headers.
4. **Often moot in practice** — many real targets (and most labs) sit behind a single reverse proxy cache (e.g. Varnish) or a non-geo-distributed setup, not a true multi-region CDN. In that case there's exactly one cache, and this whole concern disappears.

> Note: this is reasoning from general CDN architecture (anycast routing to regional PoPs, with shielding/tiered caching as the standard mitigation), not a direct quote from PortSwigger or Kettle's published research — worth independently verifying against a specific target's CDN docs before relying on it.
