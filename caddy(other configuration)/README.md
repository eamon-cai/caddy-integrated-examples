一、回落后由 caddy 分流到不同网站的配置方法

此方法解决 Xray 或 v2ray 前置监听443后，不影响先前 caddy 前置时不同域名访问不同网站问题。见 shunt_caddy.json 或 shunt_Caddyfile 配置。

注意：

1、若不同域名没有使用通配符证书，那么还需要在 Xray 或 v2ray 中并列配置多个域名对应的证书及密钥。

2、此回落到不同网站是 Xray 或 v2ray 解除 TLS 后 caddy 进行的 host（域名）分流。

3、也可以用 caddy SNI 或 haproxy SNI 等分流来解决问题（不同方法，达到相同效果。）。caddy SNI 配置示例见如下介绍。haproxy SNI 配置示例参考用 haproxy SNI 分流的 haproxy 配置示例。

二、caddy SNI 分流的配置方法

此方法也可以解决 Xray 或 v2ray 应用与网站应用（原网站不想做回落网站，或 nginx、caddy 等有多个网站应用。）共用443端口问题。

注意：

1、caddy 加 caddy-l4 插件定制编译的才可以实现 SNI 分流，目前仅支持使用 json 配置（不支持 Caddyfile 配置）。

2、1_SNI_caddy.json 采用 Local Loopback 连接，实现 caddy SNI 的端口分流。

3、2_SNI_caddy.json 采用 UDS 连接，实现 caddy SNI 的进程分流。

4、相关示例已配置 caddy SNI 分流共用端口，此配置仅备份及参考等。

三、caddy 以 DNS-01 验证方式申请 SSL/TLS 证书的配置方法

1、以 DNS-01 验证方式申请 SSL/TLS 证书，可申请通配符证书。

2、提供了 cloudflare、dnspodcn（dnspod中国版插件）、duckdns 三种最常见插件配置示例，其它插件配置请参考官方资料及 cloudflare 或 duckdns 配置示例。

注意：

1、示例为通配符域名的 SSL/TLS 证书申请，可通配符域名、普通域名（含根域名）单一或组合申请（证书是各自分开的）。

2、申请免费 SSL/TLS 证书的域名不要超过五个，否则影响 SSL/TLS 证书的更新。

3、caddy 以 DNS-01 验证方式申请 SSL/TLS 证书，必须带对应 DNS API 插件。dnspod 解析分 dnspod.com（国际版）与 dnspod.cn（中国版），故两者插件不通用，必须对应各自 dnspod 解析使用。

4、cloudflare 已不支持 freenom 提供的免费域名以 DNS-01 验证方式申请 SSL/TLS 证书了。可以参考 duckdns 配置示例间接（挑战委托模式）实现 cloudflare 解析的 freenom 免费域名以 DNS-01 验证方式申请 SSL/TLS 证书。

5、挑战委托模式最早由 duckdns 插件专属支持，后 caddy 通用支持了（参数有调整）。其它插件不推荐配置此应用：A、其它插件使用此模式需要两个根域名，其中一个域名仅用它二级域名来中间关联，极大浪费。 B、目前其它插件 Caddyfile 配置挑战委托模式很不方便，无对应全局配置参数。

6、acmeh 或 zerossl 申请成功后证书及密钥所在路径及目录。  
1）、acme 申请的普通证书及密钥在 “/home/tls/certificates/acme-v02.api.letsencrypt.org-directory/xx.yy” 目录中。/home/tls 为设置存放证书及密钥的基本路径。xx.yy 为域名，根据自己域名变化。  
2）、acme 申请的通配符证书及密钥在 “/home/tls/certificates/acme-v02.api.letsencrypt.org-directory/wildcard_.xx.yy” 目录中。/home/tls 为设置存放证书及密钥的基本路径。xx.yy 为域名，根据自己域名变化。  
3）、zerossl 申请的普通证书及密钥在 “/home/tls/certificates/acme.zerossl.com-v2-dv90/xx.yy” 目录中。/home/tls 为设置存放证书及密钥的基本路径。xx.yy 为域名，根据自己域名变化。  
4）、zerossl 申请的通配符证书及密钥在 “/home/tls/certificates/acme.zerossl.com-v2-dv90/wildcard_.xx.yy” 目录中。/home/tls 为设置存放证书及密钥的基本路径。xx.yy 为域名，根据自己域名变化。

7、其它应用使用 caddy 自动申请的 SSL/TLS 证书，若自己不支持自动重载 SSL/TLS 证书，证书到期更新（证书有效期90天，默认60天后开始更新。）后需手动重启自己来重新加载更新后的 SSL/TLS 证书。

四、caddy DDNS 客户端配置方法

caddy 使用 caddy-dynamicdns 插件与 caddy-dns 插件（仅含alidns、cloudflare、dnspod、dnspodcn、duckdns五个常用caddy-dns插件）实现 DDNS 客户端应用。基本配置见 DDNS_caddy.json 或 DDNS_Caddyfile 配置示例，详细配置见 caddy-dynamicdns 资源。

五、caddy 网盘应用配置方法

以 WebDAV 协议及文件服务应用打造不同的网盘应用（服务端）,详见 webdav_caddy.json 或 webdav_Caddyfile 配置示例。
