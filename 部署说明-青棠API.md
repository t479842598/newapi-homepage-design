# 青棠API 主页部署说明

## 📁 文件信息

- **定制化文件**: `newapi-home-redesign-v2.html`
- **原始备份**: `newapi-home-redesign.backup-20260617.html`
- **品牌名称**: 青棠API
- **Logo URL**: https://i0.hdslb.com/bfs/openplatform/d3806df20bf1ba777c7f5a61efc4d9acf2beffd4.jpg
- **域名**: https://api.tang74.top
- **控制台**: https://api.tang74.top/console

## ✅ 已完成的定制化修改

### 1. 品牌信息更新
- ✅ 网站标题：青棠API - 企业级 AI 中转站
- ✅ Logo 图片：已替换为 B站 CDN 链接
- ✅ 品牌名称：所有"天机阁"替换为"青棠API"

### 2. 导航栏更新
- ✅ 首页：当前页面锚点
- ✅ 控制台：https://api.tang74.top/console
- ✅ 说明：页面内锚点 #second-page
- ✅ 入口模块：页面内锚点 #entry-page
- ❌ 已移除"绘图"导航项

### 3. 链接更新
- ✅ 所有主要CTA按钮：指向 https://api.tang74.top/console
- ✅ 登录按钮：https://api.tang74.top/login
- ✅ 开始使用按钮：https://api.tang74.top/console

### 4. 三大入口卡片
#### 卡片1 - 大陆直连
- 域名显示：api.tang74.top
- 链接：https://api.tang74.top/console

#### 卡片2 - 全球路线
- 域名显示：api.tang74.top
- 链接：https://api.tang74.top/console

#### 卡片3 - API 市场（原"图像生成"）
- 已更新为"API 市场"
- 域名显示：api.tang74.top
- 链接：https://api.tang74.top/console
- 功能描述：
  - 多种主流AI模型
  - 灵活的计费方式
  - 统一的接口标准
  - 快速集成部署
  - 专业技术支持

### 5. 多语言支持
- ✅ 中文 (zh-CN)：完整更新
- ✅ 英文 (en)：完整更新
- ✅ 日文 (ja)：完整更新

## 🚀 部署步骤

### 方法一：直接替换（推荐用于静态部署）

#### Step 1: 找到 NewAPI 的主页文件位置

NewAPI 项目的主页文件通常在以下位置之一：

```bash
# 可能的位置：
/path/to/newapi/web/public/index.html
/path/to/newapi/public/index.html  
/path/to/newapi/web/build/index.html
/path/to/newapi/web/dist/index.html
```

#### Step 2: 备份原始主页

```bash
# 进入 NewAPI 目录
cd /path/to/newapi

# 备份原始主页
cp web/public/index.html web/public/index.html.backup
```

#### Step 3: 上传新主页

将 `newapi-home-redesign-v2.html` 重命名为 `index.html` 并上传到相应目录：

```bash
# 复制新主页到目标位置
cp /path/to/newapi-home-redesign-v2.html /path/to/newapi/web/public/index.html
```

#### Step 4: 重启服务

```bash
# 重启 NewAPI 服务
systemctl restart newapi
# 或
docker-compose restart newapi
```

### 方法二：作为独立静态站点部署

#### 使用 Nginx

1. **创建站点目录：**
```bash
mkdir -p /var/www/qingtang-home
cp newapi-home-redesign-v2.html /var/www/qingtang-home/index.html
```

2. **配置 Nginx：**
```nginx
server {
    listen 80;
    server_name api.tang74.top;
    
    root /var/www/qingtang-home;
    index index.html;
    
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    # 如果需要SSL
    listen 443 ssl;
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
}
```

3. **重载 Nginx：**
```bash
nginx -t
systemctl reload nginx
```

#### 使用 Caddy

创建 `Caddyfile`：
```caddy
api.tang74.top {
    root * /var/www/qingtang-home
    file_server
    try_files {path} /index.html
}
```

### 方法三：部署到云平台

#### Vercel 部署
```bash
# 1. 安装 Vercel CLI
npm i -g vercel

# 2. 在文件所在目录运行
cd /path/to/your/files
vercel

# 3. 按照提示完成部署
```

