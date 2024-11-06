HPING3(8)

NAME
       hping3 - send (almost) arbitrary TCP/IP packets to network hosts


=======================================
  RUN
=======================================

The hping requires root access to run because it uses raw socket.

  $ ./hping3 -c 10 192.168.178.87
  [open_sockraw] socket(): Operation not permitted
  [main] can't open raw socket


---------------------------------------
  Mode 0: IP
---------------------------------------

  $ sudo ./hping3 -0 -c 10 192.168.178.87
  HPING 192.168.178.87 (wlp3s0 192.168.178.87): raw IP mode set, 20 headers + 0 data bytes

  --- 192.168.178.87 hping statistic ---
  10 packets tramitted, 0 packets received, 100% packet loss
  round-trip min/avg/max = 0.0/0.0/0.0 ms


---------------------------------------
  Mode 1: ICMP
---------------------------------------

  $ sudo ./hping3 -1 -c 10 192.168.178.87
  HPING 192.168.178.87 (wlp3s0 192.168.178.87): icmp mode set, 28 headers + 0 data bytes
  len=28 ip=192.168.178.87 ttl=64 id=43702 icmp_seq=0 rtt=203.9 ms
  len=28 ip=192.168.178.87 ttl=64 id=43774 icmp_seq=1 rtt=103.7 ms
  len=28 ip=192.168.178.87 ttl=64 id=43851 icmp_seq=2 rtt=223.7 ms
  len=28 ip=192.168.178.87 ttl=64 id=44019 icmp_seq=3 rtt=51.5 ms
  len=28 ip=192.168.178.87 ttl=64 id=44183 icmp_seq=4 rtt=75.3 ms
  len=28 ip=192.168.178.87 ttl=64 id=44317 icmp_seq=5 rtt=162.7 ms
  len=28 ip=192.168.178.87 ttl=64 id=44417 icmp_seq=6 rtt=193.6 ms
  len=28 ip=192.168.178.87 ttl=64 id=44606 icmp_seq=7 rtt=105.3 ms
  len=28 ip=192.168.178.87 ttl=64 id=44841 icmp_seq=8 rtt=236.1 ms
  len=28 ip=192.168.178.87 ttl=64 id=44856 icmp_seq=9 rtt=192.0 ms

  --- 192.168.178.87 hping statistic ---
  10 packets tramitted, 10 packets received, 0% packet loss
  round-trip min/avg/max = 51.5/154.8/236.1 ms


---------------------------------------
  Mode 2: UDP
---------------------------------------

  $ sudo ./hping3 -2 -c 10 192.168.178.87
  HPING 192.168.178.87 (wlp3s0 192.168.178.87): udp mode set, 28 headers + 0 data bytes
  ICMP Port Unreachable from ip=192.168.178.87 name=UNKNOWN   
  ICMP Port Unreachable from ip=192.168.178.87 name=UNKNOWN   
  ICMP Port Unreachable from ip=192.168.178.87 name=UNKNOWN   
  ICMP Port Unreachable from ip=192.168.178.87 name=UNKNOWN   
  ICMP Port Unreachable from ip=192.168.178.87 name=UNKNOWN   
  ICMP Port Unreachable from ip=192.168.178.87 name=UNKNOWN   
  ICMP Port Unreachable from ip=192.168.178.87 name=UNKNOWN   
  ICMP Port Unreachable from ip=192.168.178.87 name=UNKNOWN   
  ICMP Port Unreachable from ip=192.168.178.87 name=UNKNOWN   
  ICMP Port Unreachable from ip=192.168.178.87 name=UNKNOWN   

  --- 192.168.178.87 hping statistic ---
  10 packets tramitted, 10 packets received, 0% packet loss
  round-trip min/avg/max = 0.0/0.0/0.0 ms


