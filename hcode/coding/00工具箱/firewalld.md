
## firewalld 使用

配置文件路径：`/usr/lib/firewalld/services/`
使用：

```
sudo firewall-cmd --zone=public --add-service=http --permanent
sudo firewall-cmd --reload
```

## openwrt
透明代理
```
config redirect
	option src              lan
	option proto            tcp
	option src_dport        80
	option dest_port        3128
```

https://openwrt.org/zh-cn/doc/uci/firewall


## 参考
更多：https://linux.cn/article-8098-1.html