# iftop

[`iftop`](https://pdw.ex-parrot.com/iftop/) does for network usage what top(1) does for CPU usage. It listens to network traffic on a named interface and displays a table of current bandwidth usage by pairs of hosts.

## AArch64

```
$ wget https://pdw.ex-parrot.com/iftop/download/iftop-1.0pre4.tar.gz
$ tar xzf iftop-1.0pre4.tar.gz
$ cd iftop-1.0pre4/
$ ./configure --host=aarch64 --with-libpcap=/tmp/libpcap/build/usr/local CFLAGS="-static" LIBS="/tmp/libpcap/build/libpcap.a /tmp/ncurses-6.4/lib/libncurses.a"
$ make
```
