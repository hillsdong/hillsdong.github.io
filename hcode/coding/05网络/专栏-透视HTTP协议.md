## 开篇 To Be a HTTP Hero
- 问题：nginx配置/uri/url/字符集/http缓存/https
- 困境：正式资料少/网上资料杂/权威资料难
- 广度：TCP/IP / DNS / SSL/TLS / Web Server
- 深度：RFC文档/Wireshark抓包/Nginx实验/测试用例
- Hreo：万维网诞生30周年/HTTP/1.1诞生20周年

## 破冰01-07 HTTP相关概念/相关协议/四层/七层/域名/实验环境
学习记录：200131/200201，共4小时

### 01 历史
- 196x年代，APRA网 / 197x年代，TPC/IP / **1989**年，URI/HTML/HTTP
- 199x年，HTTP0.9，包含GET/限于文本
- 1993年，Mosaic图文混排 / 1995年，Apache / 1992年，JPEG / 1995年，MP3
- 1996年，HTTP/1.0，增加HEAD/POST/响应码/协议版本/Header，不限于文本，但不是标准
- 1995年，网景Netscape Navigator和微软IE大战
- 1999年，HTTP/1.1，增加PUT/POST/缓存/持久连接/响应分块/强制Host头，正式标准RFC2616
- 2014年，修订，拆分为RFC7230-7235，无实质改动 / Google开发Chrome，推出SPDY协议
- 2015年，HTTP/2，考虑宽带/移动/不安全，二进制协议/多请求/头部压缩/推送/加密 ，RFC7540
- Google推出QUIC协议，HTTP/3制定中 / GRPC基于HTTP/2
- 课后：HTTP发展原动力是资本/HTTP是一个被动演进的协议，促进又限制发展

### 02 概念
- HTTP：超文本传输协议，HyperTextTransferProtocol
- HTTP协议：确立了一种计算机间的通信规范，控制和错误处理方式
- 协议类型举例：传输协议/广播协议/寻址协议/路由协议/隧道协议/选举协议
- 传输协议：双向协议，计算机间传输数据
- 超文本：文本/图片/音频/视频，超链接
- 不是：单独实体，如浏览器/APP/操作系统/Web服务器 / 互联网 / 编程语言 / HTML / 孤立的
- 课后：HTTP也可以用于服务器间通信 / HTTP不是万能的/不是唯一的
- 小贴士：协议包括语法/语义/同步规则/错误处理 / WWW万维网是Internet互联网的一部分

