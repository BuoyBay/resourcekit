---
title: "ResourceKit API"
summary: "Typed result factories, ordered providers, LRU bounds, monotonic expiry and invalidation."
category: "Reference"
order: 20
keywords: ["ResourceCache", "expiry", "eviction", "clock", "invalidation"]
symbols: ["ResourceResult", "ResourceResult.Present", "ResourceResult.Missing", "ResourceResult.IsFound", "ResourceResult.Value", "ResourceResult.TTL", "ResourceResult.DeclaredBytes", "ResourceProvider", "ResourceProvider.Fetch", "ResourceClock", "ResourceClock.NowMicroseconds", "ResourceCache", "ResourceCache.Fetch", "ResourceCache.Invalidate", "ResourceCache.Clear", "ResourceCache.PruneExpired", "ResourceCache.Count", "ResourceCache.CachedBytes", "ResourceCache.Capacity", "ResourceCache.DeclaredByteLimit"]
related: ["index.md", "getting-started.md", "typed-api.md"]
---

# API reference

| API | Contract |
| --- | --- |
| `ResourceResult Of T.Present(value, ttlMicroseconds = 0, declaredBytes = 0)` | Found value, including Nil class payload; nonnegative TTL/cost required |
| `ResourceResult Of T.Missing()` | Missing result; `IsFound` False and `Value` throws |
| `ResourceProvider Of T.Fetch(key)` | Return an explicit result; Nil result objects reject |
| `ResourceClock.NowMicroseconds()` | Nonnegative, nondecreasing Integer ticks; default uses System.Microseconds |
| `New ResourceCache Of T(providers, maxEntries = 1000, maxDeclaredBytes = 67108864)` | Defensive provider-array copy; first found wins; 0..1024 non-Nil providers |
| `New ResourceCache Of T(providers, maxEntries, maxDeclaredBytes, clock)` | Inject custom clock; explicit Nil selects system clock |
| `Fetch(key)` | Exact case-sensitive key, at most 4096 UTF-8 bytes; lookup or provider call |
| `Invalidate(key)` | Remove exact key; return whether stored |
| `Clear()` | Release entries and zero accounting; preserve clock epoch |
| `PruneExpired()` | Remove all expired entries and return removal count |
| `Count`, `CachedBytes` | Stored entries/cost, including lazy-expired entries before pruning |
| `Capacity`, `DeclaredByteLimit` | Configured entry and declared-cost ceilings |

`New ResourceResult Of T()` also constructs a missing result. Provider-array
elements must be typed as `ResourceProvider Of T`; concrete subclass arrays do
not implicitly convert to interface arrays.

Entry capacity is 1..100000; declared-byte capacity is nonnegative. LRU hits move
to the newest position, without refreshing TTL. Insertions evict oldest entries
until both limits fit. Oversize values and zero-TTL values return uncached,
without evicting live entries. Missing results are not cached. Empty keys work.

TTL begins after a successful provider callback. The exact deadline is expired.
If adding TTL to the sampled time would overflow Integer, the result is uncached.
Clock regression, negative time and negative TTL/cost reject. Exact-key scans and
a single LRU movement are O(n). Expiry pruning and capacity eviction can repeatedly
shift bounded arrays and are O(n²) in the worst case; Fetch inherits this bound.
Provider execution is additional work.

The package is single-threaded. Provider, clock and payload-destructor callbacks cannot reenter Fetch,
PruneExpired, Invalidate or Clear on the same cache; observation properties work.
Exceptions propagate and Finally releases the busy guard. Earlier expiry pruning
may remain applied after a failed fetch. Providers and clocks remain shared
references; release back-references to avoid ARC cycles. Payloads are not cloned.
Declared bytes are provider accounting, not a measured memory quota.

Read [Getting started](getting-started.md) and the [overview](index.md).


[Full typed API: properties, parameters, defaults, and returns](typed-api.md)
