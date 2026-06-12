
使用 `acme.sh` 为 Nginx 颁发 Let's Encrypt 证书的步骤如下：

---

### 1. **安装 `acme.sh`**
   在服务器上安装 `acme.sh`：
   ```bash
   curl https://get.acme.sh | sh -s email=my@example.com
   ```
   安装完成后，重新加载 Shell 环境：
   ```bash
   source ~/.bashrc
   ```
      配置证书申请源 （zerossl letsencrypt buypass ssl.com google）
      建议zerossl，因为letsencrypt越来越不被信任。
    
      acme.sh --set-default-ca --server zerossl
---

### 2. **选择验证方式**
   `acme.sh` 支持多种验证方式，常用的是 **HTTP 验证** 和 **DNS 验证**。

   #### **HTTP 验证**
   - 适用于可以直接通过 HTTP 访问的域名。
   - 确保 Nginx 已配置好，并且可以通过 `http://yourdomain.com/.well-known/acme-challenge/` 访问验证文件。

   #### **DNS 验证**
   - 适用于无法直接通过 HTTP 访问的域名（如通配符域名 `*.example.com`）。
   - 需要配置 DNS API（如 Cloudflare、阿里云等）。

---

### 3. **申请证书**
   #### **使用 HTTP 验证**
   ```bash
   acme.sh --issue -d yourdomain.com -d www.yourdomain.com --webroot /var/www/html
   ```
```
acme.sh --issue -d hk.issalt.dpdns.org --webroot /tmp/html2
```



   - `-d`：指定域名（支持多个域名）。
   - `--webroot`：指定网站根目录，`acme.sh` 会在此目录下创建验证文件。

   #### **使用 DNS 验证**
   以 Cloudflare 为例：
   1. 获取 Cloudflare API Token。
   2. 设置环境变量：
      ```bash
      export CF_Token="your_cloudflare_api_token"
      export CF_Account_ID="your_cloudflare_account_id"
      ```
   3. 申请证书：
      ```bash
      acme.sh --issue --dns dns_cf -d yourdomain.com -d *.yourdomain.com
      ```
      
      ```
      acme.sh --issue --dns -d hk.issalt.dpdns.org --yes-I-know-dns-manual-mode-enough-go-ahead-please
      ```
      
         ```      
      acme.sh --issue --dns -d xiaoming88.ip-ddns.com -d *.xiaoming88.ip-ddns.com --yes-I-know-dns-manual-mode-enough-go-ahead-please
         ```
      
      ```
      acme.sh --renew --dns -d xiaoming88.ip-ddns.com -d *.xiaoming88.ip-ddns.com --yes-I-know-dns-manual-mode-enough-go-ahead-please
      ```
---

### 4. **安装证书到 Nginx**

   申请成功后，证书会保存在 `~/.acme.sh/yourdomain.com/` 目录下。将证书安装到 Nginx 的配置目录：
   ```bash
   acme.sh --install-cert -d yourdomain.com \
   --key-file       /etc/nginx/ssl/yourdomain.com.key  \
   --fullchain-file /etc/nginx/ssl/yourdomain.com.crt \
   --reloadcmd     "systemctl reload nginx"
   ```
   - `--key-file`：指定私钥文件路径。
   - `--fullchain-file`：指定证书文件路径。
   - `--reloadcmd`：指定证书安装后重新加载 Nginx 的命令。

---

### 5. **配置 Nginx**
   编辑 Nginx 配置文件（如 `/etc/nginx/conf.d/yourdomain.com.conf`），添加 SSL 配置：
   ```nginx
   server {
       listen 443 ssl;
       server_name yourdomain.com www.yourdomain.com;

       ssl_certificate /etc/nginx/ssl/yourdomain.com.crt;
       ssl_certificate_key /etc/nginx/ssl/yourdomain.com.key;

       ssl_protocols TLSv1.2 TLSv1.3;
       ssl_ciphers HIGH:!aNULL:!MD5;

       root /var/www/html;
       index index.html;

       location / {
           try_files $uri $uri/ =404;
       }
   }

   server {
       listen 80;
       server_name yourdomain.com www.yourdomain.com;
       return 301 https://$host$request_uri;
   }
   ```
   - 配置 HTTPS 监听端口（443）。
   - 配置 HTTP 到 HTTPS 的重定向（可选）。

---

### 6. **重新加载 Nginx**
   测试 Nginx 配置是否正确：
   ```bash
   nginx -t
   ```
   如果测试通过，重新加载 Nginx：
   ```bash
   systemctl reload nginx
   ```

---

### 7. **自动续期**
   `acme.sh` 会自动设置定时任务（cron job），在证书到期前续期。续期后会自动重新加载 Nginx。

---

### 8. **验证证书**
   访问 `https://yourdomain.com`，确认证书已生效。可以使用工具（如 [SSL Labs](https://www.ssllabs.com/ssltest/)）检查证书配置。

---

### 总结
通过 `acme.sh` 为 Nginx 颁发证书的步骤包括：安装 `acme.sh`、选择验证方式、申请证书、安装证书、配置 Nginx 并重新加载。`acme.sh` 会自动处理证书续期，确保 HTTPS 服务持续可用。