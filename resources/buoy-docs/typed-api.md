---
title: "resourcekit typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["ResourceCache", "ResourceClock", "ResourceProvider", "ResourceResult"]
dependencies: []
symbols: ["ResourceCache", "ResourceClock", "ResourceProvider", "ResourceResult"]
related: ["api.md", "getting-started.md", "index.md"]
---

# resourcekit typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## ResourceResult

`Public Class ResourceResult Of T`

A typed provider outcome. Missing is distinct from an empty or Nil value.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `IsFound` | `Boolean` | Yes | No | — | — |
| `Value` | `T` | Yes | No | — | — |
| `TTL` | `Integer` | Yes | No | — | — |
| `DeclaredBytes` | `Integer` | Yes | No | — | — |

### Constructor

`Public Sub Constructor()`

No parameters.

Returns: no value.

### Present

`Public Shared Function Present(value As T, ttlMicroseconds As Integer = 0, declaredBytes As Integer = 0) As ResourceResult Of T`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `value` | `T` | `ByVal` | Required |
| `ttlMicroseconds` | `Integer` | `ByVal` | `0` |
| `declaredBytes` | `Integer` | `ByVal` | `0` |

#### Returns

| Type |
| --- |
| `ResourceResult Of T` |

### Missing

`Public Shared Function Missing() As ResourceResult Of T`

No parameters.

#### Returns

| Type |
| --- |
| `ResourceResult Of T` |

## ResourceProvider

`Public Interface ResourceProvider Of T`

### Fetch

`Function Fetch(key As String) As ResourceResult Of T`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `key` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `ResourceResult Of T` |

## ResourceClock

`Public Interface ResourceClock`

### NowMicroseconds

`Function NowMicroseconds() As Integer`

No parameters.

#### Returns

| Type |
| --- |
| `Integer` |

## ResourceCache

`Public Class ResourceCache Of T`

Single-threaded ordered provider chain with bounded LRU and monotonic TTLs.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Count` | `Integer` | Yes | No | — | — |
| `CachedBytes` | `Integer` | Yes | No | — | — |
| `Capacity` | `Integer` | Yes | No | — | — |
| `DeclaredByteLimit` | `Integer` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(providers As Array Of ResourceProvider Of T, maxEntries As Integer = 1000, maxDeclaredBytes As Integer = 67108864)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `providers` | `Array Of ResourceProvider Of T` | `ByVal` | Required |
| `maxEntries` | `Integer` | `ByVal` | `1000` |
| `maxDeclaredBytes` | `Integer` | `ByVal` | `67108864` |

Returns: no value.

### Constructor (overload 2)

`Public Sub Constructor(providers As Array Of ResourceProvider Of T, maxEntries As Integer, maxDeclaredBytes As Integer, clock As ResourceClock)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `providers` | `Array Of ResourceProvider Of T` | `ByVal` | Required |
| `maxEntries` | `Integer` | `ByVal` | Required |
| `maxDeclaredBytes` | `Integer` | `ByVal` | Required |
| `clock` | `ResourceClock` | `ByVal` | Required |

Returns: no value.

### Fetch

`Public Function Fetch(key As String) As ResourceResult Of T`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `key` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `ResourceResult Of T` |

### Invalidate

`Public Function Invalidate(key As String) As Boolean`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `key` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Boolean` |

### Clear

`Public Sub Clear()`

No parameters.

Returns: no value.

### PruneExpired

`Public Function PruneExpired() As Integer`

No parameters.

#### Returns

| Type |
| --- |
| `Integer` |
