# Provenance

Inspired by Andrew Keller's REALbasic Common KFS BSD resource-manager interfaces,
ordered aggregate providers and cache-validity criteria. Inspected revision:
`9d0b41fad7f75ab5c91098feed20a9fdb8a5745a`, especially
[Common/Utilities/Resource Manager](https://github.com/andrewkeller/REALbasic-Common-KFS-BSD/tree/9d0b41fad7f75ab5c91098feed20a9fdb8a5745a/Common/Utilities/Resource%20Manager).
The study inventory is `examples/andrewkeller/inventory.json` in this repository.

This is an independent Buoy design and implementation. No upstream source,
artwork or tests were transcribed or redistributed. The upstream library's BSD
license and per-file contributor notices were inspected; they do not imply API
compatibility or ownership of this independent implementation.

Differences: statically typed generic results instead of Variant, explicit
Missing separate from found Nil, fixed copied provider precedence, bounded LRU
and provider-declared cost accounting, exact keys and key-size bound, monotonic
TTL only with injected test clock, and explicit same-cache reentry rejection.
The upstream aggregate cache is not LRU and has no capacity bound. Always/latch
cache criteria, runtime reflection, async fetching, negative caching and
concurrent use are outside this package.