![HTTP知识地图](http://cdn1.klib.cn/u/93ZIA9.jpg)

### 03 相关协议-偏实践
- Internet：HTTP/BT/Magnet/FTP/SSH等
- 浏览器/Web服务器/CDN（Content Delivery Network）/爬虫
- HTML/WebService（服务）/WAF（Web Application Firewall）
- 课后1：CDN对待浏览器和发冲没有差异，爬虫会可以伪装浏览器
- 课后2：WebService指处理具体业务的服务，WebServer指Web服务器

### 04 相关协议-偏理论
- 相关协议：TCP/IP / DNS / URI / HTTPS
- TCP/IP：TCP/IP/UDP/ICMP/ARP等
- 四层：链接层/IP网际层/TCP传输层/应用层
- IP：Internet Protocol，解决寻址路由问题，如何传送数据包
- IPV4：4组 . 分割的8位数字（0-255）组成，共有2^32个地址
- IPV6：8组 : 分割的16位数字（0-ffff）组成，共有2^128个地址
- TCP：Transmission Control Protocol，传输控制协议
- DNS：Domain Name System，根DNS服务器/顶级DNS服务器/权威DNS服务器/本地DNS
- URI：Uniform Resource Identifier，统一资源标识符，协议名：主机名/路径
- URL：Uniform Resource Location，统一资源定位符，是URI子集
- HTTPS：HTTP over SSL/TLS，SSL/TLS运行在TCP/IP上层，是可靠的传输协议
- SSL：Secure Socket Layer，3.0版本改名TLS，Transport Layer Security
- 代理分类：匿名代理/透明代理（公开身份）/正向代理（代表客户端）/反向代理（代表服务端）
- 代理举例：CDN，扮演透明代理和反向代理角色 / 负载均衡 / 内容缓存 / 安全防护 / 数据处理
- 代理协议：proxy protocal，代理软件HAProxy制定，不是RFC标准
- 课后1：DNS服务域名解析，URI服务资源定位，没什么关系
- 贴士1：IPV1/V2/V3不完善/IPV5内部研究
- 贴士2：2011年2月，ICANN宣布IPV4用尽
- 贴士3：HTTP可不依赖TCP/IP，如 HTTP over UNIX Domain Socket（进程间通信）

### 05 TPC/IP网络分层模型
- 四层：链接层/IP网际层/TCP传输层/应用层
- 格式：frame 帧/package 包/segemtn 段/message 报文
- OSI：Open System Interconnection Reference Mode
- 七层：physical/data link/network/transport/session/presentation/application
- 四层负载均衡工作在传输层，七层负载均衡工作在应用层
- 课后1：二层转发指基于MAC地址的数据转发，如交换机/三层路由指基于IP地址的数据路由，如路由器
- 课后2：DNS协议位于应用层/CDN工作在应用层
- 贴士1：MAC地址，Media Access Control Address
- MAC地址：6组 : 分割的8位数字(0-ff)组成，共有2^48个地址
- 贴士2：Unix套接字工作在五层

### 06 DNS域名
- 域名：n组 . 分割的字符串组成，共有n个地址
- 本质：名字空间系统
- 分级：根域名服务器/顶级域名服务器/权威域名服务器
- 缓存：非权威域名服务器/操作系统缓存/hosts文件
- 新玩法：重定向/名字空间/负载均衡/域名屏蔽/域名劫持
- 课后1：不存在域名解析过程，本机缓存/hosts/dns/根/顶级/没有
- 贴士1：总长度253字符内，每一级不超过63字符

### 07 搭建HTTP实验环境
- OpenResty/Wireshark/Telnet/Chrome

## 基础08-14 链路/报文/请求/响应/特点/优缺点
学习记录：200201，共3小时

### 08 链路
- DNS/TCP三次握手/请求/请求ACK/响应/响应ACK

### 09 报文
- TCP：发起方端口｜接收方端口｜其他数据」实际数据
- HTTP：start line起始行｜header头部｜CRLF空行｜entity实体
- 协议没有限制头部大小，Web服务器会限制，Nginx默认限制8k
- 请求行：Method｜SP空行｜URI｜SP｜Version｜CRLF
- 状态行：Version｜SP｜Status Code｜SP｜Reason｜CRLF
- 请求头/响应头：Field Name｜:｜Field Value｜CRLF
- 头部字段：不区分大小写/不可用空格/可用-/不可用_/可扩展/HTTP/1.1要求必须有Host
- Telnet：open www.chrono.com 80 / Ctrl+] 编辑模式
- 常用头字段：通用字段/请求字段/响应字段/实体字段
- 头字段举例：Host/User-Agent/Date/Serer/Content-Length
- 不定长body采用chunked方式分段传输
- 课后1：头字段后多加CRLF会把后面的头字段当作实体处理
- 课后2：头字段中:后只有一个空格是为了减小报文大小
- 贴士1：报文中空格可以用制表符代替
- 贴士2：Nginx可以通过配置允许头部字段中包含下划线
- 贴士3：X-Powered-By: PHP/7.0.22

### 10 请求方法
- 标准方法：GET/HEAD/POST/PUT / DELETE/TRACE/OPTIONS/CONNECT
- 非标准方法：MKCOL/COPY/MOVE/LOCK/UNLOCK/PATCH...，WebDAV应用
- 安全方法：GET/HEAD
- 幂等方法：GET/HEAD/PUT/DELETE
- 课后1：GET /db/table?id=1 DB/0.9

