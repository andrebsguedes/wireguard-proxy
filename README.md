# wireguard-proxy

[![Travis-CI Build Status](https://api.travis-ci.org/moparisthebest/wireguard-proxy.svg?branch=master)](https://travis-ci.org/moparisthebest/wireguard-proxy)
[![Build status](https://ci.appveyor.com/api/projects/status/vl8c9xdhvgn997d2/branch/master?svg=true)](https://ci.appveyor.com/project/moparisthebest/wireguard-proxy/branch/master)
[![crates.io](https://img.shields.io/crates/v/wireguard-proxy.svg)](https://crates.io/crates/wireguard-proxy)

Proxy wireguard UDP packets over TCP/TLS

`wireguard-proxy` has 2 modes:
- server-side daemon to accept TCP/TLS connections from multiple clients and pipe data to and from the specified UDP port
- client-side daemon that accepts UDP packets on a local port from a single client, connects to a single remote TCP/TLS port, and pipes data between them

```
$ wireguard-proxy -h
usage: wireguard-proxy [options...]
 Client Mode (requires --tcp-target):
 -tt, --tcp-target <ip:port>     TCP target to send packets to, where
                                 wireguard-proxy server is running
 -uh, --udp-host <ip:port>       UDP host to listen on, point wireguard
                                 client here, default: 127.0.0.1:51820
 --tls                           use TLS when connecting to tcp-target
                                 WARNING: currently verifies nothing!

 Server Mode (requires --tcp-host):
 -th, --tcp-host <ip:port>                TCP host to listen on
 -ut, --udp-target <ip:port>              UDP target to send packets to, where
                                          wireguard server is running,
                                          default: 127.0.0.1:51820
 -ur, --udp-bind-host-range <ip:low-high> UDP host and port range to bind to,
                                          one port per TCP connection, to
                                          listen on for UDP packets to send
                                          back over the TCP connection,
                                          default: 127.0.0.1:30000-40000
 -tk, --tls-key <ip:port>                 TLS key to listen with,
                                          requires --tls-cert also
 -tc, --tls-cert <ip:port>                TLS cert to listen with,
                                          requires --tls-key also

 Common Options:
 -h, --help                      print this usage text
 -st, --socket-timeout <seconds> Socket timeout (time to wait for data)
                                 before terminating, default: 0
```

Binaries:

- [releases](https://github.com/moparisthebest/wireguard-proxy/releases) has static builds for most platforms performed by travis-ci and appveyor courtesy of [trust](https://github.com/japaric/trust)
- Arch Linux AUR [wireguard-proxy](https://aur.archlinux.org/packages/wireguard-proxy/) and [wireguard-proxy-git](https://aur.archlinux.org/packages/wireguard-proxy-git/)

Building:

- `cargo build --release` - minimal build without TLS support, no dependencies
- `cargo build --release --feature tls` - links to system openssl
- `cargo build --release --feature openssl_vendored` - compiles vendored openssl and link to it

Testing:

- `udp-test` is a utility to send a UDP packet and then receive a UDP packet and ensure they are the same, this verifies packets sent through proxy server/client are unmolested  
- `udp-test -s` runs udp-test against itself through proxy server/client by spawning actual binaries
- `udp-test -is` runs udp-test against itself through proxy server/client in same executable by using library, so does not test command line parsing etc
- `test.sh` runs udp-test against itself, the udp-test self tests above, and through proxy server/client in the shell script

Testing with GNU netcat:

- `nc -vulp 51820` listen on udp like wireguard would
- `nc -u -p 51821 127.0.0.1 51820` connect directly to local udp wireguard port to send data to 51820 from port 51821
- `nc -vlp 5555` listen on tcp like wireguard-proxy would
- `nc 127.0.0.1 5555` connect directly to local tcp wireguard-proxy port to send/recieve data
- so to test through wireguard-proxy run first and last command while it's running, type in both places

# License

This project is licensed under either of

 * Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or
   http://www.apache.org/licenses/LICENSE-2.0)
 * MIT license ([LICENSE-MIT](LICENSE-MIT) or
   http://opensource.org/licenses/MIT)

at your option.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in die by you, as defined in the Apache-2.0 license, shall be
dual licensed as above, without any additional terms or conditions.
