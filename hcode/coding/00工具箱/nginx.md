

### 原来 error_log 和 access_log 是两个完全不同的配置

- 来源的不同
    - error_log指令包含在Nginx核心模块中
    - access_log属于Nginx的ngx_http_log_module模块

- 关闭的不同
    - error_log /dev/null;
    - access_log off;

- 配置的不同
    - error_log location [debug | info | notice | warn | error | crit | alert | emerg]
    - access_log location format（log_format自定义）;

### 展示文件索引
```
location下
autoindex on;
```