介绍：

利用 nginx 支持 SNI 分流特性，对 vless+tcp+tls 或 vless+tcp+xtls、trojan-go 或 trojan、HTTP/2 server 进行 SNI 分流（四层转发），实现除 v2ray 或 Xray 的 mKCP 应用外共用 443 端口。其中 vless+tcp+tls 或 vless+tcp+xtls 为 WebSocket（WS） 提供分流转发；nginx 同时为 vless+tcp+tls 或 vless+tcp+xtls 与 trojan-go 或 trojan 提供回落服务，为 v2ray 或 Xray 的 gRPC 提供反向代理。包括应用如下：

1、E=vless+tcp+tls/xtls（回落/分流配置，TLS/XTLS由自己提供及处理。）

2、B=vmess+ws+tls（TLS由vless+tcp+tls/xtls提供及处理，不需配置。）

3、G=shadowsocks+grpc+tls（TLS由nginx提供及处理，不需配置。）

4、A=vless+kcp+seed

5、trojan-go或trojan（TLS由自己提供及处理。）

注意：

1、nginx 支持 SNI 分流，需要 nginx 包含 stream_core_module 和 stream_ssl_preread_module 模块。

2、采用 SNI 方式实现共用 443 端口，支持各自特色应用，但需多个域名来标记分流。

3、Xray 或 v2ray 的监听地址不支持 shadowsocks（SS） 协议使用 UDS 监听。

4、Xray 版本不小于 v1.4.0 或 v2ray 版本不小于 v4.36.2 才支持 gRPC 传输方式。

5、因 trojan-go 或 trojan 不支持 UDS，故 nginx SNI 分流 trojan-go 或 trojan 使用 Local Loopback 连接；故与 trojan-go 或 trojan 共用 WEB 服务的 vless+tcp+xtls 或 vless+tcp+tls 回落也不能使用 UDS 连接，即全部回落使用 Local Loopback 连接。

6、因 trojan-go 或 trojan 不支持 PROXY protocol，故 nginx SNI 分流启用 PROXY protocol 须特殊处理（具体见配置示例）；故与 trojan-go 或 trojan 共用 WEB 服务的 vless+tcp+xtls 或 vless+tcp+tls 回落也不能启用 PROXY protocol，即全部回落不启用 PROXY protocol。

7、trojan-go 完全兼容 trojan，服务端还有自己的特色：支持 trojan 应用与自己的 WebSocket 应用共存；支持 CDN 流量中转(基于 WebSocket over TLS)；支持使用 AEAD 对 trojan 协议流量进行二次加密(基于 Shadowsocks AEAD)。

8、nginx 支持 HTTP/2 server、gRPC proxy 及 H2C server，需要 nginx 包含 http_v2_module 和 http_ssl_module 模块。

9、nginx 支持 TLSv1.3，需要包含版本不小于 1.1.1 的 OpenSSL 软件库包和 http_ssl_module 模块。

10、nginx 支持 HTTP 功能块接收 PROXY protocol，需要 nginx 包含 http_realip_module 模块。

11、nginx 支持 H2C server，但不支持 HTTP/1.1 server 与 H2C server 共用一个端口或一个进程；故回落分成 http/1.1 回落与 h2 回落分别对应 nginx 的 HTTP/1.1 server 与 H2C server。

12、因 trojan-go 目前不支持 http/1.1 回落与 h2 回落分开，故 trojan-go 开启 Websocket 支持后只选 http/1.1 连接及 http/1.1 回落。

13、不要使用 ACME 客户端在当前服务器上以 HTTP-01 或 TLS-ALPN-01 验证方式申请与更新 SSL/TLS 证书，因 HTTP-01 或 TLS-ALPN-01 验证方式申请与更新 SSL/TLS 证书需监听 80 或 443 端口，从而与当前应用端口冲突。

14、配置1：使用 Local Loopback 连接，且启用了 PROXY protocol（全部回落除外）。配置2：使用混合连接（能 UDS 连接的全部 UDS 连接），且启用了 PROXY protocol（全部回落除外）。
