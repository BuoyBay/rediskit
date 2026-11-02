# RESP2 codec and synchronous Redis pipelines

A bounded incremental RESP2 codec and small synchronous IPv4 Redis connection.
`Import rediskit`. The codec algorithm is source-only; `RedisConnection` uses
the existing TCPSocket and console RunLoop APIs. The compiler may link socket
dependencies even for codec-only consumers of this package. There is no new
socket bridge.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add rediskit --git https://github.com/BuoyBay/rediskit.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## Example

```basic
Dim client As New RedisConnection(2000)
client.ConnectIPv4("127.0.0.1", 6379)
Dim reply As RespValue = client.Execute(RedisUTF8Arguments(["PING"]))
client.Close()
```

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer rediskit
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
