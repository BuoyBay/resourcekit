---
title: "ResourceKit"
summary: "Resolve typed resources through ordered providers with bounded LRU caching."
category: "Overview"
order: 0
keywords: ["resource", "provider", "cache", "LRU", "TTL"]
related: ["getting-started.md", "api.md", "typed-api.md"]
---

# ResourceKit

ResourceKit adds a synchronous provider chain and bounded LRU cache. It uses
statically typed `ResourceResult Of T` values, distinguishing missing data from
valid empty or Nil payloads. Providers determine cache TTL and declared cost.

Use each cache on one thread. The package has no network, filesystem, persistence
or HTTP-policy behavior. Keys match exactly and are bounded to 4096 UTF-8 bytes.
Entry counts and declared cost are bounded; declared cost is not actual memory
measurement and mutable values remain shared references.

Read [Getting started](getting-started.md) for a provider example, and the
[API reference](api.md) for expiry, eviction and callback rules.