### 11 请求路径
- URI：scheme｜://｜user:passwd@｜host:port｜path｜?query|#fragment
- 转义：非ASCII码或者特殊字符转换成十六进制字节，前面加"%"，中文使用UTF-8编码
- 贴士1：query可以放在GET方法的body里

### 12 响应码
- 1xx：提示信息，表示中间状态 / 2xx：成功 / 3xx：重定向 / 4xx：客户端错误 / 5xx：服务器错误
- 101 Switching Protocols：如使用Upgrade字段将HTTP改为WebSocket协议时返回
- 200 OK / 204 No Content，响应无body
- 206 Partial Content，返回部分内容，断点续传，配合Content-Range头使用，如0-99/2000
- 301 Moved Permanently，永久重定向 / 302 Found，临时重定向，配合Loaction响应头
- 304 Not Modified，资源未修改，重定向缓存，配合If-Modified-Since使用
- 400 Bad Request，通用 / 403 Forbidden，禁止访问 / 404 Not Found
- 405 Method Not Allowed / 406 Not Acceptable，无法满足
- 408 Request Timeout，请求超时 / 409 Conflict 请求冲突
- 413 Request Entity Too Large / 414 Request-URI Too Long
- 429 Too Many Requests / 431 Request Header Fields Too Large
- 500 Interanl Server Error / 501 Not Implemented 不支持
- 502 Bad Gateway / 503 Service Unavailable 服务器忙，配合Retry-After
- 贴士：308/307 类似301/302，但不允许更改请求方法

### 13 特点
- 灵活可扩展/可靠传输/应用层协议/请求-应答/无状态
- TCP有连接有状态/UDP无连接无状态/HTTP有连接无状态
- 课后1：有连接导致连接数限制/ TCP/IP算法导致跟不上带宽 /请求-应答模式导致服务端无法主动推
- 课后2：可靠/无状态最重要
- 贴士1：可靠是相对的，消息中间件才绝对可靠
- 贴士2：HTTP/1.1  默认启用keepalive保持长连接

### 14 优缺点
- 简单灵活可扩展：原语没有写死，可基于TCP/UNIXX Domain Socket/SSL/QUIC等
- 应用广泛、环境成熟：跨语言、跨平台
- 无状态：服务端简单可伸缩，但有重复认证等缺点
- 明文传输：不安全
- 不安全
- 性能：不算差/不够好，对头堵塞（Head-of-line blocking）


## 进阶15-22 实体/大文件传输/连接管理/重定向/跳转/Cookie/缓存/代理/缓存代理
学习记录：200212，共1小时 / 200213，共1小时 / 200214，共1小时 / 200215，共1小时

### MIME type
- 多用途互联网邮件扩展 Multipurpose Internet Mail Extensions MIME
- MIME分为八大类，形式是 type/subtype
- text：text/html 超文本文档 text/plain 纯文本文档 text/css 样式表 ...
- image：image/gif image/jpeg image/png ...
- audio/video：audio/mpeg video/mp4 ....
- application：application/json application/javascript application/pdf
- application/octet-stream 未明二进制数据 ...

### Encoding type
- gzip：GNU zip 压缩格式
- deflate：zlib 压缩格式
- br：专门用于 HTTP 优化的新压缩算法 Brotil

### 语言
- 语言类型：语言-方言，如 en-US 美式英语 en-GB 英式英语 zh-CN 汉语
- 字符集：ASCII 英语 GBK BIG5 汉语 Shift_JIS 日语 Unicode（UTF-8） 世界

