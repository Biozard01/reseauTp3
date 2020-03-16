# TP 3

## Préparation de l'environnement

### Point

- Carte NAT désactivée client1 / server1 / router

#### Méthode pour prouver

```bash
[arthur@client1 ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:8b:53:2f brd ff:ff:ff:ff:ff:ff
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:f0:c6:cf brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.11/24 brd 10.3.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fef0:c6cf/64 scope link
       valid_lft forever preferred_lft forever
[arthur@client1 ~]$
```

```bash
[arthur@server1 ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:22:4d:54 brd ff:ff:ff:ff:ff:ff
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:a9:d7:07 brd ff:ff:ff:ff:ff:ff
    inet 10.3.2.11/24 brd 10.3.2.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fea9:d707/64 scope link
       valid_lft forever preferred_lft forever
[arthur@server1 ~]$
```

```bash
[arthur@router ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:40:57:17 brd ff:ff:ff:ff:ff:ff
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:89:87:f3 brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.254/24 brd 10.3.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe89:87f3/64 scope link
       valid_lft forever preferred_lft forever
4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:48:c3:8b brd ff:ff:ff:ff:ff:ff
    inet 10.3.2.254/24 brd 10.3.2.255 scope global noprefixroute enp0s9
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe48:c38b/64 scope link
       valid_lft forever preferred_lft forever
[arthur@router ~]$
```

enp0s3 étant ma NAT on peut voir quelle est désactiver (pas de inet).

### Point

- Serveur SSH fonctionnel qui écoute sur le port 7777/tcp client1 / server1 / router

#### Méthode pour prouver

```bash
[arthur@client1 ~]$ ss -ltunp
Netid State      Recv-Q Send-Q        Local Address:Port                       Peer Address:Port
tcp   LISTEN     0      100               127.0.0.1:25                                    *:*
tcp   LISTEN     0      128                       *:7777                                  *:*
tcp   LISTEN     0      100                   [::1]:25                                 [::]:*
tcp   LISTEN     0      128                    [::]:7777                               [::]:*
[arthur@client1 ~]$
```

```bash
[arthur@server1 ~]$ ss -ltunp
Netid  State      Recv-Q Send-Q     Local Address:Port                    Peer Address:Port
tcp    LISTEN     0      100            127.0.0.1:25                                 *:*
tcp    LISTEN     0      128                    *:7777                               *:*
tcp    LISTEN     0      100                [::1]:25                              [::]:*
tcp    LISTEN     0      128                 [::]:7777                            [::]:*
[arthur@server1 ~]$
```

```bash
[arthur@router ~]$ ss -ltunp
Netid State      Recv-Q Send-Q        Local Address:Port                       Peer Address:Port
tcp   LISTEN     0      100               127.0.0.1:25                                    *:*
tcp   LISTEN     0      128                       *:7777                                  *:*
tcp   LISTEN     0      100                   [::1]:25                                 [::]:*
tcp   LISTEN     0      128                    [::]:7777                               [::]:*
[arthur@router ~]$
```

On voit bien que le serveur SSh écoute sur le port 7777.

### Point

- Pare-feu activé et configuré client1 / server1 / router

#### Méthode pour prouver

```bash
[arthur@client1 ~]$ sudo firewall-cmd --list-all
[sudo] password for arthur:
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8
  sources:
  services: dhcpv6-client ssh
  ports: 7777/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

[arthur@client1 ~]$
```

```bash
[arthur@server1 ~]$  sudo firewall-cmd --list-all
[sudo] password for arthur:
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8
  sources:
  services: dhcpv6-client ssh
  ports: 7777/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

[arthur@server1 ~]$
```

```bash
[arthur@router ~]$ sudo firewall-cmd --list-all
[sudo] password for arthur:
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s8 enp0s9
  sources:
  services: dhcpv6-client ssh
  ports: 7777/tcp
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:

[arthur@router ~]$
```

### Point

- Nom configuré client1 / server1 / router

#### Méthode pour prouver

```bash
[arthur@client1 ~]$ hostname --fqdn
client1.net1
[arthur@client1 ~]$
```

