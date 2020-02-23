
## 参考
浅谈在代理环境中的 DNS 解析行为
https://blog.skk.moe/post/what-happend-to-dns-in-proxy/

漫谈各种黑科技式 DNS 技术在代理环境中的应用
https://medium.com/@TachyonDevel/%E6%BC%AB%E8%B0%88%E5%90%84%E7%A7%8D%E9%BB%91%E7%A7%91%E6%8A%80%E5%BC%8F-dns-%E6%8A%80%E6%9C%AF%E5%9C%A8%E4%BB%A3%E7%90%86%E7%8E%AF%E5%A2%83%E4%B8%AD%E7%9A%84%E5%BA%94%E7%94%A8-62c50e58cbd0

## MAC DNS 缓存清理
```
sudo killall -HUP mDNSResponder
sudo killall mDNSResponderHelper
sudo dscacheutil -flushcache
```

## MAC DNS 查看
```
scutil --dns
```

## MAC DNS 分流
```
cat /etc/resolver/geekbang.org
domain geekbang.org
nameserver 172.72.1.1
search_order 1
timeout 5
```

## 调整 VPN 配置
https://diaryproducts.net/about/operating_systems/mac_os_x/overriding_dhcp_or_vpn_assigned_dns_servers_in_mac_os_x_leopard
https://rakhesh.com/powershell/vpn-client-over-riding-dns-on-macos/

```
#!/bin/sh
# When the ppp link comes up, this script is called with the following
# parameters
#       $1      the interface name used by pppd (e.g. ppp3)
#       $2      the tty device name
#       $3      the tty device speed
#       $4      the local IP address for the interface
#       $5      the remote IP address
#       $6      the parameter specified by the 'ipparam' option to pppd

# 清除 VPN DNS
SERVICES=$(echo "list State:/Network/Service/[^/]+/PPP" | /usr/sbin/scutil | cut -c 16- | cut -d / -f 1-4)

for SERVICE in $SERVICES
do
    echo "get $SERVICE/DNS" | /usr/sbin/scutil
    echo "d.add ServerAddresses *" | /usr/sbin/scutil
    echo "set $SERVICE/DNS" | /usr/sbin/scutil
done

# 增加路由规则

#prod-adm
/sbin/route add 39.105.227.243 -interface $1

#test-waf
/sbin/route add 47.94.5.192 -interface $1
#test-com
/sbin/route add 101.200.55.20 -interface $1
#test-base
/sbin/route add 39.106.15.84 -interface $1

#stage-com
/sbin/route add 47.95.147.161 -interface $1

#support-com
/sbin/route add 47.93.217.102 -interface $1
#support-code
/sbin/route add 47.94.217.175 -interface $1
#support-vpn
#/sbin/route add 39.106.146.191 -interface $1
#support-dns/jump
/sbin/route add 172.72.0.0/16 -interface $1

#kong
/sbin/route add 39.106.9.12 -interface $1
/sbin/route add 39.107.27.25 -interface $1
/sbin/route add 39.96.57.142 -interface $1

#wxspider
/sbin/route add 47.94.197.97 -interface $1
```

## CENTOS 增加路由规则
```
# 删除路由规则
route del default dev ppp0

# 增加路由规则

#prod-adm
route add 39.105.227.243 gw 192.168.42.1

#test-waf
route add 47.94.5.192 gw 192.168.42.1
#test-com
route add 101.200.55.20 gw 192.168.42.1
#test-base
route add 39.106.15.84 gw 192.168.42.1

#stage-com
route add 47.95.147.161 gw 192.168.42.1

#support-com
route add 47.93.217.102 gw 192.168.42.1
#support-code
route add 47.94.217.175 gw 192.168.42.1
#support-vpn
#route add 39.106.146.191 gw 192.168.42.1
#support-dns/jump
route add -net 172.72.0.0/16 gw 192.168.42.1

#kong
route add 39.106.9.12 gw 192.168.42.1
route add 39.107.27.25 gw 192.168.42.1
route add 39.96.57.142 gw 192.168.42.1

#wxspider
route add 47.94.197.97 gw 192.168.42.1
```