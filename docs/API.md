# RedisKit 0.1.0

A bounded incremental RESP2 codec and small synchronous IPv4 Redis connection.
`Import rediskit`. The codec algorithm is pure Buoy; `RedisConnection` uses
Buoy's existing TCPSocket and console RunLoop APIs. The compiler may link socket
dependencies even for codec-only consumers of this package. There is no new
socket bridge.

## Codec

- `RespDecoder(maxBytes=1048576,maxNodes=10000,maxDepth=32,maxReady=1000)`:
  `Feed(Bytes)`, `ReadyCount`, `Take()`, `Finish()`, `Failed`, `Reset()`.
- `RespValue` is caller-owned mutable data: `Kind As RespKind`, `Data As Bytes`,
  `Number As Integer`, `Items As Array Of RespValue`.
- `RespKind`: `SimpleString`, `ErrorReply`, `IntegerReply`, `BulkString`,
  `ArrayReply`, `NullBulk`, `NullArray`. Empty and null values remain distinct.
- `EncodeRedisCommand(Array Of Bytes,maxBytes=1048576)` emits an array of bulk
  strings. `RedisUTF8Arguments(Array Of String)` is an explicit text convenience.

Feed may end anywhere, including between CR/LF or within binary payloads. Take
returns Nil until a whole top-level value is ready; completed values preserve
wire order. Finish rejects truncated input and allows already queued complete
values to remain available after a clean finish. Malformed input or exceeded
wire/resource limits poison the decoder and discard queued results; Reset is
required before reuse. Do not treat results from an earlier Feed as a transaction
that will be rolled back by a later protocol error.

All string payloads remain Bytes. Binary NUL, invalid UTF-8 and embedded CRLF are
preserved; the caller chooses whether and how to decode text. Signed integer
parsing checks all Int64 boundaries explicitly. Numeric headers accept the RESP2
integer optional sign, but lengths accept only decimal digits or the exact null
sentinel `-1`. RESP3 prefixes and inline commands reject.

The byte limit bounds an individual Feed plus buffered unconsumed input, each
complete wire message (including all nested data), and queued messages' combined
wire sizes. A chunk containing many otherwise valid messages can still exceed
the Feed limit: split it and drain Take. Node count is per top-level message;
maxDepth counts open nonempty arrays; maxReady counts queued top-level messages.
Decoded object overhead and transient copies add to wire sizes. Constructor
ceilings are 64 MiB, 1,000,000 nodes, depth 128 and 100,000 queued messages; defaults
are substantially smaller. Input is copied, compacted after Feed, and parsing
uses an explicit stack without reparsing completed nested prefixes. Mutable
results are no longer owned by the decoder once returned.

## Connection

```buoy
Dim client As New RedisConnection(2000)
client.ConnectIPv4("127.0.0.1", 6379)
Dim reply As RespValue = client.Execute(RedisUTF8Arguments(["PING"]))
client.Close()
```

`RedisCommand(arguments As Array Of Bytes)` snapshots the array, not its Bytes
contents. `Pipeline(Array Of RedisCommand)` encodes every command before I/O and
returns ordered replies. Commands and argument buffers must not be concurrently
mutated. At most 1,000 commands and 1 MiB encoded bytes per pipeline are accepted;
replies use default decoder limits. Redis error replies are returned as
`ErrorReply` values, including within pipelines, rather than discarding ordered
results or raising a command-specific exception.

`RedisConnection(timeoutMs=5000)` bounds the plain TCP connect/write through the
socket timeout and the reply wait with a monotonic deadline. Protocol, socket,
truncation and reply-timeout failures close the connection so a later command
cannot consume an old response. Close is idempotent. Construction performs no
network work; ConnectIPv4 is explicit. One owner/caller only; nested pipelines
reject. Pipeline pumps the main console RunLoop and may dispatch unrelated
callbacks: do not call it from an event handler or use it in a desktop app.
Other threads should not access the connection concurrently.

This is **plain TCP and literal IPv4 only**. TLS, DNS, automatic authentication,
reconnect/retry, cluster/Sentinel, transactions, Pub/Sub, RESP3 and command-specific
wrappers are not supplied. Credentials are not stored or discovered. Commands
that change to push/RESP3 modes (`SUBSCRIBE`, `PSUBSCRIBE`, `SSUBSCRIBE`, `MONITOR`,
`HELLO`) reject; caller-supplied commands must preserve ordinary request/reply
semantics. Commands such as `CLIENT REPLY` can break that contract and time out.
A reconnect after any failed operation is explicit; an operation may have reached
the server even if its reply was lost.

## Build and verification

The installed Buoy 0.72.0 Linux compiler omits the TLS archive needed by TCPSocket,
even for plain sockets. The runner follows this repository's existing Xippets
server builder: use the host native stubs directory and add
`/usr/lib/buoy/libbuoy_tls.a`, `libssl.a`, `libcrypto.a` and the native
`libc_nonshared.a` via `--extra-obj`. This is a compiler distribution workaround,
not a claim that RedisKit supplies or verifies TLS. `BUOY` and `BPM_PACKAGER`
select the compiler and packager; default packager is `/usr/bin/buoy`.

[Provenance and parity](../PROVENANCE.md) describe the inspected upstream.