```bash
[arthur@server1 ~]$ hostname --fqdn
server1
[arthur@server1 ~]$
```

```bash
[arthur@router ~]$ hostname --fqdn
router
[arthur@router ~]$
```

### Point

- Fichiers /etc/hosts de toutes les machines configurés client1 / server1 / router

#### Méthode pour prouver

```bash
[arthur@client1 ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.3.1.11   client1
10.3.2.11   server1
10.3.1.254  router
10.3.2.254  router
[arthur@client1 ~]$
```

```bash
[arthur@server1 ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.3.1.11   client1
10.3.2.11   server1
10.3.1.254  router
10.3.2.254  router
[arthur@server1 ~]$
```

```bash
[arthur@router ~]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.3.1.11   client1
10.3.2.11   server1
10.3.1.254  router
10.3.2.254  router
[arthur@router ~]$
```

### Point

- Réseaux et adressage des machines configurés client1 / server1 / router

#### Méthode pour prouver

```bash
[arthur@client1 ~]$ ping router
PING router (10.3.1.254) 56(84) bytes of data.
64 bytes from router (10.3.1.254): icmp_seq=1 ttl=64 time=0.380 ms
64 bytes from router (10.3.1.254): icmp_seq=2 ttl=64 time=0.554 ms
64 bytes from router (10.3.1.254): icmp_seq=3 ttl=64 time=0.543 ms
^C
--- router ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2008ms
rtt min/avg/max/mdev = 0.380/0.492/0.554/0.081 ms
[arthur@client1 ~]$
```

```bash
[arthur@router ~]$ ping client1
PING client1 (10.3.1.11) 56(84) bytes of data.
64 bytes from client1 (10.3.1.11): icmp_seq=1 ttl=64 time=0.672 ms
64 bytes from client1 (10.3.1.11): icmp_seq=2 ttl=64 time=0.726 ms
64 bytes from client1 (10.3.1.11): icmp_seq=3 ttl=64 time=0.803 ms
64 bytes from client1 (10.3.1.11): icmp_seq=4 ttl=64 time=0.708 ms
^C
--- client1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3009ms
rtt min/avg/max/mdev = 0.672/0.727/0.803/0.051 ms
[arthur@router ~]$
```

```bash
[arthur@server1 ~]$ ping router
PING router (10.3.2.254) 56(84) bytes of data.
64 bytes from router (10.3.2.254): icmp_seq=1 ttl=64 time=0.810 ms
64 bytes from router (10.3.2.254): icmp_seq=2 ttl=64 time=0.864 ms
64 bytes from router (10.3.2.254): icmp_seq=3 ttl=64 time=0.764 ms
^C
--- router ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2011ms
rtt min/avg/max/mdev = 0.764/0.812/0.864/0.052 ms
[arthur@server1 ~]$
```

```bash
[arthur@router ~]$ ping server1
PING server1 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1 (10.3.2.11): icmp_seq=1 ttl=64 time=0.630 ms
64 bytes from server1 (10.3.2.11): icmp_seq=2 ttl=64 time=0.902 ms
64 bytes from server1 (10.3.2.11): icmp_seq=3 ttl=64 time=0.889 ms
64 bytes from server1 (10.3.2.11): icmp_seq=4 ttl=64 time=0.795 ms
^C
--- server1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3009ms
rtt min/avg/max/mdev = 0.630/0.804/0.902/0.108 ms
[arthur@router ~]$
```

## I. Mise en place du routage

#### 1. Configuration du routage sur router

```bash
[arthur@router ~]$ sudo sysctl net.ipv4.conf.all.forwarding
net.ipv4.conf.all.forwarding = 1
[arthur@router ~]$
```

#### 2. Ajouter les routes statiques

```bash
[arthur@client1 ~]$ ip r s
default via 10.3.1.254 dev enp0s8 proto static metric 101
10.3.1.0/24 dev enp0s8 proto kernel scope link src 10.3.1.11 metric 101
10.3.2.0/24 via 10.3.1.254 dev enp0s8 proto static metric 101
10.3.2.0/24 via 10.3.2.254 dev enp0s8 proto static metric 101
10.3.2.254 dev enp0s8 proto static scope link metric 101
[arthur@client1 ~]$
```

