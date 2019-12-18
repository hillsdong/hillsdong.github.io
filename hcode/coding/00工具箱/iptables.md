
## iptables 四种状态

ESTABLISHED 、 INVALID 、 NEW 和 RELATED


## 规则 rules
过滤条件：源地址、目的地址、传输协议（如TCP、UDP、ICMP）和服务类型（如HTTP、FTP和SMTP）

处理规则：放行（accept）、拒绝（reject）和丢弃（drop）

## 链 chains
PREROUTING INPUT FORWARD OUTPUT POSTROUTING

## 表
raw表、filter表、nat表和mangle表

优先级：Raw——mangle——nat——filter

## 类型
入站：PREROUTING -> INPUT

转发：PREROUTING -> FORWARD -> POSTROUTING

出站：OUTPUT -> POSTROUTING

## 举例


```
iptables -A INPUT -p TCP --dport 22 -j REJECT --reject-with ICMP echo-reply
```
