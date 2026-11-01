---
title: "rediskit typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["RedisCommand", "RedisConnection", "RespDecoder", "RespKind", "RespValue"]
dependencies: []
symbols: ["RedisCommand", "RedisConnection", "RespDecoder", "RespKind", "RespValue"]
related: ["api.md", "getting-started.md", "index.md"]
---

# rediskit typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## RespKind

`Public Enum RespKind As Integer`

### Values

| Name | Type | Value / Default | Description |
| --- | --- | --- | --- |
| `SimpleString` | `RespKind` | — | — |
| `ErrorReply` | `RespKind` | — | — |
| `IntegerReply` | `RespKind` | — | — |
| `BulkString` | `RespKind` | — | — |
| `ArrayReply` | `RespKind` | — | — |
| `NullBulk` | `RespKind` | — | — |
| `NullArray` | `RespKind` | — | — |

## RespValue

`Public Class RespValue`

Caller-owned decoded RESP value. Data preserves bytes; no implicit text decoding.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Kind` | `RespKind` | No | No | — | — |
| `Data` | `Bytes` | No | No | `New Bytes()` | — |
| `Number` | `Integer` | No | No | `0` | — |
| `Items` | `Array Of RespValue` | No | No | `[]` | — |

## RespDecoder

`Public Class RespDecoder`

Incremental RESP2 decoder; malformed input poisons it until Reset.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `ReadyCount` | `Integer` | Yes | No | — | — |
| `Failed` | `Boolean` | Yes | No | — | — |

### Constructor

`Public Sub Constructor(maxBytes As Integer = 1048576, maxNodes As Integer = 10000, maxDepth As Integer = 32, maxReady As Integer = 1000)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `maxBytes` | `Integer` | `ByVal` | `1048576` |
| `maxNodes` | `Integer` | `ByVal` | `10000` |
| `maxDepth` | `Integer` | `ByVal` | `32` |
| `maxReady` | `Integer` | `ByVal` | `1000` |

Returns: no value.

### Reset

`Public Sub Reset()`

No parameters.

Returns: no value.

### Feed

`Public Sub Feed(data As Bytes)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `data` | `Bytes` | `ByVal` | Required |

Returns: no value.

### Take

`Public Function Take() As RespValue`

No parameters.

#### Returns

| Type |
| --- |
| `RespValue` |

### Finish

`Public Sub Finish()`

No parameters.

Returns: no value.

## RedisKit functions

### EncodeRedisCommand

`Public Function EncodeRedisCommand(arguments As Array Of Bytes, maxBytes As Integer = 1048576) As Bytes`

Encode an array of binary bulk-string arguments. Arguments are never modified.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `arguments` | `Array Of Bytes` | `ByVal` | Required |
| `maxBytes` | `Integer` | `ByVal` | `1048576` |

#### Returns

| Type |
| --- |
| `Bytes` |

### RedisUTF8Arguments

`Public Function RedisUTF8Arguments(arguments As Array Of String) As Array Of Bytes`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `arguments` | `Array Of String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Array Of Bytes` |

## RedisCommand

`Public Class RedisCommand`

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Arguments` | `Array Of Bytes` | No | No | — | — |

### Constructor

`Public Sub Constructor(arguments As Array Of Bytes)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `arguments` | `Array Of Bytes` | `ByVal` | Required |

Returns: no value.

## RedisConnection

`Public Class RedisConnection`

Console-only synchronous RESP2 IPv4 connection, single caller, plain TCP.

### Constructor

`Public Sub Constructor(timeoutMs As Integer = 5000)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `timeoutMs` | `Integer` | `ByVal` | `5000` |

Returns: no value.

### ConnectIPv4

`Public Sub ConnectIPv4(address As String, port As Integer = 6379)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `address` | `String` | `ByVal` | Required |
| `port` | `Integer` | `ByVal` | `6379` |

Returns: no value.

### Close

`Public Sub Close()`

No parameters.

Returns: no value.

### Execute

`Public Function Execute(arguments As Array Of Bytes) As RespValue`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `arguments` | `Array Of Bytes` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `RespValue` |

### Pipeline

`Public Function Pipeline(commands As Array Of RedisCommand) As Array Of RespValue`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `commands` | `Array Of RedisCommand` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Array Of RespValue` |
