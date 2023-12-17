# Jarkom-Modul-5-D21-2023

## Kelompok D21
Akbar Putra Asenti P. 5025211004

Farrela Ranku Mahhisa 5025211129

## Nomor 1

```
iptables -t nat -A POSTROUTING -o eth0 -j SNAT -s 10.32.0.0/20 --to-source 192.168.122.2
```

## Nomor 2

```
iptables -A INPUT -p udp -j DROP
iptables -A INPUT -p tcp ! --dport 8080 -j DROP
```

## Nomor 3

```
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```

## Nomor 4

```
iptables -A INPUT -p tcp --dport 22 ! -s 10.32.8.0/22 -j REJECT
```

## Nomor 5

```
iptables -A INPUT -m time --timestart 16:00 --timestop 8:00 -j DROP
iptables -A INPUT -m time --weekdays 6,7 -j DROP
```

## Nomor 6

```
iptables -A INPUT -m time --weekdays 1,2,3,4 --timestart 12:00 --timestop 13:00 -j DROP
iptables -A INPUT -m time --weekdays 5 --timestart 11:00 --timestop 13:00 -j DROP
```

## Nomor 7

```
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 10.32.8.2 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.32.14.134:80
iptables -A PREROUTING -t nat -p tcp --dport 80 -d 10.32.8.2 -m statistic --mode nth --every 1 --packet 0 -j DNAT --to-destination 10.32.8.2:80
```

```
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 10.32.14.134  -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 10.32.8.2:443
iptables -A PREROUTING -t nat -p tcp --dport 443 -d 10.32.14.134  -m statistic --mode nth --every 1 --packet 0 -j DNAT --to-destination 10.32.14.134:443
```

## Nomor 8

```
iptables -A INPUT -s 10.32.14.144/30  -m time --datestop 2024-3-20 -j DROP
```

## Nomor 9

```

```

## Nomor 10

```

```