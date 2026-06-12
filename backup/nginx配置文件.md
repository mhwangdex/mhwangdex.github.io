# NGINX 配置文件详解

NGINX 的配置文件通常位于 `/etc/nginx/nginx.conf`，它采用模块化的结构，由多个指令块组成。以下是 NGINX 配置文件的主要组成部分和常见配置示例：

## 基本结构

```nginx
# 全局块 - 配置影响nginx全局的指令
user nginx;                      # 运行nginx的用户
worker_processes auto;           # 工作进程数，通常设置为CPU核心数
error_log /var/log/nginx/error.log; # 错误日志路径
pid /run/nginx.pid;              # PID文件位置

# Events块 - 配置影响nginx服务器或与用户的网络连接
events {
    worker_connections 1024;     # 每个工作进程的最大连接数
    use epoll;                   # 使用epoll模型(高效)
    multi_accept on;             # 同时接受多个新连接
}

# HTTP块 - 配置HTTP服务器
http {
    # 包含MIME类型定义
    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # 日志格式
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                    '$status $body_bytes_sent "$http_referer" '
                    '"$http_user_agent" "$http_x_forwarded_for"';

    access_log /var/log/nginx/access.log main; # 访问日志

    sendfile on;                 # 启用高效文件传输
    tcp_nopush on;               # 优化数据包发送
    keepalive_timeout 65;        # 保持连接超时时间
    types_hash_max_size 2048;    # 类型哈希表大小

    # 包含其他配置文件
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

## 常见服务器配置示例

### 1. 基本HTTP服务器

```nginx
server {
    listen 80;                   # 监听端口
    server_name example.com www.example.com; # 域名

    root /var/www/html;          # 网站根目录
    index index.html index.htm;   # 默认索引文件

    location / {
        try_files $uri $uri/ =404; # 尝试寻找文件或目录，否则404
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }
}
```

### 2. HTTPS服务器配置

```nginx
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256...';
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    root /var/www/html;
    index index.html;

    # 强制HTTP跳转到HTTPS
    if ($scheme = http) {
        return 301 https://$server_name$request_uri;
    }
}
```

### 3. 负载均衡配置

```nginx
upstream backend {
    server backend1.example.com weight=5;
    server backend2.example.com;
    server backend3.example.com;
    server backup.example.com backup;
}

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

### 4. 反向代理配置

```nginx
server {
    listen 80;
    server_name app.example.com;

    location / {
        proxy_pass http://localhost:3000; # 代理到本地3000端口
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

## 常用指令说明

1. **location 匹配规则**:
   - `= /path` - 精确匹配
   - `^~ /path` - 前缀匹配(不检查正则)
   - `~ pattern` - 区分大小写的正则匹配
   - `~* pattern` - 不区分大小写的正则匹配
   - `/path` - 普通前缀匹配

2. **日志相关**:
   - `access_log` - 访问日志
   - `error_log` - 错误日志
   - `rewrite_log` - 重写日志(调试用)

3. **性能优化**:
   - `gzip on` - 启用Gzip压缩
   - `client_max_body_size 20m` - 最大上传文件大小
   - `keepalive_timeout` - 保持连接时间

4. **安全相关**:
   - `add_header X-Frame-Options SAMEORIGIN` - 防止点击劫持
   - `add_header X-Content-Type-Options nosniff` - 防止MIME类型嗅探
   - `add_header X-XSS-Protection "1; mode=block"` - XSS保护

## 配置文件检查与重载

在修改配置文件后，应该先检查语法是否正确：

```bash
sudo nginx -t
```

如果语法正确，重新加载配置：

```bash
sudo nginx -s reload
```

以上是NGINX配置文件的基本结构和常见配置示例，实际使用时需要根据具体需求进行调整。