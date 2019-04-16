Problem 1
===============================

Troubleshooting steps:

1. Connected to server. 
2. Checked network config: 

	[ec2-user@ip-10-0-0-161 ~]$ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 9001
        inet 10.0.0.161  netmask 255.255.255.0  broadcast 10.0.0.255
        inet6 fe80::25:68ff:fe4b:1fa  prefixlen 64  scopeid 0x20<link>
        ether 02:25:68:4b:01:fa  txqueuelen 1000  (Ethernet)
        RX packets 48937  bytes 48198249 (45.9 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 20752  bytes 2871329 (2.7 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 8  bytes 648 (648.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 8  bytes 648 (648.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

3. Ping Gateway: (successful)

[ec2-user@ip-10-0-0-161 ~]$ ping 10.0.0.1
PING 10.0.0.1 (10.0.0.1) 56(84) bytes of data.
64 bytes from 10.0.0.1: icmp_seq=1 ttl=255 time=0.286 ms
64 bytes from 10.0.0.1: icmp_seq=2 ttl=255 time=0.273 ms
64 bytes from 10.0.0.1: icmp_seq=3 ttl=255 time=0.318 ms
64 bytes from 10.0.0.1: icmp_seq=4 ttl=255 time=0.387 ms
64 bytes from 10.0.0.1: icmp_seq=5 ttl=255 time=0.251 ms
64 bytes from 10.0.0.1: icmp_seq=6 ttl=255 time=0.309 ms
^C
--- 10.0.0.1 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5121ms
rtt min/avg/max/mdev = 0.251/0.304/0.387/0.043 ms


4. Ping internet: (successful)

[ec2-user@ip-10-0-0-161 ~]$ ping google.com
PING google.com (172.217.4.110) 56(84) bytes of data.
64 bytes from ord36s04-in-f14.1e100.net (172.217.4.110): icmp_seq=1 ttl=44 time=19.2 ms
64 bytes from ord36s04-in-f14.1e100.net (172.217.4.110): icmp_seq=2 ttl=44 time=19.3 ms
64 bytes from ord36s04-in-f14.1e100.net (172.217.4.110): icmp_seq=3 ttl=44 time=19.3 ms
64 bytes from ord36s04-in-f14.1e100.net (172.217.4.110): icmp_seq=4 ttl=44 time=19.3 ms
64 bytes from ord36s04-in-f14.1e100.net (172.217.4.110): icmp_seq=5 ttl=44 time=19.3 ms
^C
--- google.com ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4006ms
rtt min/avg/max/mdev = 19.282/19.334/19.361/0.178 ms


5. Look at arp table: (two other IPs ARPing on broadcast domain and the gateway and gateway is arping with an autoIP)

[ec2-user@ip-10-0-0-161 ~]$ arp -a
? (10.0.0.191) at 02:7e:d2:78:b8:16 [ether] on eth0
? (169.254.169.254) at 02:a1:e6:5e:fc:8e [ether] on eth0
? (10.0.0.140) at 02:06:dc:29:68:88 [ether] on eth0
gateway (10.0.0.1) at 02:a1:e6:5e:fc:8e [ether] on eth0

6. Look at DNS (successful but an error on the primary server)

[ec2-user@ip-10-0-0-161 ~]$ nslookup google.com
;; Got recursion not available from 10.0.0.191, trying next server
Server:         10.0.0.140
Address:        10.0.0.140#53

Non-authoritative answer:
Name:   google.com
Address: 172.217.4.206


7. Ping primary DNS server at 10.0.0.191 (failed)

[ec2-user@ip-10-0-0-161 ~]$ ping 10.0.0.191
PING 10.0.0.191 (10.0.0.191) 56(84) bytes of data.
^C
--- 10.0.0.191 ping statistics ---
26 packets transmitted, 0 received, 100% packet loss, time 25608ms




CONCLUSION: Based on my troublshooting it looks like something is wrong with the primary DNS server (10.0.0.191) in this envirmoent,
but I do question why the clients aren't just using the secondary server as it was configured properly on the client I tested on. 


