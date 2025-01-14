介绍：

v2ray 或 Xray 前置（监听 443 端口），利用 vless+tcp+tls 或 vless+tcp+xtls 强大的回落/分流 WebSocket（WS）特性与 caddy 为 H2C 与 gRPC 提供反向代理、为 naiveproxy 与 trojan-go 提供正向代理，实现除 v2ray 或 Xray 的 mKCP 应用外共用 443 端口。其应用如下：

1、E=vless+tcp+tls/xtls（回落/分流配置，TLS/XTLS由自己提供及处理。）

2、B=vmess+ws+tls（TLS由vless+tcp+tls/xtls提供及处理，不需配置。）

3、D=vless+h2c+tls（TLS由vless+tcp+tls/xtls提供及处理，不需配置。）

4、G=shadowsocks+grpc+tls（TLS由vless+tcp+tls/xtls提供及处理，不需配置。）

5、A=vless+kcp+seed

6、naiveproxy（基于caddy的改进版forwardproxy插件实现，TLS由vless+tcp+tls/xtls提供及处理，不需配置。）

7、trojan-go（基于caddy的caddy-trojan插件实现，TLS由vless+tcp+tls/xtls提供及处理，不需配置。）

注意：

1、Xray 或 v2ray 的监听地址不支持 shadowsocks（SS） 协议使用 UDS 监听。

2、Xray 版本不小于 v1.4.0 或 v2ray 版本不小于 v4.36.2 才支持 gRPC 传输方式。

3、caddy 版本不小于 v2.2.0 才支持 H2C proxy，即支持 Xray 或 v2ray 的 H2C（gRPC） 反向代理。caddy 版本不小于 v2.6.0 才支持 H2C proxy 的 UDS 转发。

4、caddy 支持 HTTP/1.1 server 与 H2C server 共用一个端口或一个进程。

5、使用本人 Releases 中编译好的 caddy 文件，可同时支持 H2C server、H2C proxy、trojan-go、naiveproxy 及接收 PROXY protocol 等应用。

6、本示例中 naiveproxy 仅支持 HTTP/2 代理应用，即 HTTPS 协议传输。

7、本示例中 trojan-go 兼容原版 trojan-go，继承了其服务端核心特色：支持 trojan 应用与 trojan-go 的 WebSocket 应用共存；支持 CDN 流量中转(基于 WebSocket over TLS)。

8、Xray 所需 SSL/TLS 证书推荐使用 caddy 自动申请，配合 Xray 支持自动热重载 SSL/TLS 证书，可实现 Xray 所需 SSL/TLS 证书更新全自动化。

9、不要使用第三方 ACME 客户端在当前服务器上以 HTTP-01 或 TLS-ALPN-01 验证方式申请与更新 SSL/TLS 证书，因 HTTP-01 或 TLS-ALPN-01 验证方式申请与更新 SSL/TLS 证书需监听 80 或 443 端口，从而与当前应用端口冲突。

10、配置1：使用 Local Loopback 连接，且启用了 PROXY protocol。配置2：使用 UDS 连接（对应 shadowsocks+grpc+tls 除外），且启用了 PROXY protocol。

11、若有实际网站服务推荐采用 [v2ray(E+B+D+G+A)+caddy(N+T)+nginx\haproxy](https://github.com/lxhao61/integrated-examples/tree/main/v2ray(E%2BB%2BD%2BG%2BA)%2Bcaddy(N%2BT)%2Bnginx%5Chaproxy) 示例，平衡兼顾各应用。
