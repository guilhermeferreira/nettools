NMAP(1)

NAME
       nmap - Network exploration tool and security / port scanner

                    ___.-------.___
                _.-' ___.--;--.___ `-._
             .-' _.-'  /  .+.  \  `-._ `-.
           .' .-'      |-|-o-|-|      `-. `.
          (_ <O__      \  `+'  /      __O> _)
            `--._``-..__`._|_.'__..-''_.--'
                  ``--._________.--''
   ____  _____  ____    ____       _       _______
  |_   \|_   _||_   \  /   _|     / \     |_   __ \
    |   \ | |    |   \/   |      / _ \      | |__) |
    | |\ \| |    | |\  /| |     / ___ \     |  ___/
   _| |_\   |_  _| |_\/_| |_  _/ /   \ \_  _| |_
  |_____|\____||_____||_____||____| |____||_____|

  NMAP IS A POWERFUL TOOL -- USE CAREFULLY AND RESPONSIBLY

NMAP scan ports. It is usefull to check if the server is blocking a port.
We might check if the client can't connect with the server due the server
or firewall blocking the port the server is listening. 


=======================================
  RUN
=======================================

Push the files to the device:

$ adb push nmap/arm64-v8a/lib/libc++_shared.so /data/local/tmp <- Because /system/lib might not be in LD_LIBRARY_PATH
$ adb push nmap/arm64-v8a/lib/libz.so /data/local/tmp          <- Because /system/lib might have a 32-bit version
$ adb push nmap/arm64-v8a/bin/nmap /data/local/tmp
$ adb push nmap/arm64-v8a/etc/nmap-services /data/local/tmp
$ adb shell "mkdir /data/local/tmp/etc"
$ adb push nmap/arm64-v8a/etc/services /data/local/tmp/etc

Run nmap:

$ adb shell "LD_LIBRARY_PATH=/data/local/tmp/ /data/local/tmp/nmap 192.168.0.100 -p 3333"


---------------------------------------
  Commands
---------------------------------------

Scan a single IP                nmap 192.168.1.1
Scan a host                     nmap www.testhostname.com
Scan a range of IPs             nmap 192.168.1.1-20
Scan a subnet                   nmap 192.168.1.0/24
Scan targets from a text file   nmap -iL list-of-ips.txt

Scan a single Port              nmap -p 22 192.168.1.1
Scan a range of ports           nmap -p 1-100 192.168.1.1
Scan 100 most common ports      nmap -F 192.168.1.1
Scan all 65535 ports            nmap -p- 192.168.1.1

Scan using TCP connect          nmap -sT 192.168.1.1
Scan using TCP SYN (default)    nmap -sS 192.168.1.1
Scan UDP ports                  nmap -sU -p 123,161,162 192.168.1.1
Scan selected ports             nmap -Pn -F 192.168.1.1

Detect OS and Services          nmap -A 192.168.1.1
Standard service detection      nmap -sV 192.168.1.1
More aggressive serv. detection nmap -sV --version-intensity 5 192.168.1.1
Lighter banner grabbing detc.   nmap -sV --version-intensity 0 192.168.1.1

Save default output to file     nmap -oN outputfile.txt 192.168.1.1
Save results as XML             nmap -oX outputfile.xml 192.168.1.1
Save results in grep format     nmap -oG outputfile.txt 192.168.1.1
Save in all formats             nmap -oA outputfile 192.168.1.1

More @ https://hackertarget.com/nmap-cheatsheet-a-quick-reference-guide/


=======================================
  BUILD
=======================================

---------------------------------------
  arm64-v8a
---------------------------------------

$ git clone https://github.com/nmap/nmap.git
$ ./configure --prefix=${HOME}/nmap --host=aarch64-linux CC=${HOME}/Android/android-ndk/android-ndk-r20/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android21-clang CXX=${HOME}/Android/android-ndk/android-ndk-r20/toolchains/llvm/prebuilt/linux-x86_64/bin/aarch64-linux-android21-clang++ --enable-static --sysconfdir=/data/local/tmp
$ make
$ make install


---------------------------------------
  mips_24kc
---------------------------------------

$ export OPEN_WRT_HOME=${HOME}/openwrt-9ea28dd/
$ ./configure --prefix=${HOME}/nmap --host=mips CC=${OPEN_WRT_HOME}staging_dir/toolchain-mips_24kc_gcc-8.4.0_musl/bin/mips-openwrt-linux-musl-gcc CXX=${OPEN_WRT_HOME}staging_dir/toolchain-mips_24kc_gcc-8.4.0_musl/bin/mips-openwrt-linux-musl-g++ CFLAGS="-Os -pipe -mno-branch-likely -mips32r2 -mtune=24kc"
$ make
$ make install


