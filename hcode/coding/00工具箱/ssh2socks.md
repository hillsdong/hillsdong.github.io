## mac
```
sudo /sbin/route delete 47.75.109.99 -gateway 10.2.8.1
sudo /sbin/route add 47.75.109.99 -gateway 192.168.2.1

sudo /sbin/route delete 47.75.109.99 -gateway 192.168.2.1
sudo /sbin/route add 47.75.109.99 -gateway 10.2.8.1

screen autossh -M 6666 -D 127.0.0.1:1080 root@47.75.109.99
Ctrl+A D
```

```
[Endpoint]
Proxy, builtin, socks, address=127.0.0.1, port=1080
Direct, builtin, freedom, domainStrategy=UseIP
Dns-Out, builtin, dns

[RoutingRule]
DOMAIN-KEYWORD, geosite:cn, Direct
GEOIP, cn, Direct
GEOIP, private, Direct
FINAL, Proxy

[Dns]
hijack = Dns-Out

[DnsServer]
localhost
8.8.8.8
```

## openwrt
```
ip route add 47.75.109.99 via 192.168.1.1 dev eth0
screen autossh -M 6666 -D 127.0.0.1:1080 root@47.75.109.99
Ctrl+A D
```