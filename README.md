# NewAPI Homepage Design - 青棠API

一个现代化、优雅的 NewAPI 主页设计，采用液态玻璃拟态风格。

## ✨ 特性

- 🎨 **液态玻璃设计** - 现代化的 Glassmorphism UI
- 🌓 **明暗主题** - 自动适配系统主题，支持手动切换
- 🌍 **多语言支持** - 中文/English/日本語
- 📱 **响应式布局** - 完美适配桌面、平板、手机
- 💫 **优雅动画** - 平滑的页面转场和交互动画
- 🎭 **液态玻璃转场** - 点击导航时的优雅扩散效果
- ♿ **无障碍适配** - 尊重用户的动画偏好设置
- 🚀 **零依赖** - 纯 HTML/CSS/JavaScript，无需构建

## 🎯 预览

- **在线预览**: [GitHub Pages](https://t479842598.github.io/newapi-homepage-design/)
- **主文件**: `newapi-home-redesign-v2.html`

## 📦 快速开始

### 本地预览

```bash
# 方法1：直接打开
双击 newapi-home-redesign-v2.html

# 方法2：Python HTTP 服务器
python -m http.server 8000
# 访问 http://localhost:8000/newapi-home-redesign-v2.html

# 方法3：Node.js
npx serve .
```

### 部署到 GitHub Pages

1. Fork 本仓库
2. 进入仓库设置 (Settings)
3. 在 Pages 部分选择 `main` 分支
4. 保存后等待部署完成

### 部署到 NewAPI

#### 方法一：直接替换主页

```bash
# 1. 找到 NewAPI 的 web 目录
cd /path/to/newapi

# 2. 备份原主页
cp web/public/index.html web/public/index.html.backup

# 3. 复制新主页
cp newapi-home-redesign-v2.html web/public/index.html

# 4. 重启服务
systemctl restart newapi
# 或使用 Docker
docker-compose restart newapi
```

#### 方法二：Nginx 反向代理

如果 NewAPI 运行在端口 3000，主页单独部署：

```nginx
server {
    listen 80;
    server_name your-domain.com;
    
    # 主页
    location = / {
        root /var/www/newapi-home;
        try_files /newapi-home-redesign-v2.html =404;
    }
    
    # API 和其他路径代理到 NewAPI
    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

#### 方法三：作为静态资源

将主页部署到 CDN 或静态托管服务：

1. 上传 `newapi-home-redesign-v2.html` 到静态托管
2. 在 NewAPI 中配置主页重定向
3. 或使用 iframe 嵌入

## 🎨 定制化

### 修改品牌信息

编辑 `newapi-home-redesign-v2.html`：

```javascript
// 搜索并替换
青棠API → 你的品牌名
api.tang74.top → 你的域名
https://i0.hdslb.com/bfs/... → 你的 Logo URL
```

### 调整颜色主题

在 CSS 变量部分修改：

```css
:root {
  --button-bg: #0c0c0c;      /* 主按钮背景色 */
  --button-text: #ffffff;    /* 主按钮文字色 */
  --glass-bg: #ffffff;       /* 玻璃效果背景 */
  /* ... 更多配置 */
}
```

### 调整动画速度

```css
/* 液态玻璃转场速度 */
animation: liquid-expand 0.5s /* 改为 0.3s (更快) 或 0.8s (更慢) */
```

## 📁 文件说明

```
.
├── newapi-home-redesign-v2.html    # 主文件（推荐使用）
├── README.md                       # 本文件
├── 部署说明-青棠API.md             # 详细部署指南
├── 变更摘要-青棠API.md             # 定制化变更记录
├── 液态玻璃转场说明.md             # 转场效果说明
└── NewAPI部署教程.md               # NewAPI 内部署完整教程
```

## 🔧 技术栈

- **HTML5** - 语义化结构
- **CSS3** - 现代化样式
  - CSS Variables (主题系统)
  - Backdrop Filter (毛玻璃效果)
  - CSS Animations (流畅动画)
  - Flexbox & Grid (响应式布局)
- **JavaScript (ES6+)** - 交互逻辑
  - Intersection Observer (滚动动画)
  - LocalStorage (状态持久化)
  - 自定义转场系统
- **Google Fonts** - Inter & Unbounded 字体

## 🌐 浏览器兼容性

| 浏览器 | 最低版本 | 支持程度 |
|--------|---------|---------|
| Chrome | 76+ | ✅ 完全支持 |
| Edge | 79+ | ✅ 完全支持 |
| Safari | 9+ | ✅ 完全支持 |
| Firefox | 103+ | ✅ 完全支持 |
| 旧版浏览器 | - | ⚠️ 降级支持 |

## 📖 使用文档

详细的使用和部署文档请查看：

- [部署说明-青棠API.md](./部署说明-青棠API.md) - 完整的部署指南
- [NewAPI部署教程.md](./NewAPI部署教程.md) - NewAPI 内部署教程
- [液态玻璃转场说明.md](./液态玻璃转场说明.md) - 转场效果说明
- [变更摘要-青棠API.md](./变更摘要-青棠API.md) - 变更记录

## 🎯 设计理念

本设计采用现代化的 **Glassmorphism (液态玻璃拟态)** 设计风格，灵感来自：

- Apple 的毛玻璃效果
- Material Design 的涟漪动画
- 流行的 Web3 设计趋势

目标是创造一个**现代、优雅、高端**的用户体验。

## 📸 截图

### 亮色主题
![亮色主题](./screenshots/light-theme.png)

### 暗色主题
![暗色主题](./screenshots/dark-theme.png)

### 液态玻璃转场
![转场效果](./screenshots/transition.gif)

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 📄 许可证

MIT License

## 🔗 相关链接

- [NewAPI 项目](https://github.com/Calcium-Ion/new-api)
- [设计参考：9527code.com](https://9527code.com/)

## 💬 联系方式

- GitHub: [@t479842598](https://github.com/t479842598)
- 项目地址: https://github.com/t479842598/newapi-homepage-design

---

**Made with ❤️ for NewAPI Community**
