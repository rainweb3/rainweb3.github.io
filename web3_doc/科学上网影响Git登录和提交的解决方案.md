# 科学上网影响Git登录和提交的解决方案
## 📋 目录

1. [✅ 一、为 Git 单独设置代理（推荐）](#-一为-git-单独设置代理推荐)
2. [✅ 二、针对 SSH 协议设置代理（如果你用 `git@github.com:xxx.git`）](#-二针对-ssh-协议设置代理如果你用-gitgithubcomxxxgit)
3. [✅ 三、替代方案：使用镜像或代理中转（无需配置 Git）](#-三替代方案使用镜像或代理中转无需配置-git)
4. [✅ 四、全局设置（可选）：为命令行设置环境变量](#-四全局设置可选为命令行设置环境变量)
5. [✅ 五、常见问题排查](#-五常见问题排查)
6. [✅ 总结：最佳实践](#-总结最佳实践)

> **文档更新日期**：2025年10月3日

---

科学上网（使用代理）虽然可以改善整体网络环境，但 **Git 本身并不会自动继承系统或浏览器的代理设置**，因此即使“科学上网”了，`git clone`、`git push`、`git pull` 等操作仍可能失败，出现如下错误：

```
fatal: unable to access 'https://github.com/xxx/xxx.git/': Failed to connect to github.com port 443: Connection timed out
```

这是因为 Git 使用的是底层网络调用，不受浏览器代理影响。以下是 **完整解决方案**，适用于 GitHub、GitLab、Gitee 等代码托管平台。

---

## ✅ 一、为 Git 单独设置代理（推荐）

### 1. 确认你的代理地址和端口

常见的代理工具（如 Clash、V2Ray、Shadowsocks）会在本地开启一个代理服务，典型地址如下：

- **HTTP/SOCKS5 代理地址**：`127.0.0.1:7890`
- **Clash 默认端口**：
  - HTTP 代理：`7890`
  - SOCKS5 代理：`7891`
  - 透明代理/混合端口：`7890`

> 你可以在代理软件的设置中查看“允许局域网连接”和“HTTP/SOCKS 端口”。

---

### 2. 设置 Git 使用代理

#### ✅ 方法一：使用 SOCKS5 代理（推荐，效率高）

```bash
# 设置 HTTP 和 HTTPS 代理为 SOCKS5
git config --global http.proxy socks5://127.0.0.1:7890
git config --global https.proxy socks5://127.0.0.1:7890
```

> 如果你使用的是 Clash 的 SOCKS 端口（如 7891），则改为：
> ```bash
> git config --global http.proxy socks5://127.0.0.1:7891
> ```

#### ✅ 方法二：使用 HTTP 代理

```bash
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890
```

---

### 3. 验证代理是否设置成功

```bash
git config --global --get http.proxy
git config --global --get https.proxy
```

输出应为：
```
socks5://127.0.0.1:7890
```

---

### 4. 取消代理（恢复直连）

当你不需要代理时，可以取消设置：

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

---

## ✅ 二、针对 SSH 协议设置代理（如果你用 `git@github.com:xxx.git`）

如果你使用 SSH 协议（如 `git clone git@github.com:xxx/xxx.git`），需要配置 SSH 的代理。

### 1. 编辑 SSH 配置文件

打开或创建 `~/.ssh/config`（Windows 是 `C:\Users\你的用户名\.ssh\config`）

添加以下内容：

```config
Host github.com
    HostName github.com
    User git
    Port 22
    ProxyCommand connect -S 127.0.0.1:7890 %h %p
```

> ⚠️ 需要先安装 `connect` 工具：
> - **macOS/Linux**：`brew install connect` 或 `apt install connect-proxy`
> - **Windows**：可通过 WSL 安装，或使用 `netcat` 替代

---

## ✅ 三、替代方案：使用镜像或代理中转（无需配置 Git）

如果你不想配置代理，也可以通过修改 Git 地址来加速。

### 方法1：使用 GitHub 镜像站（如 FastGit）

```bash
# 原始地址
git clone https://github.com/user/repo.git

# 改为使用镜像
git clone https://hub.fastgit.org/user/repo.git
```

> ⚠️ 注意：FastGit 等镜像站可能不定期关闭，不建议用于生产环境。

### 方法2：使用代理中转服务（如 ghproxy.com）

```bash
git clone https://ghproxy.com/https://github.com/user/repo.git
```

---

## ✅ 四、全局设置（可选）：为命令行设置环境变量

某些命令行工具（如 npm、curl）也受代理影响，可设置环境变量：

```bash
# Windows（CMD）
set http_proxy=http://127.0.0.1:7890
set https_proxy=http://127.0.0.1:7890

# Linux/macOS
export http_proxy=http://127.0.0.1:7890
export https_proxy=http://127.0.0.1:7890
```

---

## ✅ 五、常见问题排查

| 问题 | 解决方案 |
|------|----------|
| `Connection timed out` | 检查代理端口是否正确，代理软件是否开启 |
| `Could not resolve host` | 检查 DNS 设置，或尝试更换为 `socks5` 代理 |
| `Proxy authentication required` | 如果代理需要账号密码，设置为 `http://user:pass@127.0.0.1:7890` |
| 代理设置后仍无效 | 尝试关闭防火墙，或使用 `socks5` 而非 `http` |

---

## ✅ 总结：最佳实践

| 场景 | 推荐方案 |
|------|----------|
| 使用 HTTPS 克隆 | `git config --global http.proxy socks5://127.0.0.1:7890` |
| 使用 SSH 克隆 | 配置 `~/.ssh/config` 使用 `ProxyCommand` |
| 想快速解决 | 使用 `ghproxy.com` 或 `fastgit.org` 镜像 |
| 多人共享环境 | 建议统一使用 SOCKS5 代理 + Git 全局配置 |

---

✅ **一句话总结**：  
> 科学上网 ≠ Git 能用，必须**手动为 Git 配置代理**，推荐使用 `socks5://127.0.0.1:7890`，设置后即可正常 `git clone/push/pull`。
