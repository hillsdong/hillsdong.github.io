# 网络问题

## 连接VPN后出口IP不变
vpn高级配置项里可以选择是否所有流量走vpn，mac默认公网流量不走vpn，导致出口IP不变

## 连接VPN后有些网站不可访问
连接vpn后，虽然默认公网流量不走vpn，但dns使用vpn中配置的dns，其解析的IP地址有可能本地网络不可达，可配置多个dns

## 配置静态地址后主机不可访问
子网掩码配置不正确

## 乘客协议、隧道协议和承载协议
![](https://static001.geekbang.org/resource/image/a7/f1/a7c0a7fd2334d7145d093cf24bc6d7f1.jpg)