#### Netlify 部署
```bash
# 1. 安装 Netlify CLI
npm install -g netlify-cli

# 2. 部署
cd /path/to/your/files
netlify deploy

# 3. 生产部署
netlify deploy --prod
```

#### Cloudflare Pages
1. 登录 Cloudflare Dashboard
2. 进入 Pages
3. 创建新项目
4. 上传 `newapi-home-redesign-v2.html`
5. 设置自定义域名

## 🔧 NewAPI 集成方案

### 方案A：作为 NewAPI 的根路径

如果你希望主页作为 NewAPI 的首页：

1. **修改 NewAPI 路由配置**
   
   找到 NewAPI 的路由配置文件（通常是 `router/router.go` 或类似），添加：

   ```go
   // 在路由初始化部分
   router.GET("/", func(c *gin.Context) {
       c.File("./web/public/index.html")
   })
   ```

2. **将文件放到正确位置**
   ```bash
   cp newapi-home-redesign-v2.html /path/to/newapi/web/public/index.html
   ```

### 方案B：作为独立路径

如果你希望保留 NewAPI 原有首页，将新主页放在 `/home` 路径：

```go
router.GET("/home", func(c *gin.Context) {
    c.File("./web/public/home.html")
})
```

## 📋 验证清单

部署完成后，请检查以下项目：

- [ ] 主页能够正常访问
- [ ] Logo 图片正确显示
- [ ] 主题切换功能正常（亮色/暗色）
- [ ] 语言切换功能正常（中文/英文/日文）
- [ ] 所有按钮链接指向正确
  - [ ] "立即开始使用" → /console
  - [ ] "登录" → /login
  - [ ] "开始使用" → /console
  - [ ] 三个卡片的"进入"按钮 → /console
- [ ] 响应式布局在移动端正常显示
- [ ] 页面滚动动画正常触发
- [ ] 导航栏固定在顶部
- [ ] 页脚显示"青棠API · AI 中转站"

## 🎨 自定义配置

如需进一步修改，打开 `newapi-home-redesign-v2.html` 并搜索以下关键字：

### 修改品牌信息
- 搜索 `青棠API` 进行全局替换
- 搜索 `https://i0.hdslb.com/bfs/openplatform/` 修改 Logo URL

### 修改链接
- 搜索 `https://api.tang74.top` 批量替换域名
- 搜索 `/console` 修改控制台路径
- 搜索 `/login` 修改登录路径

### 修改颜色主题
在 CSS 变量部分（`:root` 和 `html.dark`）修改：
```css
:root {
  --button-bg: #0c0c0c;  /* 主按钮背景色 */
  --button-text: #ffffff; /* 主按钮文字色 */
  /* ... 更多配置 */
}
```

## 🐛 故障排查

### 问题1：Logo 图片无法加载
- 检查图片 URL 是否可访问
- 检查服务器是否允许外部图片请求
- 可以将图片下载到本地并修改路径

### 问题2：样式不生效
- 检查是否有 CSP（内容安全策略）限制
- 检查是否正确加载了 Google Fonts
- 如果网络受限，可以下载字体到本地

### 问题3：链接跳转 404
- 检查 NewAPI 的路由配置
- 确认 `/console` 和 `/login` 路径存在
- 检查 Nginx/Apache 的重写规则

### 问题4：动画不流畅
- 检查浏览器是否启用了硬件加速
- 检查是否有性能瓶颈
- 可以在 CSS 中调整动画参数

## 📞 技术支持

如有问题，可以：
1. 检查浏览器控制台的错误信息
2. 查看 NewAPI 的日志输出
3. 确认所有路径和 URL 配置正确

## 📌 注意事项

1. **备份重要**：在任何修改前务必备份原文件
2. **路径一致性**：确保所有 URL 和路径配置一致
3. **HTTPS**：建议使用 HTTPS 以确保安全性
4. **CDN加速**：考虑使用 CDN 加速静态资源
5. **缓存清理**：更新后记得清理浏览器缓存

---

**文件版本**: v2.0  
**最后更新**: 2026-06-17  
**适用于**: NewAPI 项目