```bash
[arthur@server1 ~]$ ip r s
default via 10.3.2.254 dev enp0s8 proto static metric 101
10.3.1.0/24 via 10.3.2.254 dev enp0s8 proto static metric 101
10.3.1.0/24 via 10.3.1.254 dev enp0s8 proto static metric 101
10.3.1.254 dev enp0s8 proto static scope link metric 101
10.3.2.0/24 dev enp0s8 proto kernel scope link src 10.3.2.11 metric 101
[arthur@server1 ~]$
```

#### 3. Comprendre le routage

|                                         | MAC src           | MAC dst           | IP src     | IP dst    |
| --------------------------------------- | ----------------- | ----------------- | ---------- | --------- |
| Dans net1 (trame qui entre dans router) | 08:00:27:f0:c6:cf | 08:00:27:a9:d7:07 | 10.3.1.11  | 10.3.2.11 |
| Dans net2 (trame qui sort de router)    | 08:00:27:48:c3:8b | 08:00:27:a9:d7:07 | 10.3.2.254 | 10.3.2.11 |

### II. ARP

#### 1. Tables ARP

```bash
[arthur@client1 ~]$ ip neigh show
10.3.1.254 dev enp0s8 lladdr 08:00:27:89:87:f3 STALE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:14 DELAY
[arthur@client1 ~]$
```

On peut voir sur la première ligne que nous avons l'IP du router sur notre réseau, son interface et sa MAC.

Sur la deuxième ligne on voit la première IP disponible sur notre réseau, son interface et sa MAC.

```bash
[arthur@server1 ~]$ ip neigh show
10.3.2.254 dev enp0s8 lladdr 08:00:27:48:c3:8b STALE
10.3.2.1 dev enp0s8 lladdr 0a:00:27:00:00:19 DELAY
[arthur@server1 ~]$
```

On peut voir sur la première ligne que nous avons l'IP du router sur notre réseau, son interface et sa MAC.

Sur la deuxième ligne on voit la première IP disponible sur notre réseau, son interface et sa MAC.

```bash
[arthur@router ~]$ ip neigh show
10.3.1.11 dev enp0s8 lladdr 08:00:27:f0:c6:cf STALE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:14 DELAY
10.3.2.1 dev enp0s9 lladdr 0a:00:27:00:00:19 STALE
10.3.2.11 dev enp0s9 lladdr 08:00:27:a9:d7:07 STALE
[arthur@router ~]$
```

On peut voir sur la première ligne que nous avons l'IP de client1, son interface et sa MAC.

Sur la deuxième ligne on voit la première IP disponible sur le réseau de client1, son interface et sa MAC.

Sur la troisième ligne on voit la première IP disponible sur le réseau de server1, son interface et sa MAC.

Sur la quatrième ligne on voit l'IP de server1, son interface et sa MAC.

#### 2. Requêtes ARP

##### A. Table ARP 1

```bash
[arthur@client1 ~]$ sudo ip neigh flush all
[sudo] password for arthur:
[arthur@client1 ~]$ ping server1
PING server1 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1 (10.3.2.11): icmp_seq=1 ttl=63 time=2.39 ms
64 bytes from server1 (10.3.2.11): icmp_seq=2 ttl=63 time=1.35 ms
64 bytes from server1 (10.3.2.11): icmp_seq=3 ttl=63 time=1.34 ms
64 bytes from server1 (10.3.2.11): icmp_seq=4 ttl=63 time=1.39 ms
^C
--- server1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3007ms
rtt min/avg/max/mdev = 1.347/1.620/2.390/0.445 ms
[arthur@client1 ~]$ ip neigh show
10.3.1.254 dev enp0s8 lladdr 08:00:27:89:87:f3 REACHABLE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:14 REACHABLE
[arthur@client1 ~]$
```

