# RedisKit study

[RedisKit](../../../../libraries/rediskit/README.md) supplies a binary-safe RESP2
codec and explicit synchronous IPv4 command/pipeline connection. This example
prints a command frame and decodes an array offline; it makes no connection.
The source comments show an explicit local PING.

Run `python3 tests/impact_redis_verify.py` from the repository root. The runner
builds in isolation, checks all six targets and runs codec/local TCP mock tests.
See the package README for the installed Linux socket-linker workaround, API
bounds and unimplemented TLS/RESP3/cluster/automatic-auth behavior.
