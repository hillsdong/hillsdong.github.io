```
{
  "log": {
    "error": "",
    "loglevel": "info",
    "access": ""
  },
  "inbounds": [
    {
      "listen": "127.0.0.1",
      "protocol": "socks",
      "settings": {
        "ip": "",
        "userLevel": 0,
        "timeout": 360,
        "udp": false,
        "auth": "noauth"
      },
      "port": "1080"
    },
    {
      "listen": "0.0.0.0",
      "protocol": "http",
      "settings": {
        "timeout": 360
      },
      "port": "1087"
    }
  ],
  "outbounds": [
    {
      "mux": {
        "enabled": false,
        "concurrency": 8
      },
      "protocol": "vmess",
      "streamSettings": {
        "wsSettings": {
          "path": "/ray",
          "headers": {
            "host": "cdn2.klib.cn"
          }
        },
        "tlsSettings": {
          "serverName": "cdn2.klib.cn",
          "allowInsecure": false
        },
        "security": "tls",
        "network": "ws"
      },
      "tag": "agentout",
      "settings": {
        "vnext": [
          {
            "address": "cdn2.klib.cn",
            "users": [
              {
                "id": "971f5aa7-92a3-4627-b80e-4350bf45722e",
                "alterId": 64,
                "level": 1,
                "security": "auto"
              }
            ],
            "port": 443
          }
        ]
      }
    },
    {
      "tag": "direct",
      "protocol": "freedom",
      "settings": {
        "domainStrategy": "AsIs",
        "redirect": "",
        "userLevel": 0
      }
    },
    {
      "tag": "blockout",
      "protocol": "blackhole",
      "settings": {
        "response": {
          "type": "none"
        }
      }
    }
  ],
  "dns": {
    "servers": [
      ""
    ]
  },
  "routing": {
    "strategy": "rules",
    "settings": {
      "domainStrategy": "IPIfNonMatch",
      "rules": [
        {
          "outboundTag": "direct",
          "type": "field",
          "ip": [
            "geoip:cn",
            "geoip:private"
          ],
          "domain": [
            "geosite:cn",
            "geosite:speedtest"
          ]
        }
      ]
    }
  },
  "transport": {}
}
```


```
{
  "log": {
    "error": "",
    "loglevel": "info",
    "access": ""
  },
  "inbounds": [
    {
      "listen": "127.0.0.1",
      "protocol": "socks",
      "settings": {
        "ip": "",
        "userLevel": 0,
        "timeout": 360,
        "udp": false,
        "auth": "noauth"
      },
      "port": "1080"
    },
    {
      "listen": "0.0.0.0",
      "protocol": "http",
      "settings": {
        "timeout": 360
      },
      "port": "1087"
    }
  ],
  "outbounds": [
    {
      "mux": {
        "enabled": false,
        "concurrency": 8
      },
      "protocol": "vmess",
      "streamSettings": {
        "wsSettings": {
          "path": "/ray",
          "headers": {
            "host": "cdn2.klib.cn"
          }
        },
        "tlsSettings": {
          "serverName": "cdn2.klib.cn",
          "allowInsecure": false
        },
        "security": "tls",
        "network": "ws"
      },
      "tag": "agentout",
      "settings": {
        "vnext": [
          {
            "address": "cdn2.klib.cn",
            "users": [
              {
                "id": "971f5aa7-92a3-4627-b80e-4350bf45722e",
                "alterId": 64,
                "level": 1,
                "security": "auto"
              }
            ],
            "port": 443
          }
        ]
      }
    }
  ],
  "dns": {},
  "routing": {},
  "transport": {}
}
```