```bash
[arthur@router ~]$ sudo ip neigh flush all
[sudo] password for arthur:
[arthur@router ~]$ ip neigh show
10.3.1.11 dev enp0s8 lladdr 08:00:27:f0:c6:cf REACHABLE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:14 DELAY
10.3.2.11 dev enp0s9 lladdr 08:00:27:a9:d7:07 REACHABLE
[arthur@router ~]$
```

##### B. Table ARP 2

```bash
[arthur@client1 ~]$  sudo ip neigh flush all
[sudo] password for arthur:
[arthur@client1 ~]$ ping server1
PING server1 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1 (10.3.2.11): icmp_seq=1 ttl=63 time=1.79 ms
64 bytes from server1 (10.3.2.11): icmp_seq=2 ttl=63 time=1.32 ms
^C
--- server1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 1.326/1.560/1.795/0.237 ms
[arthur@client1 ~]$ ip neigh show
10.3.1.254 dev enp0s8 lladdr 08:00:27:89:87:f3 REACHABLE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:14 REACHABLE
[arthur@client1 ~]$
```

```bash
[arthur@server1 ~]$  sudo ip neigh flush all
[sudo] password for arthur:
[arthur@server1 ~]$ ip neigh show
10.3.2.254 dev enp0s8 lladdr 08:00:27:48:c3:8b STALE
10.3.2.1 dev enp0s8 lladdr 0a:00:27:00:00:19 DELAY
[arthur@server1 ~]$
```

```bash
[arthur@router ~]$ sudo ip neigh flush all
[sudo] password for arthur:
[arthur@router ~]$ ip neigh show
10.3.1.11 dev enp0s8 lladdr 08:00:27:f0:c6:cf REACHABLE
10.3.1.1 dev enp0s8 lladdr 0a:00:27:00:00:14 DELAY
10.3.2.11 dev enp0s9 lladdr 08:00:27:a9:d7:07 REACHABLE
[arthur@router ~]$
```

##### TCPDUMP 1

```bash
[arthur@client1 ~]$ sudo ip neigh flush all
```

```bash
[arthur@router ~]$ sudo ip neigh flush all
```

```bash
[arthur@client1 ~]$ ping server1
PING server1 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1 (10.3.2.11): icmp_seq=1 ttl=63 time=1.10 ms
64 bytes from server1 (10.3.2.11): icmp_seq=2 ttl=63 time=0.620 ms
^C
--- server1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 0.620/0.863/1.107/0.245 ms
```

##### TCPDUMP 2

```bash
[arthur@router ~]\$ sudo ip neigh flush all
```

```bash
[arthur@client1 ~]\$ sudo ip neigh flush all
```

```bash
[arthur@server1 ~]\$ sudo ip neigh flush all
```

```bash
[arthur@client1 ~]\$ ping server1
PING server1 (10.3.2.11) 56(84) bytes of data.
64 bytes from server1 (10.3.2.11): icmp_seq=1 ttl=63 time=1.20 ms
64 bytes from server1 (10.3.2.11): icmp_seq=2 ttl=63 time=0.626 ms
^C
--- server1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 0.626/0.914/1.203/0.290 ms
```

### Entracte : Donner un accès internet aux VM

```bash
[arthur@router ~]$ sudo firewall-cmd --add-masquerade --permanent
success
```

```bash
[arthur@router ~]$ sudo firewall-cmd --reload
success
```

```bash
[arthur@client1 ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=53 time=38.3 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=53 time=33.1 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 33.123/35.730/38.338/2.614 ms
```

```bash
[arthur@client1 ~]\$ dig ynov.com

; <<>> DiG 9.11.4-P2-RedHat-9.11.4-9.P2.el7 <<>> ynov.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 50746
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1452
;; QUESTION SECTION:
;ynov.com. IN A

;; ANSWER SECTION:
ynov.com. 10800 IN A 217.70.184.38

;; Query time: 70 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Sun Mar 15 17:28:17 CET 2020
;; MSG SIZE rcvd: 53
```

