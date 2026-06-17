# NewAPI + 营销主页 完整部署方案

## 问题分析

你遇到的问题是：
1. **只有首页** - 这是正常的，营销页本身就是单页面
2. **顶部功能栏被盖住** - NewAPI 的导航栏和营销页导航栏重叠了

## 原因

参考网站 https://9527code.com/ 的架构是：
- 首页 `/` → 营销主页（独立页面）
- 控制台 `/console` → NewAPI 完整系统（另一个完整应用）

这两个是**分离**的，不会互相干扰。

---

## 🎯 推荐部署方案：Nginx 反向代理分离

### 架构图

```
用户访问 api.tang74.top
         ↓
   Nginx (80/443)
         ↓
    ┌────┴────┐
    ↓         ↓
   /        /console, /api, /token
营销主页      NewAPI (3000端口)
```

### 完整 Nginx 配置

创建文件：`/etc/nginx/sites-available/newapi`

```nginx
server {
    listen 80;
    server_name api.tang74.top;
    
    # 根路径 - 营销主页（静态文件）
    location = / {
        root /var/www/newapi-home;
        index index.html;
        try_files /newapi-home-redesign-v2.html =404;
    }
    
    # 营销页的静态资源（Logo等）
    location ~ ^/(logo\.jpg|assets/) {
        root /var/www/newapi-home;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
    
    # NewAPI 控制台（代理到 NewAPI）
    location /console {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    
    # NewAPI 登录页面
    location /login {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
    
    # NewAPI API 路径
    location /api {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
    
    # NewAPI Token 管理
    location /token {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
    
    # 其他所有路径也代理到 NewAPI
    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket 支持
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}

# HTTPS 配置
server {
    listen 443 ssl http2;
    server_name api.tang74.top;

    ssl_certificate /path/to/your/cert.pem;
    ssl_certificate_key /path/to/your/key.pem;

    # SSL 优化配置
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    # 复制上面的 location 配置
    # ... (和上面一样的配置)
}
```

### 部署步骤

#### 1. 创建营销主页目录

```bash
# 创建目录
sudo mkdir -p /var/www/newapi-home

# 下载主页文件
sudo wget https://raw.githubusercontent.com/t479842598/newapi-homepage-design/main/newapi-home-redesign-v2.html \
    -O /var/www/newapi-home/newapi-home-redesign-v2.html

# 下载 Logo
sudo wget https://raw.githubusercontent.com/t479842598/newapi-homepage-design/main/logo.jpg \
    -O /var/www/newapi-home/logo.jpg

# 设置权限
sudo chown -R www-data:www-data /var/www/newapi-home
sudo chmod -R 755 /var/www/newapi-home
```

#### 2. 配置 Nginx

```bash
# 创建配置文件
sudo nano /etc/nginx/sites-available/newapi

# 粘贴上面的完整配置

# 创建软链接
sudo ln -s /etc/nginx/sites-available/newapi /etc/nginx/sites-enabled/

# 测试配置
sudo nginx -t

# 重载 Nginx
sudo systemctl reload nginx
```

#### 3. 确保 NewAPI 运行在 3000 端口

```bash
# 检查 NewAPI 是否运行
curl http://localhost:3000

# 如果没有运行，启动 NewAPI
cd /path/to/newapi
./newapi
# 或
systemctl start newapi
```

#### 4. 配置 SSL（推荐）

```bash
# 安装 Certbot
sudo apt update
sudo apt install certbot python3-certbot-nginx

# 自动配置 SSL
sudo certbot --nginx -d api.tang74.top

# 测试自动续期
sudo certbot renew --dry-run
```

---

## 🎯 访问效果

配置完成后：

```
https://api.tang74.top/
└─ 显示营销主页（你设计的页面）

https://api.tang74.top/console
└─ 显示 NewAPI 控制台（原有界面）

https://api.tang74.top/login
└─ 显示 NewAPI 登录页面

https://api.tang74.top/api
└─ NewAPI 的 API 接口
```

---

## 🔧 替代方案：直接替换（不推荐）

如果你坚持要替换 NewAPI 的所有页面：

```bash
# 备份
cp /path/to/newapi/web/public/index.html /path/to/newapi/web/public/index.html.backup

# 替换
cp newapi-home-redesign-v2.html /path/to/newapi/web/public/index.html
cp logo.jpg /path/to/newapi/web/public/

# 重启
systemctl restart newapi
```

⚠️ **问题**：这样会导致：
- NewAPI 的控制台、登录页等全部被替换
- 用户无法正常使用 NewAPI 的功能
- 这不是正确的使用方式

---

## 📋 验证清单

配置完成后，验证：

- [ ] 访问 `https://api.tang74.top/` 看到营销主页
- [ ] 点击"立即开始使用"跳转到 `/console`
- [ ] `/console` 显示 NewAPI 的完整控制台界面
- [ ] 没有导航栏重叠问题
- [ ] Logo 正常显示
- [ ] 主题切换功能正常

---

## 🆘 故障排查

### 问题1：营销主页无法访问

```bash
# 检查文件权限
ls -la /var/www/newapi-home/

# 检查 Nginx 错误日志
tail -f /var/log/nginx/error.log
```

### 问题2：/console 显示 502 错误

```bash
# 检查 NewAPI 是否运行
systemctl status newapi
curl http://localhost:3000

# 查看 NewAPI 日志
journalctl -u newapi -f
```

### 问题3：导航栏仍然重叠

说明配置没有生效，检查：
```bash
# 确认 Nginx 配置已加载
sudo nginx -t
sudo systemctl reload nginx

# 清除浏览器缓存
Ctrl + Shift + R (强制刷新)
```

---

## 💡 总结

**关键点**：
1. 营销主页是**独立的静态页面**，只用于展示
2. 通过 Nginx 实现路径分离：`/` → 营销页，`/console` → NewAPI
3. 这样两者互不干扰，各司其职

**推荐架构**：
```
营销主页 (/) → 用户第一印象，品牌展示
      ↓ 点击按钮
NewAPI (/console) → 实际的功能系统
```

这就是 9527code.com 使用的架构！
