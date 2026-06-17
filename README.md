# NewAPI Homepage Design

一个现代化、优雅的 NewAPI 主页设计，采用液态玻璃拟态风格。

## ✨ 特性

- 🎨 **液态玻璃设计** - 现代化的 Glassmorphism UI
- 🌓 **明暗主题** - 自动适配系统主题，支持手动切换
- 🌍 **多语言支持** - 中文/English/日本語
- 📱 **响应式布局** - 完美适配桌面、平板、手机
- 💫 **优雅动画** - 平滑的页面转场和交互动画
- 🎭 **液态玻璃转场** - 点击导航时的优雅扩散效果
- 🚀 **零依赖** - 纯 HTML/CSS/JavaScript，无需构建

## 🎯 在线预览

- **GitHub Pages**: https://t479842598.github.io/newapi-homepage-design/
- **主文件**: `newapi-home-redesign-v2.html`

## 📦 快速开始

### 本地预览

```bash
# 直接打开 HTML 文件
双击 newapi-home-redesign-v2.html

# 或使用本地服务器
python -m http.server 8000
# 访问 http://localhost:8000/newapi-home-redesign-v2.html
```

## 🚀 部署

### 部署到 NewAPI

```bash
# 1. 备份原文件
cp /path/to/newapi/web/public/index.html /path/to/newapi/web/public/index.html.backup

# 2. 下载并替换主页
wget https://raw.githubusercontent.com/t479842598/newapi-homepage-design/main/newapi-home-redesign-v2.html -O /path/to/newapi/web/public/index.html

# 3. 重启服务
systemctl restart newapi
```

### 使用 Nginx 反向代理

```nginx
server {
    listen 80;
    server_name your-domain.com;
    
    # 主页
    location = / {
        root /var/www/newapi-home;
        try_files /newapi-home-redesign-v2.html =404;
    }
    
    # API 代理到 NewAPI
    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
    }
}
```

## 🎨 定制化

编辑 `newapi-home-redesign-v2.html` 修改品牌信息：

```html
<!-- 品牌名称 -->
搜索替换: 青棠API → 你的品牌名

<!-- 域名 -->
搜索替换: api.tang74.top → 你的域名

<!-- Logo -->
搜索替换: https://i0.hdslb.com/bfs/... → 你的Logo URL
```

## 🌐 浏览器兼容性

| 浏览器 | 最低版本 |
|--------|---------|
| Chrome | 76+ |
| Edge | 79+ |
| Safari | 9+ |
| Firefox | 103+ |

## 📄 许可证

MIT License

## 🔗 相关链接

- [NewAPI 项目](https://github.com/Calcium-Ion/new-api)

---

**Made with ❤️ for NewAPI Community**
