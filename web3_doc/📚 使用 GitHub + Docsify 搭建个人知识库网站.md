
---

# 📚 使用 GitHub + Docsify 搭建个人知识库网站
> **从注册 GitHub 到上线专属知识库，全程图文级指引（2025 最佳实践）**

---

## 📁 目录

1. [前言：为什么选择 GitHub 搭建知识库？](#1-前言为什么选择-github-搭建知识库)
2. [第一步：注册 GitHub 账号](#第一步注册-github-账号)
3. [第二步：理解 GitHub Pages 与域名机制](#第二步理解-github-pages-与域名机制)
4. [第三步：创建个人站点仓库（xxx.github.io）](#第三步创建个人站点仓库xxxgithubio)
5. [第四步：上传 Markdown 文档](#第四步上传-markdown-文档)
6. [第五步：创建 index.html（让 Markdown 变成网页）](#第五步创建-indexhtml让-markdown-变成网页)
7. [第六步：启用 GitHub Pages（自动或手动）](#第六步启用-github-pages自动或手动)
8. [第七步：添加侧边栏导航 _sidebar.md](#第七步添加侧边栏导航-_sidebarmd)
9. [第八步：访问你的知识库网站](#第八步访问你的知识库网站)
10. [第九步：优化功能](#第九步优化功能)
11. [第十步：日常维护与更新流程](#第十步日常维护与更新流程)
12. [常见问题 FAQ 与注意事项（含本次踩坑总结）](#常见问题-faq-与注意事项含本次踩坑总结)

---

## 1. 前言：为什么选择 GitHub 搭建知识库？

- ✅ **免费**：GitHub Pages 免费提供静态网站托管
- ✅ **永久**：只要仓库存在，网站就永久在线
- ✅ **可版本控制**：所有修改都有记录，可回滚
- ✅ **支持 Markdown**：写文档像写笔记一样简单
- ✅ **全球访问**：CDN 加速，速度快

> 💡 适合程序员、学生、技术博主、知识管理者。

---

## 第一步：注册 GitHub 账号

1. 打开 [https://github.com](https://github.com)
2. 点击 “Sign up”
3. 填写用户名（如 `testweb3`）、邮箱、密码
4. 完成验证
5. 登录

> ✅ 注册后，你的用户名就是 `https://github.com/你的用户名`

---

## 第二步：理解 GitHub Pages 与域名机制

GitHub Pages 是 GitHub 提供的**静态网站托管服务**。

### 🌐 域名规则

| 仓库类型 | 访问地址 |
|--------|---------|
| 用户站点：`用户名.github.io` | `https://用户名.github.io` |
| 项目站点：`其他仓库` | `https://用户名.github.io/仓库名` |

> ✅ 本次我们搭建的是 **用户站点**，所以必须创建 `用户名.github.io` 仓库。

---

## 第三步：创建个人站点仓库（xxx.github.io）

1. 进入 GitHub 主页
2. 点击 “New repository”
3. 填写：
    - **Repository name**: `testweb3.github.io`（必须是你用户名）
    - **Description**: 我的个人知识库
    - **Visibility**: Public（GitHub Pages 要求公开）
    - ✅ **Add a README file**
4. 点击 “Create repository”

> ✅ 创建后，GitHub 会自动启用 Pages。

---

## 第四步：上传 Markdown 文档

### 推荐结构：

```
testweb3.github.io/
├── docs/
│   ├── 入门.md
│   ├── 使用.md
│   └── 指南.md
├── index.html
├── _sidebar.md
├── _navbar.md
├── 404.md
├── .nojekyll
└── README.md
```

### 上传方法：

1. 进入仓库
2. 点击 “Add file” → “Upload files”
3. 创建 `docs/` 文件夹（直接输入路径）
4. 上传你的 Markdown 文档

> 💡 建议使用英文文件名（如 `blockchain-basics.md`），避免中文路径编码问题。

---

## 第五步：创建 index.html（让 Markdown 变成网页）

在根目录创建 `index.html`，内容如下：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Web3知识库</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/docsify@4/lib/themes/vue.css">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/docsify-dark-mode@1.0.0/dist/style.min.css">
    <style>
        body, h1, h2, h3, code, pre {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif !important;
        }
    </style>
</head>
<body>

<div id="app">加载中...</div>

<!-- GitHub Corner -->
<a href="https://github.com/raintweb3/rainweb3.github.io" class="github-corner" aria-label="View source on GitHub">
    <svg viewBox="0 0 250 250" width="40" height="40">
        <path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path>
        <path d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.2 125.5,87.2 C122.9,97.4 130.2,105.8 134.5,103.6 C139.0,101.4 139.0,93.5 139.0,93.5 C139.0,82.0 141.0,73.5 141.0,73.5 C141.0,70.7 138.5,68.0 135.0,68.0 C131.0,68.0 128.0,71.0 128.0,71.0 C128.0,71.0 128.3,109.0 128.3,109.0 Z" fill="currentColor"></path>
    </svg>
</a>
<style>
    .github-corner:hover .octo-arm { animation: octocat-wave 560ms ease-in-out; }
    @keyframes octocat-wave {
        0%, 100% { transform: rotate(0); }
        20%, 60% { transform: rotate(-25deg); }
        40%, 80% { transform: rotate(10deg); }
    }
</style>

<!-- ✅ 1. 核心 -->
<script src="https://cdn.jsdelivr.net/npm/docsify@4"></script>

<!-- ✅ 2. 插件（顺序关键） -->
<script src="https://cdn.jsdelivr.net/npm/docsify-copy-code@1.0.0/dist/docsify-copy-code.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/docsify-dark-mode@1.0.0/dist/index.min.js"></script>

<!-- ✅ 3. 配置 -->
<script>
    window.$docsify = {
      name: 'Web3知识库',
      repo: 'https://github.com/raintweb3/rainweb3.github.io',
      loadSidebar: true,
      loadNavbar: true,
      auto2top: true,
      routerMode: 'history',
      coverpage: false,
      themeColor: '#42b983',
      copyCode: true,
      darkMode: true,
      darkModeSwitch: true,
      notFoundPage: '/404.md'
    };
</script>

</body>
</html>
```

> ✅ 这是核心文件，它让 Markdown 文档变成可交互的网页。

---

## 第六步：启用 GitHub Pages（自动或手动）

### ✅ 自动启用：
- 创建 `用户名.github.io` 仓库后，GitHub 会自动启用 Pages

### ✅ 手动检查：
1. 进入仓库 → Settings → Pages
2. 构建源（Source）：
    - Branch: `main`
    - Folder: `/ (root)`
3. 点击 “Save”

等待 1 分钟，会显示：
> Your site is published at https://testweb3.github.io

---

## 第七步：添加侧边栏导航 _sidebar.md

在 **根目录** 创建 `_sidebar.md`：

```md
- [🏠 首页](/)
- [📚 入门](/docs/区块链入门.md)
- [🔧 使用](/docs/智能合约.md)
- [🌐 指南](/docs/应用.md)
```

> ✅ 文件名必须是 `_sidebar.md`，Docsify 才能识别。

---

## 第八步：访问你的知识库网站

打开浏览器，访问：

👉 [https://testweb3.github.io](https://testweb3.github.io)

你应该看到：
- “正在加载文档...”
- 然后显示左侧菜单和文档内容

> ✅ 首次访问可能较慢，刷新即可。

---

## 第九步：优化功能

### ✅ 1. 添加 GitHub 小徽章

在 `index.html` 的 `<div id="app">` 前添加：

```html
<a href="https://github.com/testweb3/testweb3.github.io" class="github-corner" aria-label="View source on GitHub">
  <svg viewBox="0 0 250 250" width="40" height="40">
    <path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path>
    <path d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.2 125.5,87.2 C122.9,97.4 130.2,105.8 134.5,103.6 C139.0,101.4 139.0,93.5 139.0,93.5 C139.0,82.0 141.0,73.5 141.0,73.5 C141.0,70.7 138.5,68.0 135.0,68.0 C131.0,68.0 128.0,71.0 128.0,71.0 C128.0,71.0 128.3,109.0 128.3,109.0 Z" fill="currentColor"></path>
  </svg>
</a>
<style>.github-corner:hover .octo-arm{animation:octocat-wave 560ms ease-in-out}@keyframes octocat-wave{0%,100%{transform:rotate(0)}20%,60%{transform:rotate(-25deg)}40%,80%{transform:rotate(10deg)}}@media (max-width:500px){.github-corner:hover .octo-arm{animation:none}.github-corner .octo-arm{animation:octocat-wave 560ms ease-in-out}}</style>
```

---

## 第十步：日常维护与更新流程

1. 修改 `docs/` 下的 Markdown 文件
2. 提交更改
3. 等待 1 分钟
4. 刷新网站即可看到更新

> ✅ 所有操作均可在 GitHub 网页端完成，无需本地 Git。

---

## 常见问题 FAQ 与注意事项（含本次踩坑总结）

### ❓ 1. 为什么 `_sidebar.md` 一直 404，无法访问？

**根本原因**：GitHub Pages 默认使用 **Jekyll** 构建，而 Jekyll 会自动忽略所有以 `_` 开头的文件（如 `_sidebar.md`、`_config.yml`）。

**解决方案**：
> ✅ 在仓库根目录创建一个空文件：`.nojekyll`

作用：告诉 GitHub Pages **不要使用 Jekyll**，直接部署所有文件。

📌 **这是本次所有问题的根源**，也是最容易被忽略的关键点。

---

### ❓ 2. 为什么必须是 `_sidebar.md`？不能叫 `sidebar.md` 吗？

因为 **Docsify 有固定约定**：
- `_sidebar.md` → 自动加载为侧边栏
- `_coverpage.md` → 自动加载为封面
- `README.md` → 自动作为首页

如果你改名，Docsify 就“看不见”它。

✅ 所以必须叫 `_sidebar.md`，且在正确路径。

---

### ❓ 3. 为什么用了 CDN 还是加载失败？

可能原因：
- 使用了非官方 CDN（如 `cdn.staticfile.org`），不稳定
- 脚本加载顺序错误（`search.min.js` 必须在 `docsify.min.js` 之后）
- 浏览器缓存了旧版本

✅ **解决方案**：
- 使用官方 CDN：`https://unpkg.com/docsify/...`
- 确保加载顺序：
    1. `docsify.min.js`
    2. `search.min.js`
    3. `window.$docsify = { ... }`

---

### ❓ 4. 中文文件名能用吗？

**不推荐**。中文路径在 URL 中会被编码（如 `%E5%8C%BA%E5%9D%97%E9%93%BE`），可能导致：
- 链接失效
- 搜索功能无法识别
- 兼容性问题

✅ **推荐做法**：使用英文文件名，如 `blockchain-basics.md`，在 `_sidebar.md` 中显示中文标题。

---

### ❓ 5. 如何确保文档能被访问？

验证方法：
> 打开 `https://testweb3.github.io/docs/你的文档名.md`

如果能看到原始 Markdown 内容 → ✅ 成功
如果 404 → ❌ 文件未提交或路径错误

---

### ❓ 6. 为什么插件报错 `$docsify is not defined`？

因为 `window.$docsify` 配置写在了 `docsify.min.js` 之前。

✅ 正确顺序：
```html
<script src="docsify.min.js"></script>
<script src="search.min.js"></script>
<script>
  window.$docsify = { ... }
</script>
```

---
