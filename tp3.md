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
client1.net1.tp3
[arthur@client1 ~]$
```

```bash
[arthur@server1 ~]$ hostname --fqdn
server1.net2.tp3
[arthur@server1 ~]$
```

```bash
[arthur@router ~]$ hostname --fqdn
router.tp3
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
