# NewAPI 内部署完整教程

本教程详细说明如何将定制化主页部署到 NewAPI 项目中。

## 📋 目录

- [准备工作](#准备工作)
- [方法一：直接替换主页](#方法一直接替换主页)
- [方法二：Nginx反向代理](#方法二nginx反向代理)
- [方法三：作为静态资源服务](#方法三作为静态资源服务)
- [验证部署](#验证部署)
- [常见问题](#常见问题)

## 准备工作

### 1. 了解 NewAPI 项目结构

NewAPI 通常的目录结构：

```
newapi/
├── main.go                 # 主程序入口
├── web/
│   ├── public/             # 静态资源目录 ⭐
│   │   ├── index.html      # 默认主页（需要替换）
│   │   ├── assets/         # JS/CSS 资源
│   │   └── images/         # 图片资源
│   ├── src/                # 前端源码（如果使用React）
│   └── dist/               # 构建输出
├── config.json             # 配置文件
└── docker-compose.yml      # Docker配置（如果使用）
```

### 2. 下载主页文件

从本仓库下载 `newapi-home-redesign-v2.html`：

```bash
# 克隆整个仓库
git clone https://github.com/t479842598/newapi-homepage-design.git

# 或直接下载文件
wget https://raw.githubusercontent.com/t479842598/newapi-homepage-design/main/newapi-home-redesign-v2.html
```

### 3. 备份原有文件

⚠️ **重要**：在任何修改前务必备份！

```bash
cd /path/to/newapi
cp web/public/index.html web/public/index.html.backup.$(date +%Y%m%d)
```

---

## 方法一：直接替换主页

### 适用场景
- NewAPI 独立部署
- 使用默认的静态文件服务
- 最简单直接的方式

### 步骤

#### 1. 定位 NewAPI 主页文件

```bash
# 找到 NewAPI 安装目录
cd /path/to/newapi

# 查看 web 目录结构
ls -la web/public/
```

通常主页位于：
- `web/public/index.html`
- `public/index.html`
- `web/build/index.html`
- `web/dist/index.html`

#### 2. 替换主页文件

```bash
# 方式A：直接复制
cp newapi-home-redesign-v2.html web/public/index.html

# 方式B：重命名后复制
mv newapi-home-redesign-v2.html index.html
cp index.html web/public/

# 验证文件
ls -lh web/public/index.html
```

#### 3. 重启 NewAPI 服务

**使用 systemd：**
```bash
sudo systemctl restart newapi
sudo systemctl status newapi
```

**使用 Docker：**
```bash
docker-compose restart newapi
docker-compose logs -f newapi
```

**直接运行：**
```bash
# 停止旧进程
pkill newapi

# 启动新进程
./newapi &
```

#### 4. 验证部署

打开浏览器访问：`http://your-domain.com`

---

## 方法二：Nginx反向代理

### 适用场景
- NewAPI 运行在非80/443端口
- 需要将主页和API分离
- 使用 Nginx 作为前端代理

### 架构图

```
用户请求
   ↓
Nginx (80/443)
   ├─ / (根路径) → 静态主页
   └─ /api, /console, /token 等 → NewAPI (3000)
```

### 步骤

#### 1. 创建静态文件目录

```bash
# 创建主页目录
sudo mkdir -p /var/www/newapi-home

# 复制主页文件
sudo cp newapi-home-redesign-v2.html /var/www/newapi-home/index.html

# 设置权限
sudo chown -R www-data:www-data /var/www/newapi-home
sudo chmod -R 755 /var/www/newapi-home
```

#### 2. 配置 Nginx

创建或编辑 Nginx 配置文件：

```bash
sudo nano /etc/nginx/sites-available/newapi
```

添加以下配置：

```nginx
server {
    listen 80;
    server_name api.tang74.top;  # 改为你的域名

    # 主页 - 静态文件
    location = / {
        root /var/www/newapi-home;
        try_files /index.html =404;
    }

    # 静态资源（如果需要）
    location /assets/ {
        root /var/www/newapi-home;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }

    # API 路径 - 代理到 NewAPI
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

    # 控制台路径
    location /console {
        proxy_pass http://localhost:3000/console;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # Token 管理路径
    location /token {
        proxy_pass http://localhost:3000/token;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # API 路径
    location /api {
        proxy_pass http://localhost:3000/api;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

# HTTPS 配置（推荐）
server {
    listen 443 ssl http2;
    server_name api.tang74.top;

    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;

    # 其他配置同上...
    # 复制上面 server {} 块中的 location 配置
}
```

#### 3. 启用配置

```bash
# 创建软链接
sudo ln -s /etc/nginx/sites-available/newapi /etc/nginx/sites-enabled/

# 测试配置
sudo nginx -t

# 重载 Nginx
sudo systemctl reload nginx
```

#### 4. 配置 SSL（推荐）

使用 Certbot 自动配置 Let's Encrypt：

```bash
# 安装 Certbot
sudo apt update
sudo apt install certbot python3-certbot-nginx

# 自动配置 SSL
sudo certbot --nginx -d api.tang74.top

# 设置自动续期
sudo certbot renew --dry-run
```

---

## 方法三：作为静态资源服务

### 适用场景
- 使用 CDN 加速
- 主页与 API 完全分离
- 需要独立的缓存策略

### 部署到 Vercel

#### 1. 准备项目

```bash
cd /path/to/newapi-homepage-design

# 创建 vercel.json
cat > vercel.json << 'EOF'
{
  "routes": [
    {
      "src": "/",
      "dest": "/newapi-home-redesign-v2.html"
    }
  ]
}
EOF
```

#### 2. 部署

```bash
# 安装 Vercel CLI
npm i -g vercel

# 登录
vercel login

# 部署
vercel

# 生产部署
vercel --prod
```

#### 3. 配置自定义域名

在 Vercel Dashboard 中：
1. 进入项目设置
2. 添加自定义域名
3. 配置 DNS 记录

### 部署到 Netlify

#### 1. 准备项目

```bash
# 创建 netlify.toml
cat > netlify.toml << 'EOF'
[build]
  publish = "."

[[redirects]]
  from = "/"
  to = "/newapi-home-redesign-v2.html"
  status = 200
EOF
```

#### 2. 部署

```bash
# 安装 Netlify CLI
npm install -g netlify-cli

# 登录
netlify login

# 部署
netlify deploy

# 生产部署
netlify deploy --prod
```

### 部署到 Cloudflare Pages

1. 登录 Cloudflare Dashboard
2. 进入 Pages
3. 创建新项目
4. 连接 GitHub 仓库或直接上传文件
5. 设置构建命令：无需构建
6. 发布目录：`.`
7. 部署完成后配置自定义域名

---

## 验证部署

### 1. 基础功能检查

```bash
# 检查主页是否可访问
curl -I https://api.tang74.top/

# 应该返回 200 OK
HTTP/1.1 200 OK
Content-Type: text/html
```

### 2. 浏览器测试

打开浏览器访问主页，检查：

- [ ] 页面正常加载
- [ ] Logo 显示正确
- [ ] 标题显示"青棠API"（或你的品牌名）
- [ ] 主题切换功能正常
- [ ] 语言切换功能正常
- [ ] 所有按钮链接正确跳转：
  - [ ] "立即开始使用" → `/console`
  - [ ] "登录" → `/login`
  - [ ] 三个卡片按钮 → `/console`
- [ ] 液态玻璃转场效果正常
- [ ] 响应式布局正常（手机/平板/桌面）

### 3. 控制台和API测试

```bash
# 测试控制台访问
curl -I https://api.tang74.top/console

# 测试 API 访问
curl -I https://api.tang74.top/api

# 测试 Token 页面
curl -I https://api.tang74.top/token
```

---

## 常见问题

### Q1: 替换后页面显示空白

**原因：**
- 文件路径错误
- 权限问题
- NewAPI 未重启

**解决方法：**
```bash
# 检查文件是否存在
ls -la web/public/index.html

# 检查权限
chmod 644 web/public/index.html

# 重启服务
systemctl restart newapi
```

### Q2: 点击按钮后404

**原因：**
- NewAPI 路由配置问题
- Nginx 代理配置不正确

**解决方法：**
1. 检查 NewAPI 是否正常运行
2. 检查 Nginx 配置中的 `proxy_pass` 设置
3. 查看 NewAPI 日志

### Q3: Logo图片无法加载

**原因：**
- 外部图片被防火墙拦截
- CSP 策略限制

**解决方法：**
```bash
# 下载Logo到本地
wget https://i0.hdslb.com/bfs/openplatform/xxx.jpg -O web/public/logo.jpg

# 修改HTML中的Logo路径
sed -i 's|https://i0.hdslb.com/bfs/openplatform/xxx.jpg|/logo.jpg|g' web/public/index.html
```

### Q4: 样式不生效

**原因：**
- 浏览器缓存
- CSP 策略限制

**解决方法：**
```bash
# 清除浏览器缓存
Ctrl + Shift + R (强制刷新)

# 检查 CSP 设置
# 在 Nginx 配置中添加
add_header Content-Security-Policy "default-src 'self' 'unsafe-inline' 'unsafe-eval' https://fonts.googleapis.com https://fonts.gstatic.com https://i0.hdslb.com;";
```

### Q5: Docker 环境下如何部署

**方法A：挂载主页文件**

```yaml
# docker-compose.yml
services:
  newapi:
    image: newapi:latest
    volumes:
      - ./newapi-home-redesign-v2.html:/app/web/public/index.html
    ports:
      - "3000:3000"
```

**方法B：构建自定义镜像**

```dockerfile
# Dockerfile
FROM newapi:latest
COPY newapi-home-redesign-v2.html /app/web/public/index.html
```

```bash
# 构建镜像
docker build -t newapi:custom .

# 运行
docker run -d -p 3000:3000 newapi:custom
```

---

## 🔧 高级配置

### 配置 CDN 加速

如果使用 CDN，在 Nginx 中添加缓存头：

```nginx
location = / {
    root /var/www/newapi-home;
    try_files /index.html =404;
    
    # 缓存控制
    add_header Cache-Control "public, max-age=3600";
    
    # CDN 支持
    add_header X-Content-Type-Options "nosniff";
    add_header X-Frame-Options "SAMEORIGIN";
}
```

### 配置 Gzip 压缩

```nginx
# 在 nginx.conf 或 server 块中
gzip on;
gzip_types text/html text/css application/javascript;
gzip_min_length 1000;
gzip_comp_level 6;
```

### 监控和日志

```bash
# 查看 NewAPI 日志
tail -f /var/log/newapi/app.log

# 查看 Nginx 访问日志
tail -f /var/log/nginx/access.log

# 查看 Nginx 错误日志
tail -f /var/log/nginx/error.log
```

---

## 📚 相关文档

- [README.md](./README.md) - 项目说明
- [部署说明-青棠API.md](./部署说明-青棠API.md) - 通用部署指南
- [液态玻璃转场说明.md](./液态玻璃转场说明.md) - 转场效果说明

## 🆘 获取帮助

如果遇到问题：
1. 查看本文档的"常见问题"部分
2. 检查 GitHub Issues
3. 提交新的 Issue

---

**部署成功后，记得⭐ Star 本项目！**