### 头字段
- 请求头 Accept：客户端可理解的 MIME type，如 text/html,image/webp
- 请求头 Accept-Encoding：客户端支持的压缩格式，可空
- 请求头 Accept-Language：客户端可理解的自然语言，如 zh-CN,zh,en
- 请求头 Accept-Charset：客户端可理解的字符集，如 uft-8，一般无
- 请求响应头 Content-Type：响应内容格式，如 text/html
- 请求响应头 Contnet-Encoding：实际使用的压缩格式，可空
- 请求响应头 Content-Language：实际使用的自然语言，一般无
- 请求响应头 Content-Type：实际使用的字符集，如 text/html; charset=uft-8
- quality factor：请求头中可用 q 表示权重，如 text/html;q=0.8
- 请求头Vary: 客户端要求参考头，如 Accept-Encoding,User-Agent,Accept
- 课后2：Content-Type: application/json;charset=utf-8

### 大文件
- 应用：视频 / 下载
- 分块请求 请求头：Transfer-Encoding: chunnked
- 分块编码 实体规则：十六进制长度 \n 数据块 \n ... \n 0
- 范围请求 请求头：Range：bytes=0-31
- 范围请求 响应头：Accept-Ranges: bytes Content-Range: bytes 0-31/97
- 多端请求 请求头：Range：bytes=0-9, 20-29
- 多段数据 响应头：Content-Type: multipart/byteranges; boundary=0000001
- 多段数据 实体规则：分隔符 \n Content-Type: test/plain \n Content-Range: bytes 0-9/96 \n \n Body \n ... \n 分隔符 

### 连接管理
- 短连接 HTTP/0.9 HTTP/1.0，被称作无连接，效率低
- 长连接 HTTP/1.1，持久连接/连接保活/连接复用
- 请求响应头 Connection: keep-alive 保持连接
- 请求响应头 Connection: close 关闭连接
- Nginx 用 keepalive-timeout 配置一次连接最大维持时长
- Nginx 用 keepalive-requests 配置一次连接最大请求数
- 请求响应头 Keep-Alive: timeout=value 不遵守，不常见
- HTTP 队头堵塞：“请求-应答”机制导致，串行请求
- 性能优化：并发请求，6-8个 / 域名分片
- 贴士：长连接在 TCP 协议层也有优势，避免连接时的慢启动/拥塞窗口
- 贴士：长连接机制必须指定 Content-Length 或分块传输
- 贴士：DDos 利用长连接特性，导致服务器“拒绝服务”
- 贴士：Connection: Upgrade 用来升级协议，如 HTTP 到 WebSocket
- 留言：TCP 也有对头堵塞

### 重定向
- 301 永久 302 临时
- 响应头 Location: /index.html
- 303 临时，GET方法 / 307 临时，请求方法和实体不允许变动 / 308 永久
- 300 Multiple Choices 返回包含多个链接的页面
- 响应头 Refresh: 5; url=xxx 实现延时重定向
- 请求头 Referer/Referrer-Policy，表示跳转来源
- Ningx 等服务器可实现内部重定向，没有额外性能损失
- 不受同源策略限制

### Cookie
- 响应头 Set-Cookie: a=xxx 可以有多行
- 请求头 Cookie: a=xxx;b=yyy
- Set-Cookie 有效期 Max-Age=10; Expires=Fri, 07-Jun-19 00:19:00 GMT
- Set-Cookie 作用域 Domain=klib.cn; Path=/
- Set-Cookie 安全性 HttpOnly 禁止JS等其他方式访问
- Set-Cookie 安全性 SameSite=Strict 不能跨站发送 / Lax 禁止POST跨站发送 / Secure 只能用HTTPS协议发送
- 应用：状态保持/广告跟踪
- DNT：Do Not Track 防止滥用 Cookie 搜集用户隐私 
- P3P： Platform for Privacy Preferences Project
- Cookie 并不是 HTTP 标准，并不是用 , 分割
- 不指定过期时间时 Cookie 随浏览器关闭失效

### 缓存
- 响应头 Cach-Control: no-store,no-cache,must-revalidate,max-age
- 请求头 Cach-Control: no-cache，用于刷新
- 响应头 Last-modified，最后修改时间
- 响应头 Etag，资源唯一标识，弱TAG W/ 开头
- 请求头 if-Modified-Since
- 请求头 if-None-Match
- 响应码 304 Not Modified
- 响应头 Expires 类似 Cach-Control，已过时，优先级低
- 浏览器默认缓存时间：(Date - Last-modified) * 10%
- Cache 无法使用JS等脚本获取

