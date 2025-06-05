# Wireguard Dynamic IP Configuration Tool

This is an implementation of [the wg-dynamic description](./docs/idea.md).

Work-in-progress; nothing to see here yet.
# Install compilation Dependencies
``` compilation Error:
wg-dynamic$ make
  CC      wg-dynamic-server.o
wg-dynamic-server.c:22:10: fatal error: libmnl/libmnl.h: No such file or directory
   22 | #include <libmnl/libmnl.h>
      |          ^~~~~~~~~~~~~~~~~
compilation terminated.
make: *** [<builtin>: wg-dynamic-server.o] Error 1
```
#### Dependency Install - Ubuntu
``` commandline
sudo apt install  libmnl-dev -y && make
```
# Testing
```
sudo ./tests/netsh.sh !
[+] ip netns add wg-test-1560-0
[+] ip netns add wg-test-1560-1
[+] ip netns add wg-test-1560-2
[+] NS0: ip link set up dev lo
[+] NS0: ip link add dev wg0 type wireguard
[+] NS0: ip link set wg0 netns wg-test-1560-1
[+] NS0: ip link add dev wg0 type wireguard
[+] NS0: ip link set wg0 netns wg-test-1560-2
[+] NS1: ip addr add fe80::/64 dev wg0
[+] NS2: ip addr add fe80::badc:0ffe:e0dd:f00d/128 dev wg0
[+] NS1: wg set wg0 private-key /dev/fd/63 listen-port 1 peer 3OGTRdgbVCCErSOlRoGgwqnwLydC/gFGZmgzpGW9aSQ= allowed-ips fe80::badc:0ffe:e0dd:f00d/128
[+] NS2: wg set wg0 private-key /dev/fd/63 listen-port 2 peer 6LfsXu07/GwQ2GEe0/bjFuQ8ywokGtgXve5QFdgjIiY= allowed-ips 0.0.0.0/0,::/0
[+] NS1: ip link set up dev wg0
[+] NS2: ip link set up dev wg0
[+] NS2: ip route add fe80::/128 dev wg0
[+] NS1: ip route add 192.168.4.0/28 dev wg0
[+] NS1: ip route add 192.168.73.0/27 dev wg0
[+] NS1: ip route add 2001:db8:1234::/124 dev wg0
[+] NS1: ip route add 2001:db8:7777::/124 dev wg0
[+] NS1: wg set wg0 peer 3OGTRdgbVCCErSOlRoGgwqnwLydC/gFGZmgzpGW9aSQ= endpoint [::1]:2
[+] NS2: wg set wg0 peer 6LfsXu07/GwQ2GEe0/bjFuQ8ywokGtgXve5QFdgjIiY= endpoint [::1]:1
[+] NS2: ping6 -c 10 -f -W 1 fe80::%wg0
PING fe80::%wg0 (fe80::%wg0) 56 data bytes

--- fe80::%wg0 ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 11ms
rtt min/avg/max/mdev = 0.385/0.862/2.541/0.604 ms, ipg/ewma 1.179/1.216 ms
[+] NS1: ping6 -c 10 -f -W 1 fe80::badc:0ffe:e0dd:f00d%wg0
PING fe80::badc:0ffe:e0dd:f00d%wg0 (fe80::badc:ffe:e0dd:f00d%wg0) 56 data bytes

--- fe80::badc:0ffe:e0dd:f00d%wg0 ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 8ms
rtt min/avg/max/mdev = 0.321/0.646/1.133/0.227 ms, ipg/ewma 0.930/0.732 ms
[+] NS1: ./wg-dynamic-server --leasetime 10 wg0
(ipm.c:111): index=2, family=10, addr=fe80::
(wg-dynamic-server.c:71): - peer 3OGTRdgbVCCErSOlRoGgwqnwLydC/gFGZmgzpGW9aSQ=
(wg-dynamic-server.c:72):   allowedips:
(wg-dynamic-server.c:78):     fe80::badc:ffe:e0dd:f00d
^C[+] NS0: ip link del dev wg0
[+] NS1: ip link del dev wg0
^C
```
