# ResourceKit 0.1.0

`Import resourcekit` adds typed ordered resource providers and a bounded LRU
cache. This package is synchronous and **single-threaded**. It neither fetches
network resources itself nor selects an HTTP caching policy.

Implement `ResourceProvider Of T.Fetch(key As String) As ResourceResult Of T`.
Return `ResourceResult Of T.Present(value, ttlMicroseconds = 0, declaredBytes = 0)`
or `.Missing()`. `IsFound` distinguishes a missing result from a valid empty or
Nil payload. Reading `Value` on Missing throws `InvalidArgumentException`.
Results have read-only `TTL` and `DeclaredBytes`; mutable payloads retain normal
reference identity and are not deep copied. Negative TTL/cost throws.

Create `New ResourceCache Of T(providers, maxEntries = 1000,
maxDeclaredBytes = 67108864)` for the system clock, or use the four-argument
constructor `(providers, maxEntries, maxDeclaredBytes, clock)` for an injected
clock. Explicit Nil in the latter also selects the system clock. Construct the
provider array using interface-typed elements, as shown in the example; an array
of concrete subclasses is not implicitly converted to an interface array.
Provider order is copied on creation;
there may be zero through 1024 providers, with no Nil elements. The first found
result wins. Provider exceptions stop the chain and propagate; a Nil result
object is a provider contract error. Keys match exactly and case-sensitively,
including empty keys, and are limited to 4096 UTF-8 bytes.

`Fetch(key)` returns a cached or provider result. Hits move entries to the most
recently used end; successful insertions evict least recently used entries until
both limits fit. Entry capacity is 1..100000. The declared-byte budget is any
nonnegative Integer. Costs are supplied by the provider and are **not a measured
memory quota**: mutable objects can grow, zero-cost values still own memory, and
keys/metadata/backing array allocation are additional memory. The entry and key
limits bound metadata growth. Accounting checks avoid Integer overflow.

TTL zero means returned without caching, not forever; no negative caching or
never-expire mode exists. Oversize results are returned without caching or
evicting existing values. Cacheable TTL begins after the provider returns
successfully. A hit does not refresh TTL. An entry expires at the exact deadline.
An expiry beyond Integer.MaxValue is returned uncached rather than wrapping.

The default `ResourceClock.NowMicroseconds()` uses monotonic System.Microseconds.
Pass a custom clock for deterministic tests. Negative or regressing clock samples
throw. The same cache requires one continuous monotonic clock epoch, including
across Clear. Clock exceptions propagate. Fetch samples once before lookup and,
for a cacheable successful provider result, again after the provider returns.

`Invalidate(key)` removes an exact key and reports whether it existed; `Clear()`
releases all cached results. `PruneExpired()` samples the clock, removes all
expired entries, and returns their count. `Count` and `CachedBytes` observe stored
entries without sampling time, so expired entries remain included until the next
Fetch/PruneExpired. `Capacity` and `DeclaredByteLimit` report configured limits.

Provider, clock and payload-destructor callbacks cannot reenter Fetch, PruneExpired, Invalidate or
Clear on the same cache: those operations throw while Fetch, PruneExpired,
Invalidate or Clear is in progress. Observation properties are permitted.
Finally restores the guard after errors. A failed fetch may already have removed expired entries, but does not
cache a failed provider result. Callbacks may use a different cache. There is no
locking; callers must keep each cache and its mutable collaborators on one
thread. Release any provider/clock back-reference to the cache to avoid ARC
ownership cycles.

Maintainers run the private validation suite before release. See the repository README for public build instructions.
