TCPDUMP(8)

NAME
       tcpdump - dump traffic on a network


=======================================
  RUN
=======================================

$ adb push tcpdump /data/local/tmp

$ adb shell "tcpdump -i any -U -w - 2>/dev/null" | wireshark -k -S -i -
$ adb shell "tcpdump -A tcp -" | wireshark -k -S -i -
$ adb shell "/data/local/tmp/tcpdump -A tcp > /data/local/tmp/tcpdump.pcap"

dreamlte:/data/local/tmp $ ./tcpdump.arm -h                                                                                         
tcpdump.arm version 4.9.3
libpcap version 1.9.1 (with TPACKET_V3)
Usage: tcpdump.arm [-aAbdDefhHIJKlLnNOpqStuUvxX#] [ -B size ] [ -c count ]
		[ -C file_size ] [ -E algo:secret ] [ -F file ] [ -G seconds ]
		[ -i interface ] [ -j tstamptype ] [ -M secret ] [ --number ]
		[ -Q in|out|inout ]
		[ -r file ] [ -s snaplen ] [ --time-stamp-precision precision ]
		[ --immediate-mode ] [ -T type ] [ --version ] [ -V file ]
		[ -w file ] [ -W filecount ] [ -y datalinktype ] [ -z postrotate-command ]
		[ -Z user ] [ expression ]

dreamlte:/data/local/tmp $ ./tcpdump.arm64 -h
tcpdump.arm64 version 4.9.3
libpcap version 1.8.1
Usage: tcpdump.arm64 [-aAbdDefhHIJKlLnNOpqStuUvxX#] [ -B size ] [ -c count ]
		[ -C file_size ] [ -E algo:secret ] [ -F file ] [ -G seconds ]
		[ -i interface ] [ -j tstamptype ] [ -M secret ] [ --number ]
		[ -Q in|out|inout ]
		[ -r file ] [ -s snaplen ] [ --time-stamp-precision precision ]
		[ --immediate-mode ] [ -T type ] [ --version ] [ -V file ]
		[ -w file ] [ -W filecount ] [ -y datalinktype ] [ -z postrotate-command ]
		[ -Z user ] [ expression ]


=======================================
  SECURITY
=======================================

The tcpdump requires two security bypass in order to run in the device, SELinux
and Linux capabilities.

$ tcpdump -i wlan0                                                                                       
tcpdump: wlan0: You don't have permission to capture on that device
(socket: Operation not permitted)


---------------------------------------
  SELinux
---------------------------------------

perm={ create read write getattr bind connect listen accept getopt setopt shutdown ioctl setattr lock } 
scontext=u:r:shell:s0
tcontext=u:r:shell:s0
tclass=socket

perm={ create read write getattr bind connect listen accept getopt setopt shutdown ioctl setattr lock } 
scontext=u:r:shell:s0
tcontext=u:r:shell:s0
tclass=packet_socket

perm={ create read write getattr bind connect listen accept getopt setopt shutdown ioctl setattr lock }
scontext=u:r:shell:s0
tcontext=u:r:shell:s0
tclass=netlink_netfilter_socket


---------------------------------------
  Capabilities
---------------------------------------

tcpdump creates a PF_PACKET socket. Only processes with effective UID 0 (root)
or the CAP_NET_RAW capability may open packet sockets.

In the kernel, the call stack is:

  #1. SYSCALL_DEFINE3(socket, int, family, int, type, int, protocol)
  #2. sock_create(int family, int type, int protocol, struct socket **res)
  #3. __sock_create(struct net *net, int family, int type, int protocol, struct socket **res, int kern)
  #4. packet_create(struct net *net, struct socket *sock, int protocol, int kern)
    {
    	...

    	if (!ns_capable(net->user_ns, CAP_NET_RAW)) <== this is not only SELinux, it requires root
    		return -EPERM;
    	...
    }

In order to allow tcpdump to work, remove this check from packet_create.


=======================================
  Interfaces
=======================================

On Galaxy S8 (SM-G950F):

    $ ./tcpdump/arm64-v8a/bin/wireshark -i wlan0  <== Wi-Fi

    $ ./tcpdump/arm64-v8a/bin/wireshark -i rmnet0 <= GPRS

    dreamlte:/ $ ip route
    10.0.0.0/24 dev rmnet0 proto kernel scope link src 10.0.0.4

    dreamlte:/ $ ip link
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: umts_dm0: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 

    3: rmnet0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc mq state UNKNOWN mode DEFAULT group default qlen 1000 <== GPRS On
        link/ppp
    3: rmnet0: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000              <== GPRS Off
        link/ppp 

    4: rmnet1: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 
    5: rmnet2: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 
    6: rmnet3: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 
    7: rmnet4: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 
    8: rmnet5: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 
    9: rmnet6: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 
    10: rmnet7: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 
    11: sit0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN mode DEFAULT group default qlen 1
        link/sit 0.0.0.0 brd 0.0.0.0
    12: ip6tnl0@NONE: <NOARP> mtu 1452 qdisc noop state DOWN mode DEFAULT group default qlen 1
        link/tunnel6 :: brd ::
    13: p2p0: <NO-CARRIER,BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state DORMANT mode DEFAULT group default qlen 1000
        link/ether 46:91:60:ab:9c:8d brd ff:ff:ff:ff:ff:ff

    14: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DORMANT group default qlen 1000 <== Wi-Fi On
        link/ether 44:91:60:ab:9c:8d brd ff:ff:ff:ff:ff:ff
    14: wlan0: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast state DOWN mode DEFAULT group default qlen 1000           <== Wi-Fi Off
        link/ether 44:91:60:ab:9c:8d brd ff:ff:ff:ff:ff:ff

    15: swlan0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ether 02:90:4c:65:c4:c0 brd ff:ff:ff:ff:ff:ff
    16: epdg0: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
        link/none 
    17: epdg1: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
        link/none 
    18: epdg2: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
        link/none 
    19: epdg3: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
        link/none 
    20: epdg4: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
        link/none 

On Galaxy A30 (SM-A305F).

    $ ./tcpdump/arm64-v8a/bin/wireshark -i wlan0  <== Wi-Fi

    $ ./tcpdump/arm64-v8a/bin/wireshark -i rmnet4 <= GPRS

    a30:/ $ ip route
    10.0.0.0/24 dev rmnet4 proto kernel scope link src 10.0.0.2

    a30:/ $ ip link
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: umts_dm0: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 
    3: rmnet0: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 
    4: rmnet1: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 
    5: rmnet2: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 
    6: rmnet3: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 

    7: rmnet4: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc mq state UNKNOWN mode DEFAULT group default qlen 1000 <== GPRS On
        link/ppp
    7: rmnet4: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000              <== GPRS Off
        link/ppp 

    8: rmnet5: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 
    9: rmnet6: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 
    10: rmnet7: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ppp 

    11: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DORMANT group default qlen 1000 <== Wi-Fi On
        link/ether c6:db:b9:35:ee:8a brd ff:ff:ff:ff:ff:ff
    11: wlan0: <BROADCAST,MULTICAST> mtu 1500 qdisc mq state DOWN mode DEFAULT group default qlen 1000           <== Wi-Fi Off
        link/ether c6:db:b9:35:ee:8a brd ff:ff:ff:ff:ff:ff

    12: p2p0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN mode DORMANT group default qlen 1000
        link/ether 3e:20:f6:2b:47:4a brd ff:ff:ff:ff:ff:ff
    13: swlan0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
        link/ether 00:00:00:00:00:00 brd ff:ff:ff:ff:ff:ff
    14: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN mode DEFAULT group default qlen 1
        link/ipip 0.0.0.0 brd 0.0.0.0
    15: ip6_vti0@NONE: <NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1
        link/tunnel6 :: brd ::
    16: sit0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN mode DEFAULT group default qlen 1
        link/sit 0.0.0.0 brd 0.0.0.0
    17: ip6tnl0@NONE: <NOARP> mtu 1452 qdisc noop state DOWN mode DEFAULT group default qlen 1
        link/tunnel6 :: brd ::
    18: epdg0: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
        link/none 
    19: epdg1: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
        link/none 
    20: epdg2: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
        link/none 
    21: epdg3: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
        link/none 
    22: epdg4: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
        link/none 
    23: epdg5: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
        link/none 
    24: epdg6: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
        link/none 
    25: epdg7: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
        link/none


=======================================
  BUILD
=======================================

Build first libpcap. Then, build tcpdump with static linked libpcap.a.


---------------------------------------
  arm64-v8a - libpcap
---------------------------------------

$ git clone https://android.googlesource.com/platform/external/libpcap
$ cd libpcap
$ git checkout -t origin/pie-dev
$ ./configure --prefix=${HOME}/libpcap --host=aarch64-linux --with-pcap=linux CC=${HOME}/Android/android-ndk/android-ndk-r20/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android21-clang --with-pcap=linux
$ make
$ make install


---------------------------------------
  arm64-v8a - tcpdump
---------------------------------------

$ git clone https://github.com/the-tcpdump-group/tcpdump.git
$ cd tcpdump/
$ git checkout -t origin/tcpdump-4.9
$ ./configure --prefix=${HOME}/tcpdump --host=aarch64-linux CC=${HOME}/Android/android-ndk/android-ndk-r20/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android21-clang --enable-static --enable-pie
$ make
$ make install


---------------------------------------
  mips_24kc - libpcap
---------------------------------------

$ export OPEN_WRT_HOME=${HOME}/openwrt-9ea28dd/
$ cd libpcap
$ ./configure --prefix=${HOME}/libpcap --host=mips CC=${OPEN_WRT_HOME}staging_dir/toolchain-mips_24kc_gcc-8.4.0_musl/bin/mips-openwrt-linux-musl-gcc --with-pcap=linux CFLAGS="-Os -pipe -mno-branch-likely -mips32r2 -mtune=24kc"
$ make
$ make install
    

---------------------------------------
  mips_24kc - tcpdump
---------------------------------------

$ cd tcpdump
$ ./configure --prefix=${HOME}/tcpdump --host=mips CC=${OPEN_WRT_HOME}staging_dir/toolchain-mips_24kc_gcc-8.4.0_musl/bin/mips-openwrt-linux-musl-gcc CFLAGS="-Os -pipe -mno-branch-likely -mips32r2 -mtune=24kc"
$ make
$ make install


