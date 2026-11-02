# Ordered resource lookup with cache and expiry

`Import resourcekit` adds typed ordered resource providers and a bounded LRU
cache. This package is synchronous and **single-threaded**. It neither fetches
network resources itself nor selects an HTTP caching policy.

Implement `ResourceProvider Of T.Fetch(key As String) As ResourceResult Of T`.
Return `ResourceResult Of T.Present(value, ttlMicroseconds = 0, declaredBytes = 0)`
or `.Missing()`. `IsFound` distinguishes a missing result from a valid empty or
Nil payload. Reading `Value` on Missing throws `InvalidArgumentException`.
Results have read-only `TTL` and `DeclaredBytes`; mutable payloads retain normal
reference identity and are not deep copied. Negative TTL/cost throws.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add resourcekit --git https://github.com/BuoyBay/resourcekit.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## API entrypoints

- `Import resourcekit`
- `ResourceProvider Of T.Fetch(key As String) As ResourceResult Of T`
- `ResourceResult Of T.Present(value, ttlMicroseconds = 0, declaredBytes = 0)`
- `.Missing()`
- `IsFound`

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer resourcekit
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```

## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