### 代理服务（负载均衡）
- 服务本身不产生内容，处于中间位置准发上下游的请求和响应
- 匿名代理/透明代理/正向代理/反向代理
- 计算机科学领域里的任何问题，都可以通过引入一个中间层来解决
- 反向代理基本功能是负载均衡，还有健康检查/安全防护/加密卸载/数据过滤/内容缓存
- 请求响应头 Via：proxy1, proxy2（主机名）
- 请求头 X-Forwarded-For: 客户端真实IP, 代理IP
- 请求头 X-Real-IP: 客户端真实IP
- 请求头 X-Forwarded-Host 请求的原始域名 X-Forwarded-Proto 请求的原始协议名
- 代理协议v1 PROXY TCP4 1.1.1.1 2.2.2.2 55555 80 \r\n ，请求头外，不改动原始协议
- 课后1：代理增加了复杂度
- 课后2：负载均衡算法有 随机/轮询/ip hash/最少连接数/最快连接数/权重/根据负载动态调整
- 贴士：常用代理软件 HAProxy/Squid/Varnish/Nginx
- 贴士：X-Via=Via
- 贴士：X-Forwarded-For 不可信
- 贴士：Forwarded 是协议规定字段，但应用不多

### 代理缓存（CDN）
- Cache-Control 字段也可以控制缓存代理，常用的有 private / s-maxage / no-transform 等字段，必须配合 Last-modified 和 ETag 等字段使用
- Vary 用于声明缓存时要区分哪些头 
- 自定义请求方法 Purge 用于请求里代理缓存

## 安全23-29 SSL/TLS / 对称/非对称加密 / TLS1.2连接 / TLS1.3特征 / HTTPS优化
学习记录：200216/200217/200218
### SSL/TLS
- 安全：机密性/完整性/身份认证/不可否认
- 机密性：Secrecy/Confidentiality 不能让不相关人看到不该看的东西
- 完整性：Integrity/一致性 不能被篡改
- 身份认证：Authentication 证明身份
- 不可否认：Non-repudiation/Undeniable/不可抵赖 不能否认已经发生过的行为
- HTTPS = HTTP + SSL/TLS
- SSL：Secure Sockets Layer，会话层 网景1994，v2/v3
- TLS：Transport Layer Security，IEEF1999，TLSv1.0=SSLv3.1
- TLS1.2 目前应用最广泛，由记录协议/握手协议/告警协议/变更密码规范协议/扩展协议组成
- TLS 应用了 对称加密/非对称加密/身份认证 等密码学技术
- 加密套件：chiper suite，也叫密码套件，建立连接需要的一组加密算法
- OpenSSL：开源密码学程序库和工具包，几乎支持所有公开的加密算法和协议，最新版本为1.1.1
- OpenSSL密码套件定义形式：ECDHE_RSA_AES_256_GCM_SHA384
- 课后：HTTPS区别：基于不同协议，是否明文，是否安全
- 课后：机密性：加密内容；完整性：哈希校验
- 贴士：FTPS=FTP+SSL/TLS，LDAPS=LDAP+SSL/TLS
- 贴士：OpenSSL1.0.1版本有个著名的心脏出血（Heart Bledd）漏洞
- 贴士：TLS密码套件定义形式：TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384

### 对称加密
- 名词：加密 encrypt / 密钥 key / 明文 plain text / 密文 chiper text / 解密 decrypt
- 密钥只有一个，长度单位是 位 bit
- 不安全算法：RC4/DES/3DES，安全算法：AES/ChaCha20
- AES Advanced Encryption Standard 高级加密标准，密钥长度可是 128/192/256
- ChaCha20 Google设计，密钥长度固定 256
- 对称加密的分组模式，可以用固定长度密钥加密任意长度明文，不安全：ECB/CBC/CFB/OFB，安全：AEAD(GCM/CCM/Poy1305)
- AES128-CBC：采用AES算法，密钥长度是128，采用CBC分组模式的对称加密算法

