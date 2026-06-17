# GitHub Pages 部署指南

本文档说明如何将主页部署到 GitHub Pages。

## 🚀 自动部署（推荐）

本项目已配置 GitHub Actions，推送到 `main` 分支后会自动部署到 GitHub Pages。

### 步骤

#### 1. 启用 GitHub Pages

1. 访问仓库：https://github.com/t479842598/newapi-homepage-design
2. 点击 **Settings** (设置)
3. 在左侧菜单找到 **Pages**
4. 在 **Source** (来源) 下选择：
   - Source: **GitHub Actions**
5. 点击 **Save** (保存)

#### 2. 触发部署

部署会在以下情况自动触发：
- 推送代码到 `main` 分支
- 手动触发工作流

**手动触发方式：**
1. 进入 **Actions** 标签页
2. 选择 **Deploy to GitHub Pages** 工作流
3. 点击 **Run workflow** 按钮
4. 选择 `main` 分支
5. 点击 **Run workflow**

#### 3. 查看部署状态

1. 进入 **Actions** 标签页
2. 查看最新的工作流运行状态
3. 等待部署完成（通常1-2分钟）

#### 4. 访问网站

部署完成后，访问：
```
https://t479842598.github.io/newapi-homepage-design/
```

## 🌐 配置自定义域名

### 步骤

#### 1. 在 GitHub 设置域名

1. 进入 **Settings** → **Pages**
2. 在 **Custom domain** 输入你的域名：
   ```
   home.api.tang74.top
   ```
3. 点击 **Save**
4. 等待 DNS 检查完成

#### 2. 配置 DNS 记录

在你的域名 DNS 管理面板添加记录：

**方法A：使用 CNAME (推荐)**
```
类型: CNAME
名称: home (或 home.api)
值: t479842598.github.io.
TTL: 3600
```

**方法B：使用 A 记录**
```
类型: A
名称: home (或 home.api)
值: 185.199.108.153
值: 185.199.109.153
值: 185.199.110.153
值: 185.199.111.153
TTL: 3600
```

#### 3. 启用 HTTPS

1. DNS 解析生效后（通常几分钟到几小时）
2. 在 **Settings** → **Pages** 中
3. 勾选 **Enforce HTTPS**

#### 4. 验证

访问你的自定义域名：
```
https://home.api.tang74.top
```

## 📝 项目结构说明

```
.
├── .github/
│   └── workflows/
│       └── deploy.yml              # GitHub Actions 配置
├── index.html                      # 重定向入口
├── newapi-home-redesign-v2.html   # 主页文件
├── README.md                       # 项目说明
├── NewAPI部署教程.md               # NewAPI 部署教程
├── 部署说明-青棠API.md             # 详细部署说明
├── 液态玻璃转场说明.md             # 转场效果说明
└── 变更摘要-青棠API.md             # 变更记录
```

## 🔧 工作流配置说明

`.github/workflows/deploy.yml` 文件配置了自动部署流程：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches:
      - main          # 推送到 main 分支时触发
  workflow_dispatch:  # 支持手动触发

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Setup Pages
        uses: actions/configure-pages@v4
      
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: '.'    # 部署整个目录
      
      - name: Deploy to GitHub Pages
        uses: actions/deploy-pages@v4
```

## 📊 部署验证清单

部署完成后，请验证：

- [ ] 访问 `https://t479842598.github.io/newapi-homepage-design/` 正常
- [ ] 页面显示"青棠API"品牌信息
- [ ] Logo 图片正常加载
- [ ] 主题切换功能正常
- [ ] 语言切换功能正常
- [ ] 液态玻璃转场效果正常
- [ ] 所有链接正确指向（如果配置了自定义域名）
- [ ] 响应式布局正常（手机/平板/桌面）

## 🐛 常见问题

### Q1: GitHub Pages 显示 404

**原因：**
- Pages 未启用
- 工作流未成功执行
- DNS 未正确配置

**解决方法：**
1. 检查 Settings → Pages 是否启用
2. 查看 Actions 标签页的工作流状态
3. 等待几分钟让部署生效

### Q2: 样式或图片无法加载

**原因：**
- 相对路径问题
- CDN 被阻止

**解决方法：**
1. 检查浏览器开发者工具的 Network 标签
2. 确认所有资源路径正确
3. 如果使用了外部 CDN，确保可以访问

### Q3: 自定义域名无法访问

**原因：**
- DNS 记录未生效
- 配置错误

**解决方法：**
1. 使用 `nslookup` 或 `dig` 检查 DNS 记录
   ```bash
   nslookup home.api.tang74.top
   ```
2. 等待 DNS 传播（可能需要几小时）
3. 检查 CNAME 记录是否指向正确的 GitHub Pages 域名

### Q4: HTTPS 证书错误

**原因：**
- HTTPS 未启用
- 证书正在生成中

**解决方法：**
1. 在 Settings → Pages 中启用 **Enforce HTTPS**
2. 等待 Let's Encrypt 证书生成（通常几分钟）
3. 如果长时间未生效，取消勾选 HTTPS，保存，再重新勾选

## 🔄 更新部署

### 方法1：通过 Git 推送

```bash
# 修改文件后
git add .
git commit -m "更新：描述你的更改"
git push

# GitHub Actions 会自动部署
```

### 方法2：通过 GitHub 网页界面

1. 在 GitHub 网页上直接编辑文件
2. 提交更改
3. 自动触发部署

### 方法3：手动触发

1. 进入 **Actions** 标签页
2. 选择 **Deploy to GitHub Pages**
3. 点击 **Run workflow**

## 📈 监控部署

### 查看部署历史

1. 进入 **Actions** 标签页
2. 查看所有工作流运行记录
3. 点击具体运行查看详细日志

### 部署通知

如果需要部署通知：
1. 进入 **Settings** → **Notifications**
2. 配置邮件通知
3. 或集成 Slack/Discord 通知

## 🎯 性能优化建议

### 启用缓存

GitHub Pages 默认启用缓存，但可以优化：

1. **图片优化**：压缩图片文件
2. **代码压缩**：压缩 HTML（可选）
3. **CDN 加速**：使用 Cloudflare 等 CDN

### 配置 Cloudflare CDN

1. 在 Cloudflare 添加域名
2. 配置 DNS 指向 GitHub Pages
3. 启用 Cloudflare 代理（橙色云朵）
4. 配置缓存规则

## 📚 相关资源

- [GitHub Pages 官方文档](https://docs.github.com/en/pages)
- [GitHub Actions 文档](https://docs.github.com/en/actions)
- [自定义域名配置](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)

---

**部署成功！** 🎉

访问地址：https://t479842598.github.io/newapi-homepage-design/
