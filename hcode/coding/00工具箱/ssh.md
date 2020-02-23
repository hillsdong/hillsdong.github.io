## SSH 几个用途
```
# 提供socks代理
ssh -D 127.0.0.1:1080 user@server

# 正向端口代理
ssh -L 127.0.0.1:1080:rhost:rport user@server

# 反向端口代理
ssh -R rhost:rport:127.0.0.1:1080 user@server

# 通过socks代理登录
ssh -o ProxyCommand='nc -x 127.0.0.1:1080 %h %p' user@server
```