### 非对称加密
- 密钥有两个，分为 公钥 public key / 私钥 private key
- 算法：DH / DSA / RSA / ECC
- RSA：基于“整数分解”，采用两个超大素数乘机生成密钥，推荐2048位
- ECC：Elliptic Curve Cryptography，基于“椭圆曲率离散数学”，224位～RSA2048位- ECC采用 ECDHE 交换密钥，ECDSA 数字签名
- ECC常用曲线：P-256（官方）/x25519（安全/快速）

### 混合加密（机密性）
- TLS中，使用 RSA/ECDHE 等非对称加密算法交换对称加密算法密钥，然后使用对称加密算法
- 课后：私钥加密公钥解密用途：身份认证/不可抵赖
- 贴士：对称加密可分为：块加密（AES）/流加密（ChaCha20）
- 贴士：比特币使用ECC（secp256k1曲线）
- 贴士：TLS1.2 要求 TLS_RSA_WITH_AES_128_CBC_SHA，TLS1.3要求 TLS_AES_128_GCM_SHA256

### 摘要算法（完整性）
- 散列/哈希函数
- 具有单向性和雪崩效应，微小不同会导致输出剧烈变化，也被 TLS 生成伪随机数
- MD5 Message-Digest 5，生成 16字节
- SHA-1 Secure-Hash Algorithm 1，生成 20字节
- SHA-2：SHA224/SHA256/SHA384 分别生成 28/32/48字节
- 完整性的前提是机密性
- 摘要结果：哈希消息认证码 HMAC

### 数字签名（不可否认）
- 数字签名：使用私钥和摘要算法实现
- 过程：签名-验签

### 数字证书（身份认证）
- CA含义：Certificate Authority 证书认证机构
- CA机构：DigiCert / VeriSign / Entrust / Let's Encrypt
- CA签发证书分类：DV / OV / EV，可信度递增
- CA证明自己：各CA根证书（自签名证书）+ 证书链
- 证书体系：PKI Public Key Infrastrcture
- 弱点：签发错误，解决1：CRL Certificate revocation list 证书吊销列表
- 弱点：签发错误，解决2：OCSP Online Certificate Status Protocol
- 弱点：机构失信，解决：浏览器CA黑名单
- 课后：服务器返回证书链，用根证书解密得到一级证书，依次解密得到网站公钥
- 贴士：Let's Encrypt 只颁发90天的DV证书
- 贴士：DER为二进制的证书格式，PEM为ASCII码的证书格式
- 留言：nonce防止重放

### TLS1.2
- TLS协议：Record Protocol / Alert Protocol / Handshake Protocol / Change Cipher Spec Protocol
- 

### TLS1.3

### HTTPS优化

### 迁移到HTTPS

## 飞翔30-33 HTTP/2 / HTTP/3
学习记录：200220，共小时

### HTTP/2
- 不再使用小版本号
- 目标是改进性能
- 向前兼容HTTTP/1，语义不变，语法天翻地覆



## 探索34-38 Nginx/OpenResty/WAF/CDN/WebSocket
学习记录：200219，共2小时

### Nginx
- 没有使用多进程，使用“进程池（worker）+ 单线程”的工作方式
- 线程绑定CPU，最大化减少进程切换成本
- I/O 多路复用（epoll），Web 服务器根本上是 I/O 密集型
- epoll，连接管理由操作系统内核处理，应用程序内存占用小
- 多阶段处理：handle/filter/upstream/balance
- handle/filter 模块，“职责链”模式设计

### OpenResty
- 2009年诞生，淘宝章义春 agentzh
- ngx_lua 模块扩展
- lua 协程特性 结合 nginx 事件机制，实现“同步非堵塞”编程范式
- lua 热加载特性，实现动态配置
- luaJIT Just In Time，把 Lua 代码即时编译成本地代码，消除脚本语言劣势
- 阶段式处理，增加 init 和 ssl 阶段，实现各种动态配置

