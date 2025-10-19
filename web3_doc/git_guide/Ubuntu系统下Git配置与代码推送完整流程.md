# VS Code + WSL: Ubuntu 环境下 Git 配置与代码推送完整流程（2025年）

> **适用场景**：在 Windows 上使用 WSL（Windows Subsystem for Linux）运行 Ubuntu 子系统，通过 VS Code 进行开发，并将项目推送至 GitHub。

---
**作者：RainWeb3** 
---
## 目录

1. [前置条件](#✅-前置条件)
2. [步骤 1：打开 WSL 环境的项目](#步骤-1打开-wsl-环境的项目)
3. [步骤 2：安装 Git 并配置全局身份](#步骤-2安装-git-并配置全局身份)
   - 2.1 打开终端
   - 2.2 安装 Git（若未安装）
   - 2.3 配置 Git 用户信息
   - 2.4 验证配置
4. [步骤 3：配置 SSH 密钥（推荐，免密推送）](#步骤-3配置-ssh-密钥推荐免密推送)
   - 3.1 生成 SSH 密钥
   - 3.2 复制公钥内容
   - 3.3 在 GitHub 添加 SSH 密钥
   - 3.4 验证 SSH 连接
5. [步骤 4：初始化本地 Git 仓库（首次推送）](#步骤-4初始化本地-git-仓库首次推送)
6. [步骤 5：暂存并提交本地代码](#步骤-5暂存并提交本地代码)
7. [步骤 6：创建远程仓库并关联](#步骤-6创建远程仓库并关联)
   - 6.1 安装 GitHub CLI 工具 `gh`
   - 6.2 登录 GitHub 账号
   - 6.3 创建远程仓库并自动关联
   - 6.4 手动关联远程仓库（可选，若自动失败）
8. [步骤 7：推送代码到远程仓库](#步骤-7推送代码到远程仓库)
9. [步骤 8：验证推送结果](#步骤-8验证推送结果)
10. [常见问题与解决](#🛠-常见问题与解决)
11. [后续操作（日常开发）](#🎯-后续操作日常开发)
12. [总结](#✅-总结)

---

## ✅ 前置条件

- 已安装 WSL 并启用 Ubuntu 子系统  
  （可通过 `wsl --install Ubuntu` 安装）
- 已在 VS Code 中安装 **Remote - WSL** 扩展（用于连接 WSL 环境）
- 已注册 [GitHub](https://github.com) 账号

---

## 步骤 1：打开 WSL 环境的项目

1. 打开 **VS Code**
2. 点击左侧「**远程资源管理器**」图标（或按 `Ctrl+Shift+P`）
3. 输入并选择：`Remote-WSL: New Window`
4. 在新打开的 WSL 窗口中，点击「**打开文件夹**」
5. 选择 Ubuntu 中的项目路径（如 `/home/你的用户名/项目名`），打开本地项目

> 💡 确保 VS Code 底部状态栏显示 `WSL: Ubuntu`，表示当前处于 WSL 环境。

---

## 步骤 2：安装 Git 并配置全局身份

### 2.1 打开终端

- 按 `Ctrl + `` ` 调出 VS Code 终端
- 确保终端左侧显示 `bash` 或 `wsl`，表示运行在 WSL 环境

### 2.2 安装 Git（若未安装）

```bash
sudo apt update && sudo apt install git -y
```

### 2.3 配置 Git 用户信息

```bash
# 替换为你的 GitHub 用户名和注册邮箱
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的GitHub注册邮箱"
```

> 示例：
> ```bash
> git config --global user.name "lgyRain"
> git config --global user.email "lgyrain@example.com"
> ```

### 2.4 验证配置

```bash
git config --global --list
```

> ✅ 输出应包含你设置的 `user.name` 和 `user.email`

---

## 步骤 3：配置 SSH 密钥（推荐，免密推送）

### 3.1 生成 SSH 密钥

```bash
ssh-keygen -t ed25519 -C "你的GitHub注册邮箱"
```

- 提示 `Enter file in which to save the key`：直接按回车（使用默认路径 `~/.ssh/id_ed25519`）
- 提示 `Enter passphrase`：可按回车跳过（无密码），或设置密码增强安全性

> 🔐 建议：对于生产环境或高安全需求场景，建议设置 `passphrase`。

---

### 3.2 复制公钥内容

```bash
cat ~/.ssh/id_ed25519.pub
```

- 复制输出内容（从 `ssh-ed25519` 开头到邮箱结尾的整行）

> 📌 注意：必须复制完整的一行，包括 `ssh-ed25519` 前缀和邮箱后缀。

---

### 3.3 在 GitHub 添加 SSH 密钥

1. 登录 [GitHub](https://github.com)
2. 点击右上角头像 → **Settings**
3. 左侧菜单选择 **SSH and GPG keys** → **New SSH key**
4. 填写：
   - **Title**：`WSL-Ubuntu`（便于识别）
   - **Key**：粘贴上一步复制的公钥内容
5. 点击 **Add SSH key**

> ✅ 添加成功后，你可以在列表中看到新添加的密钥。

---

### 3.4 验证 SSH 连接

```bash
ssh -T git@github.com
```

> ✅ 成功提示：
> ```
> Hi 你的GitHub用户名! You've successfully authenticated, but GitHub does not provide shell access.
> ```

> ❌ 若失败，请检查：
> - 公钥是否正确添加
> - 是否复制了完整的公钥内容
> - 是否使用了正确的协议（SSH 而非 HTTPS）

---

## 步骤 4：初始化本地 Git 仓库（首次推送）

1. 在 VS Code 中，点击左侧「**源代码管理**」图标（分支图标）
2. 点击顶部的「**初始化仓库**（Initialize Repository）」
3. 此时项目根目录会生成 `.git` 文件夹，表示本地仓库创建完成

> 💡 你也可以在终端中手动执行：
> ```bash
> git init
> ```

---

## 步骤 5：暂存并提交本地代码

1. 在「源代码管理」面板的「更改」区域：
   - 点击顶部 **+** 号（暂存所有更改）
   - 或右键单个文件选择「暂存更改」
2. 在提交输入框中填写提交信息（如 `feat: 初始化项目`）
3. 点击旁边的 **✓** 号（提交到本地仓库）

> ✅ 提交成功后，「更改」区域清空，历史记录中显示新提交。

> 📝 提交信息规范建议使用 [Conventional Commits](https://www.conventionalcommits.org/) 格式，如：
> - `feat: 添加登录功能`
> - `fix: 修复按钮点击失效问题`
> - `docs: 更新 README`

---

## 步骤 6：创建远程仓库并关联

### 6.1 安装 GitHub CLI 工具 `gh`

```bash
sudo apt install gh -y
```

> ⚠️ 若提示 `E: Unable to locate package gh`，请参考 GitHub 官方文档添加 `gh` 的 APT 源。

---

### 6.2 登录 GitHub 账号

```bash
gh auth login
```

按提示操作：
- 选择 `GitHub.com`
- 选择 `SSH` 协议
- 按 Enter 打开浏览器
- 输入验证码，登录并授权

> ✅ 成功提示：`Logged in as 你的GitHub用户名`

---

### 6.3 创建远程仓库并自动关联

```bash
gh repo create 你的仓库名 --private
```

> 示例：
> ```bash
> gh repo create ethers-simple-storage --private
> ```

- `--private`：创建私有仓库（可改为 `--public` 创建公开仓库）
- 执行后，会自动创建 GitHub 仓库并关联远程地址 `origin`

---

### 6.4 手动关联远程仓库（可选，若自动失败）

```bash
# 替换为你的仓库 SSH 地址（从 GitHub 仓库页面「Code → SSH」复制）
git remote add origin git@github.com:你的用户名/你的仓库名.git
```

> 示例：
> ```bash
> git remote add origin git@github.com:lgyRain/ethers-simple-storage.git
> ```

> ✅ 验证远程地址：
> ```bash
> git remote -v
> ```

---

## 步骤 7：推送代码到远程仓库

```bash
# 首次推送：关联本地 main 分支与远程 main 分支
git push -u origin main
```

> ✅ 推送成功提示：
> ```
> Enumerating objects: ..., done.
> Counting objects: 100% (...)
> ...
> To github.com:你的用户名/你的仓库名.git
>  * [new branch]      main -> main
> Branch 'main' set up to track remote branch 'main' from 'origin'.
> ```

> 📌 参数说明：
> - `-u`：设置上游分支（upstream），后续可直接使用 `git push` 和 `git pull`

---

## 步骤 8：验证推送结果

1. 打开浏览器，访问你的 GitHub 仓库地址：  
   `https://github.com/你的用户名/你的仓库名`
2. 查看文件列表，确认本地项目文件（如 `src/`, `README.md` 等）已成功推送

> ✅ 若文件显示完整，说明推送成功！

---

## 🛠 常见问题与解决

| 问题 | 解决方案 |
|------|----------|
| `remote origin already exists` | 执行 `git remote rm origin` 删除旧关联，再重新添加 |
| `Repository not found` | 检查仓库名是否正确，或使用 `gh repo create` 重新创建 |
| `Permission denied (publickey)` | 重新执行步骤 3，确保 SSH 公钥已添加并验证成功 |
| `gh: command not found` | 确保已运行 `sudo apt install gh -y` |
| 推送时提示输入用户名/密码 | 确保使用 **SSH 地址**（`git@github.com:...`）而非 HTTPS 地址 |

> 💡 补充：若 SSH 服务未启动，可尝试：
> ```bash
> eval "$(ssh-agent -s)"
> ssh-add ~/.ssh/id_ed25519
> ```

---

## 🎯 后续操作（日常开发）

完成首次推送后，后续代码更新只需重复以下三步：

1. **修改代码**
2. **暂存更改**（VS Code 源代码管理 → +）
3. **提交并推送**（输入信息 → ✓ → `Ctrl+Shift+P` → `Git: Push`）

> ✅ 推荐快捷键流程：
> - `Ctrl+Shift+G`：打开源代码管理
> - `Ctrl+Enter`：提交
> - `Ctrl+Shift+P` → `Git: Push`：推送

---

## ✅ 总结

通过以上流程，你已在 **VS Code + WSL: Ubuntu** 环境中完成了：

- ✅ Git 安装与身份配置  
- ✅ SSH 密钥设置（实现免密推送）  
- ✅ 本地仓库初始化  
- ✅ 远程仓库创建与自动关联  
- ✅ 代码首次推送至 GitHub  

现在你可以高效地在 WSL 环境中进行开发，并无缝同步代码至 GitHub！

> 💡 **最佳实践建议**：
> - 将此流程保存为开发环境搭建文档，便于后续复用
> - 为不同环境（如工作、个人）使用不同的 SSH 密钥
> - 定期备份 `.ssh` 目录以防密钥丢失

> 🚫 **安全提醒**：
> - 切勿将私钥文件（如 `id_ed25519`）上传至 GitHub 或分享给他人
> - 使用 `passphrase` 增强密钥安全性
> - 避免在公共电脑上保存 SSH 密钥

祝你开发顺利！🚀
