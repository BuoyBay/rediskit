# RedisKit provenance and scope

The source survey identified [XOJO-Redis](https://github.com/ktekinay/XOJO-Redis/).
Inspected commit: `a98c8894ff0832ea0cf116b8527dc53a344f1fda`.
Its MIT license (copyright 2018 Kem Tekinay) is retained in
[UPSTREAM-LICENSE.txt](UPSTREAM-LICENSE.txt).

Inspected files include README.md and Redis Module/M_Redis/Redis_MTC.xojo_code,
CommandSpec.xojo_code and RedisServer_MTC.xojo_code. The original has a broad set
of command-specific methods, AUTH support, pipeline mode, semaphore/concurrency
machinery and desktop/console harnesses. This implementation independently builds
a narrower codec and explicit generic-command connection; no upstream source or
assets are copied.

Wire behavior follows the primary [Redis RESP specification](https://redis.io/docs/latest/develop/reference/protocol-spec/):
RESP2 simple strings, errors, signed 64-bit integers, bulk strings, arrays and
separate null forms. RESP3 and push-mode behavior remain outside the contract.
Tests independently frame/parse commands in Python and drive TCP mock responses.

Buoy's installed bytes.md, tcpsocket.md, streamsocket.md, runloop.md,
microseconds.md and thread.md define the underlying APIs. TCPSocket already has
TLS primitives but its documented blocking encrypted I/O and lifecycle caveats
make an honestly bounded TLS client a separate task. RedisKit deliberately exposes
plain IPv4 only instead of claiming a verified timeout/security contract it does
not implement. A package build on an OS target is not a runtime networking test
on that platform.

Runtime findings: Integer.Parse accepted an out-of-Int64-range value during
boundary tests in this Buoy installation. The codec therefore validates and
accumulates decimal digits with explicit overflow checks. Bytes rather than
String also avoid the installation's embedded-NUL string limitations.