```bash
[arthur@client1 ~]\$ sudo yum install -y epel-release
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile

- base: centos.crazyfrogs.org
- extras: ftp.pasteur.fr
- updates: mirror.in2p3.fr
  Resolving Dependencies
  --> Running transaction check
  ---> Package epel-release.noarch 0:7-11 will be installed
  --> Finished Dependency Resolution

Dependencies Resolved

========================================================================================================================
Package Arch Version Repository Size
========================================================================================================================
Installing:
epel-release noarch 7-11 extras 15 k

# Transaction Summary

Install 1 Package

Total download size: 15 k
Installed size: 24 k
Downloading packages:
epel-release-7-11.noarch.rpm | 15 kB 00:00:00
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : epel-release-7-11.noarch 1/1
Verifying : epel-release-7-11.noarch 1/1

Installed:
epel-release.noarch 0:7-11

Complete!
```

```bash
[arthur@client1 ~]\$ sudo yum install -y sl
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
epel/x86_64/metalink | 19 kB 00:00:00

- base: centos.crazyfrogs.org
- epel: fr2.rpmfind.net
- extras: ftp.pasteur.fr
- updates: mirror.in2p3.fr
  epel | 5.3 kB 00:00:00
  (1/3): epel/x86_64/group_gz | 95 kB 00:00:00
  (2/3): epel/x86_64/updateinfo | 1.0 MB 00:00:02
  (3/3): epel/x86_64/primary_db | 6.7 MB 00:00:08
  Resolving Dependencies
  --> Running transaction check
  ---> Package sl.x86_64 0:5.02-1.el7 will be installed
  --> Finished Dependency Resolution

Dependencies Resolved

========================================================================================================================
Package Arch Version Repository Size
========================================================================================================================
Installing:
sl x86_64 5.02-1.el7 epel 14 k

# Transaction Summary

Install 1 Package

Total download size: 14 k
Installed size: 17 k
Downloading packages:
warning: /var/cache/yum/x86_64/7/epel/packages/sl-5.02-1.el7.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID 352c64e5: NOKEY
Public key for sl-5.02-1.el7.x86_64.rpm is not installed
sl-5.02-1.el7.x86_64.rpm | 14 kB 00:00:00
Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
Importing GPG key 0x352C64E5:
Userid : "Fedora EPEL (7) <epel@fedoraproject.org>"
Fingerprint: 91e9 7d7c 4a5e 96f1 7f3e 888f 6a2f aea2 352c 64e5
Package : epel-release-7-11.noarch (@extras)
From : /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : sl-5.02-1.el7.x86_64 1/1
Verifying : sl-5.02-1.el7.x86_64 1/1

Installed:
sl.x86_64 0:5.02-1.el7

Complete!
```

```bash
[arthur@client1 ~]\$ sl
```

### III. Plus de tcpdump

#### 1. TCP et UDP

##### A. Warm-up

```bash
[arthur@client1 ~]$ sudo firewall-cmd --add-port=9999/tcp --permanent
success
```

```bash
[arthur@client1 ~]$ sudo firewall-cmd --add-port=9999/udp --permanent
success
```

```bash
[arthur@server1 ~]$ sudo firewall-cmd --add-port=9999/tcp --permanent
success.
```

```bash
[arthur@server1 ~]$ sudo firewall-cmd --add-port=9999/udp --permanent
success
```

##### B. Analyse de trames

##### TCP

```bash
[arthur@client1 ~]$ nc server1 9999
yes
sa marche
^C
```

```bash
[arthur@server1 ~]$ nc -l -p 9999
yes
sa marche
^C
```

##### UDP

```bash
[arthur@client1 ~]$ nc -u server1 9999
dans l'autre sense
je te la met a l'envers
^C
```

```bash
[arthur@server1 ~]$ nc -u -l -p 9999
dans l'autre sense
je te la met a l'envers
wait la co n'est plus là
^C
```

#### 2. SSH

```bash
[arthur@client1 ~]$ ssh arthur@10.3.2.11 -p 7777
arthur@10.3.2.11's password:
Last login: Mon Mar 16 10:43:15 2020 from 10.3.2.1
Last login: Mon Mar 16 15:05:16 2020 from 10.3.2.1
```
