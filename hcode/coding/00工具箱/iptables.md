
## iptables
- 基于内核netfilter模块
- /etc/sysconfig/iptables-config

## iptables 四种状态

ESTABLISHED 、 INVALID 、 NEW 和 RELATED


## 规则 rules
过滤条件：源地址、目的地址、传输协议（如TCP、UDP、ICMP）和服务类型（如HTTP、FTP和SMTP）

处理规则：
- 放行（accept）、拒绝（reject）和丢弃（drop）
- REDIRECT 、MASQUERADE、 LOG、ULOG、DNAT、RETURN、TOS、SNAT、MIRROR、QUEUE、TTL、MARK
## 链 chains
PREROUTING INPUT FORWARD OUTPUT POSTROUTING

## 表
raw表、filter表、nat表和mangle表

优先级：Raw——mangle——nat——filter

## 类型
入站：PREROUTING -> INPUT

转发：PREROUTING -> FORWARD -> POSTROUTING

出站：OUTPUT -> POSTROUTING

## 状态
ESTABLISHED 、 INVALID 、 NEW 和 RELATED

## 操作

- -t table, -j judge, -N 建立用户定义链，! 取反

```
-P  --policy        <链名>  定义默认策略
-L  --list          <链名>  查看iptables规则列表
-A  --append        <链名>  在规则列表的最后增加1条规则
-I  --insert        <链名>  在指定的位置插入1条规则
-D  --delete        <链名>  从规则列表中删除1条规则
-R  --replace       <链名>  替换规则列表中的某条规则
-F  --flush         <链名>  删除表中所有规则
-Z  --zero          <链名>  将表中数据包计数器和流量计数器归零
-X  --delete-chain  <链名>  删除自定义链
-v  --verbose       <链名>  与-L他命令一起使用显示更多更详细的信息

-i --in-interface    网络接口名>     指定数据包从哪个网络接口进入，
-o --out-interface   网络接口名>     指定数据包从哪个网络接口输出
-p ---proto          协议类型        指定数据包匹配的协议，如TCP、UDP和ICMP等
-s --source          源地址或子网>   指定数据包匹配的源地址
   --sport           源端口号>       指定数据包匹配的源端口号
   --dport           目的端口号>     指定数据包匹配的目的端口号
-m --match           匹配的模块      指定数据包规则所使用的过滤模块
```

## 举例
```
# 配置INPUT链的默认规则为DROP
iptables -P INPUT DROP

# filter表中追加一条规则，允许IP为 192.168.1.10 的主机访问
iptables -t filter -A INPUT -s 192.168.1.10 -j ACCEPT

# filter为默认表，可不写
iptables -A INPUT -s 192.168.1.10 -j ACCEPT

# 允许状态匹配的包
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# 禁止ssh协议
iptables -A INPUT -p TCP --dport 22 -j REJECT --reject-with ICMP echo-reply
```


## 参考
https://www.jianshu.com/p/5f38e7253fc8
https://www.jianshu.com/p/586da7c8fd42
https://www.jianshu.com/p/6d77d73325bf

![鸟哥iptables流程图](http://cdn1.klib.cn/u/bKFmOh.jpg)