---
title: "Getting started with ResourceKit"
summary: "Implement a typed provider, create a cache, and distinguish found from missing values."
category: "Guide"
order: 10
keywords: ["ResourceProvider", "ResourceResult", "ResourceCache", "example"]
related: ["index.md", "api.md", "typed-api.md"]
---

# Getting started

Import `resourcekit`. Implement `ResourceProvider Of String` and its
`Fetch(key As String) As ResourceResult Of String` method. For a known key return
`ResourceResult Of String.Present(text, 1000000, declaredBytes)` to cache for one
second. Otherwise return `ResourceResult Of String.Missing()`.

Assign the provider to an interface-typed local, then make an
`Array Of ResourceProvider Of String` from that local. Create
`New ResourceCache Of String(providers, 16, 65536)`. `cache.Fetch(key)`
returns a result. Inspect `IsFound` before reading `Value`, because missing Value
access throws. Multiple providers are searched in their constructor order; a
provider exception propagates instead of falling through.

Use `Invalidate(key)` after a resource changes, or `Clear()` to release all
cached results. Zero TTL deliberately bypasses caching. The cache does not watch
files, reload resources automatically or deep-copy mutable payloads.

Maintainers run the private validation suite before release. See the repository README for public build instructions.

See the [API reference](api.md) and [overview](index.md).
