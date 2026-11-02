# RedisKit study

[RedisKit](../../../../README.md) supplies a binary-safe RESP2
codec and explicit synchronous IPv4 command/pipeline connection. This example
prints a command frame and decodes an array offline; it makes no connection.
The source comments show an explicit local PING.

Install RedisKit and compile `main.bui` in a consumer project. Use a Redis endpoint configured for the transport supported by the package.
See the package README for the installed Linux socket-linker workaround, API
bounds and unimplemented TLS/RESP3/cluster/automatic-auth behavior.