### WAF
- Web Application Firewall
- 攻击：DDos distributed denial-of-service attack 洪水攻击
- 攻击：SQL 注入
- 攻击：HTTP 头注入
- 攻击：XSS 跨站脚本攻击，属于 JS 代码注入，利用脚本获取未设防的 Cookie
- 功能：IP 黑白名单 / URI 黑白名单 / 限连限速，防护DDos攻击 / 过滤请求报文，防止代码注入攻击 / 过滤响应报文，防止敏感信息泄露 / 审计日志，记录所有入侵操作
- 产品：ModSecurity，WAF事实标准，核心组件：规则引擎/规则集
- 产品：Naxsi
- 产品：OpenResty+

### CDN
- Conetent Distribution Net
- 缓存代理技术
- 只缓存静态资源
- 关键组成：全局负载均衡/缓存系统
- DNS负载均衡：考虑用户IP/用户运营商/边缘节点负载/健康状态等
- 缓存系统指标：命中率/回源率，手段：增加存储/多级缓存
- 软件：Squid/Varnish/ATS/Nginx/OpenResty
- 功能延伸：SSL加速 / 内容优化（数据压缩/格式转换）/ 防盗链 / 安全防护
- 课后：动态资源可利用 CDN 进行边缘计算或路径优化

### WebSocket
- 普通 Socket 运行在 TCP/IP 协议上，WebSocket 借用 HTTP 协议，简单封装 TCP
- HTTP/2 针对“对头阻塞” / WebSocket 针对“请求-应答”模式
- “请求-应答”模式缺点：半双工，同一时刻只能一个方向上有动作，服务器只能被动响应
- 克服“轮询”技术不经济的缺点
- 特点：全双工/采用二进制帧结构，语法于HTTP不兼容/协议ws或wss
- 协议升级：HTTP GET，增加头 Connection: Upgrade / Upgrade: websocket
- 额外字段：Sec-WebSocket-Key / Sec-WebSocket-Version
- 贴士：利用ping/pong帧保持连接

## 总结39-40 HTTP性能优化
学习记录：200220，共1小时

### HTTP 服务器
- 性能指标：吞吐量 RPS/TPS/QPS requests per second
- 性能指标：并发数 concurrency
- 性能指标：响应时间 time per request
- CPU/硬盘/内存/网卡的资源占用率

### HTTPS 客户端
- 性能指标：延迟 latency 距离（光速）/带宽/DNS查询/TCP握手
- TTFB：Time To First Byte

### HTTPS 传输链路
- 性能指标：传输速度，分为 第一公里/中间一公里/最后一公里

### 优化
- 花钱：升级硬件/买CDN
- 开源：开发服务器潜力，如使用Nginx，启用长连接，启用 TCP Fast Open
- 节流：减少数据量，如文本压缩/图片压缩/切图/少用Cookie/收缩域名/减少重定向
- 缓存：服务器缓存 / CDN / ETag/Last-modified/Cache-Control/Expires等头
- HTTP/2：收缩域名/资源力度尽量小


## 答疑42-42 实验环境 / DHE/ECDHE算法
- DH Diffie-Hellman 迪菲-赫尔曼算法，非对称加密算法，基础是离散对数
- A = G^a % P , B = G^b % P (P=17, G=5)，AB为公钥，ab为私钥，PG公开
- Pre-Master = B^a % P = (G^b % P)^a % P = (G^a % P)^b % P = A^b % P
- static DH 算法中，一方私钥是静态的，不具有前向安全
- ephemeral DH/DHE/EDH 算法，私钥是临时的
- ECDHE ephemeral Elliptic Curve Diffie-Hellman 短暂-椭圆曲线-迪菲-赫尔曼
- 思考题： DHE不能用于数字签名，是因为无法使用公钥验证私钥有效性

## 结束语 做兴趣使然的Hero



