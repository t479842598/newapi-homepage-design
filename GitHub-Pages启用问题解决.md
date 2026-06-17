# GitHub Pages 启用问题解决方案

## ❌ 遇到的错误

```
Error: Get Pages site failed. Please verify that the repository has Pages enabled 
and configured to build using GitHub Actions
```

## 🔧 解决方法

GitHub Actions 工作流在运行，但 GitHub Pages 功能还没有启用。需要手动启用。

### 步骤 1: 启用 GitHub Pages

1. **访问仓库设置页面：**
   ```
   https://github.com/t479842598/newapi-homepage-design/settings/pages
   ```

2. **配置 Source（来源）：**
   - 找到 "Build and deployment" 部分
   - 在 **Source** 下拉菜单中选择：`GitHub Actions`
   - ⚠️ 注意：不要选择 "Deploy from a branch"

3. **保存设置：**
   - 选择后会自动保存
   - 页面会显示 "Your site is ready to be published"

4. **触发重新部署：**
   
   **方法A：推送一个小改动**
   ```bash
   cd "D:\桌面\主页设计"
   git commit --allow-empty -m "触发 GitHub Pages 部署"
   git push
   ```

   **方法B：手动触发工作流**
   - 访问：https://github.com/t479842598/newapi-homepage-design/actions
   - 点击 "Deploy to GitHub Pages" 工作流
   - 点击右侧 "Run workflow" 按钮
   - 选择 `main` 分支
   - 点击绿色 "Run workflow" 按钮

### 步骤 2: 等待部署完成

1. 访问 Actions 页面查看部署状态：
   ```
   https://github.com/t479842598/newapi-homepage-design/actions
   ```

2. 等待工作流运行完成（通常 1-2 分钟）

3. 当工作流显示绿色✓时，访问：
   ```
   https://t479842598.github.io/newapi-homepage-design/
   ```

## 📸 配置截图参考

### 正确的配置应该是：

```
Build and deployment
├─ Source: GitHub Actions  ← 选择这个！
└─ (不要选择 "Deploy from a branch")
```

## 🐛 如果还是失败

### 检查仓库权限

1. 进入：Settings → Actions → General
2. 找到 "Workflow permissions"
3. 确保选择了：`Read and write permissions`
4. 勾选：`Allow GitHub Actions to create and approve pull requests`
5. 点击 Save

### 检查 Pages 权限

1. 在 Settings → Pages 页面
2. 确认没有错误提示
3. 如果看到 "Upgrade or make this repository public to enable Pages"
   - 说明仓库是私有的，需要升级到 Pro 账户
   - 或者将仓库改为 Public

### 将仓库改为 Public

如果仓库是私有的且没有 Pro 账户：

1. 进入：Settings (仓库设置)
2. 滚动到最底部 "Danger Zone"
3. 点击 "Change visibility"
4. 选择 "Make public"
5. 确认操作

## ✅ 验证部署成功

部署成功后，你应该看到：

1. **在 Settings → Pages：**
   ```
   Your site is live at https://t479842598.github.io/newapi-homepage-design/
   ```

2. **在 Actions 页面：**
   - 工作流显示绿色 ✓
   - 没有错误信息

3. **访问网站：**
   - 页面正常加载
   - 显示"青棠API"主页

## 🎯 快速命令

启用 Pages 后，重新触发部署：

```bash
cd "D:\桌面\主页设计"
git commit --allow-empty -m "重新触发部署"
git push
```

## 📞 仍然有问题？

1. 检查仓库是否是 Public
2. 检查 Actions 权限是否正确
3. 查看完整的错误日志：
   - 访问 Actions 页面
   - 点击失败的工作流
   - 查看详细错误信息

---

**关键点：** 必须在 Settings → Pages 中手动启用 GitHub Actions 作为部署来源，工作流才能正常运行。
