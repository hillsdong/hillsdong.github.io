# 路由表

## 命令
- 路由守护程序 routed，动态路由管理
- 路由命令 route，静态路由管理
- netstat命令，路由查看

## linux
```
netstat -nr
route -n
route add -net 10.67.0.0/16 gw 192.168.120.254
```
```
# 格式: ip route { add | del | change | append | replace } ROUTE
# 查看所有路由表
$: ip rule list
# 查看主路由表
$: ip route list
# 查看某路由表
$: ip route list table local
# 增加静态路由
$: ip route add 192.168.0.0/24 via 172.16.15.253 dev eth0
# 删除静态路由
$: ip route del 192.168.0.0/24 via 172.16.15.253 dev eth0
# 查看指定目的IP的路由信息
$: ip route get 172.18.0.10
```

## macos
```
netstat -nr
route -n add defalut  10.13.31.1
route -n add -net 10.0.0.0/8  10.13.31.1
```

## macos vpn hook
位置 /etc/ppp/ip-up
```
#!/bin/sh
/sbin/route add 192.168.1.0/24 -interface $5
/sbin/route add 0.0.0.0/0 -interface $1
```
可用变量
```
Argument	Description	Example value
$1	The VPN interface	ppp0
$2	unknown	
$3	unknown	0
$4	Your IP on VPN network	10.0.13.37
$5	IP of VPN gateway	10.0.13.1
$6	IP of your local network gateway	192.168.1.1
```
```
$ networksetup -listallnetworkservices
$ networksetup -setadditionalroutes 'My Office' 172.16.0.0 255.255.240.0 192.168.0.1 192.168.2.0 255.255.255.0 192.168.0.1
```