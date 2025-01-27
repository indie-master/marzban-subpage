# Routing

Generate pair of public and private keys:
```
docker exec marzban-marzban-1 xray x25519
```

Generate uuid:
```
uuidgen
```

Generate short id:
```
openssl rand -hex 8
```

### Generate JSON fragment from client V2BOX as example:
```
{
  "log": {},
  "inbounds": [
    {
      "listen": "127.0.0.1",
      "port": 58858,
      "tag": "socks",
      "sniffing": {
        "enabled": true,
        "domainsExcluded": [
          "courier.push.apple.com"
        ],
        "destOverride": [
          "tls",
          "http",
          "quic"
        ]
      },
      "protocol": "shadowsocks",
      "settings": {
        "network": "tcp,udp",
        "level": 8,
        "ota": true,
        "method": "chacha20-ietf-poly1305",
        "password": "123456",
        "udp": false
      }
    },
    {
      "listen": "127.0.0.1",
      "port": 1087,
      "tag": "directSocks",
      "protocol": "socks",
      "settings": {
        "userLevel": 8,
        "udp": true,
        "auth": "noauth"
      }
    },
    {
      "listen": "[::1]",
      "port": 58859,
      "tag": "api",
      "protocol": "dokodemo-door",
      "settings": {
        "address": "[::1]"
      }
    },
    {
      "listen": "127.0.0.1",
      "port": 58860,
      "tag": "inDns",
      "protocol": "dokodemo-door",
      "settings": {
        "userLevel": 0,
        "port": 53,
        "address": "8.8.8.8",
        "network": "tcp,udp",
        "timeout": 10
      }
    }
  ],
  "outbounds": [
    {
      "mux": {
        "xudpProxyUDP443": "allow",
        "xudpConcurrency": 128,
        "enabled": false,
        "concurrency": 50
      },
      "tag": "proxy",
      "protocol": "vless",
      "streamSettings": {
        "realitySettings": {
          "fingerprint": "chrome",
          "spiderX": "",
          "show": false,
          "shortId": "807e4f530af1ed68",
          "serverName": "destname.ru",
          "publicKey": "publicKey_RU_server"
        },
        "tcpSettings": {
          "header": {
            "type": "none"
          }
        },
        "security": "reality",
        "network": "tcp"
      },
      "settings": {
        "vnext": [
          {
            "users": [
              {
                "encryption": "none",
                "level": 8,
                "flow": "xtls-rprx-vision",
                "id": "id_RU_server",
                "email": ""
              }
            ],
            "address": "address_RU_server",
            "port": 443
          }
        ]
      }
    },
    {
      "tag": "fragment",
      "protocol": "freedom",
      "settings": {
        "userLevel": 8,
        "fragment": {
          "interval": "10-100",
          "packets": "tlshello",
          "length": "80-250"
        }
      },
      "streamSettings": {
        "sockopt": {
          "tcpNoDelay": true
        }
      }
    }
  ],
  "api": {
    "services": [
      "StatsService"
    ],
    "tag": "api"
  },
  "dns": {
    "disableFallbackIfMatch": true,
    "tag": "dnsQuery",
    "disableFallback": true,
    "queryStrategy": "UseIP",
    "servers": [
      {
        "skipFallback": false,
        "address": "8.8.8.8"
      }
    ],
    "disableCache": true
  },
  "stats": {},
  "routing": {
    "rules": [
      {
        "outboundTag": "direct",
        "type": "field",
        "domain": [
          "geosite:private",
          "geosite:apple"
        ]
      },
      {
        "outboundTag": "api",
        "type": "field",
        "inboundTag": [
          "api"
        ]
      },
      {
        "outboundTag": "outDns",
        "type": "field",
        "inboundTag": [
          "inDns"
        ]
      },
      {
        "outboundTag": "proxy",
        "type": "field",
        "inboundTag": [
          "dnsQuery"
        ]
      },
      {
        "outboundTag": "direct",
        "type": "field",
        "inboundTag": [
          "directSocks"
        ]
      }
    ],
    "balancers": [],
    "domainStrategy": "AsIs"
  },
  "policy": {
    "levels": {
      "8": {
        "uplinkOnly": 1,
        "statsUserDownlink": false,
        "handshake": 4,
        "connIdle": 30,
        "statsUserUplink": false,
        "bufferSize": 0,
        "downlinkOnly": 1
      }
    },
    "system": {
      "statsOutboundUplink": true,

Andrej Master, [22.01.2025 15:52]
"statsInboundUplink": true,
      "statsInboundDownlink": true,
      "statsOutboundDownlink": true
    }
  },
  "transport": {}
}
```
