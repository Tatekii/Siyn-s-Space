```nginx
# ----------------------------------------------------------------------
# 全局配置 (main context)
# 这些指令影响 Nginx 服务器的整体行为
# ----------------------------------------------------------------------

# 定义 Nginx worker 进程运行的用户和组。
# 通常使用一个非特权用户（如 'nginx' 或 'www-data'）以提高安全性。
user nginx;

# 定义 worker 进程的数量。
# 'auto' 会让 Nginx 根据 CPU 核心数自动调整，以最大化 CPU 利用率。
worker_processes auto;

# 错误日志路径和级别。
# 级别可以是：debug, info, notice, warn, error, crit, alert, emerg (从详细到紧急)
# 'warn' 级别会记录警告及以上级别的错误。
error_log /var/log/nginx/error.log warn;

# Master 进程的 PID (进程ID) 文件路径。
# Nginx 会将 Master 进程的 PID 写入到这个文件中。
pid /var/run/nginx/nginx.pid;


# ----------------------------------------------------------------------
# 事件配置 (events context)
# 配置 Nginx worker 进程如何处理连接
# ----------------------------------------------------------------------
events {
    # 每个 worker 进程能够同时打开的最大连接数。
    # 这个值受限于系统文件描述符的限制 (ulimit -n)，通常需要大于 1024。
    # 总的连接数 = worker_processes * worker_connections。
    worker_connections 1024;

    # (可选) 允许 worker 进程一次性接受所有新连接，而不是逐个接受。
    # 可以在高并发场景下提高效率。
    # multi_accept on;

    # (可选) 指定 Nginx 使用的连接处理方法。
    # 在 Linux 上，通常推荐使用 'epoll'。其他可选值包括 'kqueue', 'devpoll', 'select', 'poll'。
    # Nginx 通常会根据操作系统自动选择最佳方法。
    # use epoll;
}

# ----------------------------------------------------------------------
# HTTP 服务器配置 (http context)
# 包含所有 HTTP 相关的配置，如 MIME 类型、日志、文件传输优化、Gzip 压缩等。
# ----------------------------------------------------------------------
http {
    # 包含 MIME 类型映射文件。
    # 它定义了文件扩展名与对应的 Content-Type (媒体类型) 之间的映射关系，例如 .html -> text/html。
    include /etc/nginx/mime.types;

    # 当文件的 MIME 类型无法识别时使用的默认 Content-Type。
    default_type application/octet-stream;

    # HTTP 访问日志配置。
    # 'main' 是 Nginx 内置的日志格式，记录了请求的常见信息 (IP, 时间, 请求行, 状态码, 字节数等)。
    # 也可以自定义日志格式。
    access_log /var/log/nginx/access.log main;

    # ------------------- 文件传输优化 -------------------
    # 启用 sendfile 模式。
    # 允许 Nginx 直接在内核中进行数据传输，避免了用户空间和内核空间之间的数据拷贝，
    # 显著提高静态文件服务性能。
    sendfile on;

    # (可选) 在 sendfile 启用时，将 HTTP 响应头和文件内容一起发送。
    # 有助于减少网络包的数量，但可能会增加首次发送响应的延迟。
    # tcp_nopush on;

    # (可选) 禁用 Nagle 算法。
    # 允许实时发送小数据包，适用于短连接或需要低延迟的场景 (如 WebSockets)。
    # tcp_nodelay on;

    # ------------------- 连接超时 -------------------
    # 客户端与服务器保持长连接的超时时间 (秒)。
    # 在这个时间内，客户端可以发送多个请求而无需重新建立 TCP 连接，减少了连接建立的开销。
    keepalive_timeout 65;

    # (可选) 客户端发送请求头的超时时间 (秒)。
    # 如果客户端在指定时间内没有发送完整的请求头，Nginx 会关闭连接。
    # client_header_timeout 10;

    # (可选) 客户端发送请求体的超时时间 (秒)。
    # 如果客户端在指定时间内没有发送完整的请求体 (如文件上传)，Nginx 会关闭连接。
    # client_body_timeout 10;

    # ------------------- Gzip 压缩 -------------------
    # 启用 Gzip 压缩。
    # 可以显著减小传输数据的大小，提高页面加载速度。
    gzip on;

    # 允许代理服务器修改响应头，以指示内容已被 gzip 压缩。
    gzip_vary on;

    # 允许对被代理的请求进行 gzip 压缩 (即 Nginx 作为反向代理时)。
    # 'any' 表示对所有代理请求都进行压缩。
    gzip_proxied any;

    # Gzip 压缩级别 (1-9)。
    # 数字越大压缩率越高，但 CPU 消耗也越大。通常 6 是一个平衡点。
    gzip_comp_level 6;

    # 压缩缓冲区的大小和数量。
    gzip_buffers 16 8k;

    # 兼容的 HTTP 协议版本。
    gzip_http_version 1.1;

    # 指定要进行 gzip 压缩的 MIME 类型。
    # 确保只对文本内容 (如 HTML, CSS, JS, JSON) 进行压缩，因为图片等二进制文件通常已经压缩过了。
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # (可选) 禁用特定 User-Agent 的 Gzip 压缩，例如旧版 IE 浏览器。
    # gzip_disable "MSIE [1-6]\.";

    # ------------------- 客户端请求体大小限制 -------------------
    # 允许客户端上传的最大文件大小。
    # 如果请求体超过此限制，Nginx 将返回 413 Request Entity Too Large 错误。
    client_max_body_size 100m;

    # ------------------- 虚拟主机配置引入 -------------------
    # 引入其他配置文件。
    # 通常将每个虚拟主机的配置放在独立的 .conf 文件中，并放在 conf.d 或 sites-enabled 目录下，
    # 这样可以更好地组织和管理配置。
    # include /etc/nginx/conf.d/*.conf;
    # (某些发行版可能使用 sites-enabled 目录)
    # include /etc/nginx/sites-enabled/*;
}

# ----------------------------------------------------------------------
# 虚拟主机配置 (server context) - 示例：静态文件服务/SPA 应用
# 每个 server 块定义了一个虚拟主机，监听特定的域名和端口。
# ----------------------------------------------------------------------

server {
    # 监听 80 端口，用于 HTTP 请求。
    listen 80;

    # 匹配请求的域名。
    # 当请求的 Host 头是 example.com 或 www.example.com 时，这个 server 块会处理请求。
    server_name example.com www.example.com;

    # ------------------- SSL/TLS 配置 (HTTPS) -------------------
    # 如果需要启用 HTTPS，需要取消以下行的注释，并替换证书路径。
    # listen 443 ssl;
    # ssl_certificate /etc/nginx/certs/example.com.crt; # 你的 SSL 证书文件路径
    # ssl_certificate_key /etc/nginx/certs/example.com.key; # 你的 SSL 证书私钥文件路径
    # ssl_protocols TLSv1.2 TLSv1.3; # 启用 TLS 协议版本 (推荐只用高版本)
    # ssl_ciphers 'TLS_AES_256_GCM_SHA384:TLS_CHACHA20_POLY1305_SHA256:TLS_AES_128_GCM_SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256'; # 推荐的加密套件
    # ssl_prefer_server_ciphers on; # 服务器优先选择加密套件

    # 网站的根目录。
    # Nginx 会在这个目录下查找请求的文件。
    root /usr/share/nginx/html;

    # 默认首页文件。
    # 当请求 URI 对应一个目录时，Nginx 会尝试查找这些文件作为索引文件。
    index index.html index.htm;

    # ------------------- 错误页面 -------------------
    # 自定义 404 错误页面。
    # 当文件或页面找不到时，Nginx 会返回 /404.html 的内容。
    error_page 404 /404.html;

    # ------------------- URL 路径匹配 (location context) -------------------
    # 匹配所有请求 (优先级最低)。
    # 这是最常用的 location 块，用于处理网站的大部分请求。
    location / {
        # 尝试查找文件或目录。
        # $uri: 尝试查找与请求 URI 对应的文件 (如 /about.html)。
        # $uri/: 尝试查找与请求 URI 对应的目录下的默认索引文件 (如 /blog/ -> /blog/index.html)。
        # /index.html: 如果前两者都找不到，则内部重定向到 /index.html。这对于单页应用 (SPA) 非常有用，
        # 确保所有路由都由前端框架处理。
        try_files $uri $uri/ /index.html;
    }

    # ------------------- 静态文件服务 -------------------
    # 匹配以 /static/ 开头的请求。
    # 通常用于服务图片、CSS、JS 等静态资源。
    location /static/ {
        # 静态文件的实际存放路径。
        root /path/to/your/static/files; # 替换为你的实际路径

        # 设置静态文件的缓存过期时间。
        # 30d 表示缓存 30 天，可以大大减少浏览器重复下载的次数。
        expires 30d;

        # 添加 Cache-Control 头部，告诉浏览器和代理如何缓存资源。
        add_header Cache-Control "public, no-transform";
    }

    # ------------------- PHP FastCGI 处理 -------------------
    # 使用正则表达式匹配所有以 .php 结尾的请求。
    # 通常用于将 PHP 请求转发给 PHP-FPM (FastCGI 进程管理器) 处理。
    location ~ \.php$ {
        # 将请求传递给 FastCGI 服务器 (PHP-FPM)。
        # 可以是 Unix socket 路径 (更高效) 或 TCP 地址 (如 127.0.0.1:9000)。
        fastcgi_pass unix:/run/php/php7.4-fpm.sock; # 替换为你的 PHP-FPM socket 或地址
        # fastcgi_pass 127.0.0.1:9000;

        # 指定 FastCGI 索引文件。
        fastcgi_index index.php;

        # 包含 FastCGI 相关的参数，如 SCRIPT_FILENAME 等。
        # 这些参数告诉 PHP-FPM 如何处理请求。
        include fastcgi_params;

        # (可选) 确保 SCRIPT_FILENAME 正确指向 PHP 文件。
        # fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    # ------------------- 反向代理到后端 API 服务 -------------------
    # 匹配所有以 /api/ 开头的请求。
    # 将这些请求转发到后端 API 服务器。
    location /api/ {
        # 代理到后端服务的地址。
        proxy_pass http://localhost:8080; # 替换为你的后端 API 服务地址

        # 转发 Host 头到后端服务器。
        # 保持客户端请求的原始 Host 头，而不是 Nginx 服务器的 Host 头。
        proxy_set_header Host $host;

        # 转发真实的客户端 IP 地址。
        # 如果不设置，后端服务器可能会看到 Nginx 的 IP 地址而不是用户的 IP。
        proxy_set_header X-Real-IP $remote_addr;

        # 转发代理链中所有客户端 IP 地址。
        # 如果请求经过多个代理，这个头会包含所有中间代理的 IP。
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        # 转发请求的协议 (HTTP 或 HTTPS)。
        proxy_set_header X-Forwarded-Proto $scheme;

        # 关闭 Nginx 的重定向。
        # 如果后端返回 301/302，Nginx 不会修改 Location 头，由客户端直接处理。
        proxy_redirect off;

        # (可选) 对后端响应进行缓存。
        # 例如，对 200 和 302 响应缓存 10 分钟。
        # proxy_cache_valid 200 302 10m;
        # (可选) 定义缓存的键，用于区分不同的缓存条目。
        # proxy_cache_key "$host$request_uri$is_args$args";
        # (需要先在 http 块中配置 proxy_cache_path)
    }

    # ------------------- 精确匹配 Favicon -------------------
    # 精确匹配 /favicon.ico 请求。
    location = /favicon.ico {
        # 不记录此请求的 404 错误。
        log_not_found off;
        # 不记录此请求的访问日志。
        access_log off;
    }
}


# ----------------------------------------------------------------------
# 上游服务器组 (upstream context)
# 定义一组后端服务器，通常与负载均衡结合使用。
# ----------------------------------------------------------------------

# 定义一个名为 'backend_servers' 的上游服务器组。
# 当你在 proxy_pass 指令中使用这个组名时，Nginx 会根据负载均衡算法选择一个后端服务器。
upstream backend_servers {
    # ------------------- 负载均衡算法 (默认是轮询) -------------------
    # (默认) 轮询算法：按顺序将请求分发给组中的服务器。
    # round_robin;

    # 最少连接算法：将请求发送给当前活动连接数最少的服务器。
    # least_conn;

    # IP 哈希算法：根据客户端 IP 地址的哈希值将请求分配到特定的服务器。
    # 这确保了来自同一个客户端的请求总是被发送到同一个后端服务器，适用于需要会话粘性（session stickiness）的场景。
    # ip_hash;

    # (需要第三方模块) Fair 算法：根据服务器的响应时间（或其他指标）进行加权轮询。
    # fair;

    # 定义组中的后端服务器。
    # 可以指定 IP 地址或域名，以及端口。
    # weight=3: 分配权重为 3，表示它将比其他服务器获得三倍的请求。
    server 192.168.1.100:8080 weight=3;
    server 192.168.1.101:8080;
    # backup: 标记为备份服务器，只有当所有主服务器都不可用时才会被使用。
    server 192.168.1.102:8080 backup;
    # down: 标记为永久下线，Nginx 不会向其发送请求。
    server 192.168.1.103:8080 down;

    # (可选) 与上游服务器保持连接的池大小。
    # 减少与后端建立新连接的开销，适用于后端支持长连接的情况。
    keepalive 64;
}

# (示例) 使用上游服务器组的虚拟主机
server {
    listen 80;
    server_name api.example.com; # 监听用于 API 请求的域名

    location / {
        # 将所有请求转发到 'backend_servers' 上游服务器组。
        # Nginx 会根据上游组的负载均衡算法选择一个后端服务器。
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_redirect off;
        # ...其他 proxy 相关配置
    }
}
```