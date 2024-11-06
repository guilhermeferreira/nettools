IPERF(1)

NAME
       iperf3 - perform network throughput tests


=======================================
  RUN
=======================================

The iperf3 requires a server and a client to run. It measures the network speed
between them.

iperf3 client and server establishe a connection and exchange TCP segments.


---------------------------------------
  Ubuntu 18.04
---------------------------------------

Run the server on the host machine.

  $ ./iperf3/x86_64/bin/iperf3 -s
  -----------------------------------------------------------
  Server listening on 5201
  -----------------------------------------------------------
  Accepted connection from 192.168.178.87, port 52840
  [  5] local 192.168.178.172 port 5201 connected to 192.168.178.87 port 52842
  [ ID] Interval           Transfer     Bitrate
  [  5]   0.00-1.00   sec  1.77 MBytes  14.9 Mbits/sec                  
  [  5]   1.00-2.00   sec   249 KBytes  2.04 Mbits/sec                  
  [  5]   2.00-3.00   sec  0.00 Bytes  0.00 bits/sec                  
  [  5]   3.00-4.00   sec  0.00 Bytes  0.00 bits/sec                  
  [  5]   4.00-5.00   sec  0.00 Bytes  0.00 bits/sec                  
  [  5]   5.00-6.00   sec  0.00 Bytes  0.00 bits/sec                  
  [  5]   6.00-7.00   sec  0.00 Bytes  0.00 bits/sec                  
  [  5]   7.00-8.00   sec  0.00 Bytes  0.00 bits/sec                  
  [  5]   8.00-9.00   sec  0.00 Bytes  0.00 bits/sec                  
  [  5]   9.00-10.00  sec  0.00 Bytes  0.00 bits/sec                  
  [  5]  10.00-10.16  sec  0.00 Bytes  0.00 bits/sec                  
  - - - - - - - - - - - - - - - - - - - - - - - - -
  [ ID] Interval           Transfer     Bitrate
  [  5]   0.00-10.16  sec  2.01 MBytes  1.66 Mbits/sec                  receiver


---------------------------------------
  Android 10
---------------------------------------

Run the client on Android target device.

  $ adb push iperf3/arm64-v8a/bin/iperf3 /data/local/tmp
  $ adb shell
  a50: $ /data/local/tmp/iperf3 -c 192.168.178.172
  Connecting to host 192.168.178.172, port 5201
  [  5] local 192.168.178.87 port 52842 connected to 192.168.178.172 port 5201
  [ ID] Interval           Transfer     Bitrate         Retr  Cwnd
  [  5]   0.00-1.00   sec  2.70 MBytes  22.6 Mbits/sec    0    216 KBytes       
  [  5]   1.00-2.00   sec   509 KBytes  4.17 Mbits/sec    1   1.41 KBytes       
  [  5]   2.00-3.00   sec  0.00 Bytes  0.00 bits/sec    1   1.41 KBytes       
  [  5]   3.00-4.00   sec  0.00 Bytes  0.00 bits/sec    1   1.41 KBytes       
  [  5]   4.00-5.00   sec  0.00 Bytes  0.00 bits/sec    0   1.41 KBytes       
  [  5]   5.00-6.00   sec  0.00 Bytes  0.00 bits/sec    1   1.41 KBytes       
  [  5]   6.00-7.00   sec  0.00 Bytes  0.00 bits/sec    0   1.41 KBytes       
  [  5]   7.00-8.00   sec  0.00 Bytes  0.00 bits/sec    0   1.41 KBytes       
  [  5]   8.00-9.00   sec  0.00 Bytes  0.00 bits/sec    0   1.41 KBytes       
  [  5]   9.00-10.00  sec  0.00 Bytes  0.00 bits/sec    0   1.41 KBytes       
  - - - - - - - - - - - - - - - - - - - - - - - - -
  [ ID] Interval           Transfer     Bitrate         Retr
  [  5]   0.00-10.00  sec  3.19 MBytes  2.68 Mbits/sec    4             sender
  [  5]   0.00-10.16  sec  2.01 MBytes  1.66 Mbits/sec                  receiver

  iperf Done.


=======================================
  BUILD
=======================================

How to build.


---------------------------------------
  Ubuntu 18.04
---------------------------------------

  $ git checkout master
  $ ./configure --prefix=~/iperf --enable-static-bin
  $ make
  $ make install


---------------------------------------
  Android 10
---------------------------------------

  $ git checkout android
  $ ndk-build


