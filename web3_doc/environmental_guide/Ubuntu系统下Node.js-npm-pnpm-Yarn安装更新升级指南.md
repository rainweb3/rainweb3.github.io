# VS Code WSL: Ubuntu 下 Node.js、npm、pnpm、Yarn 安装更新升级指南
---
**作者：RainWeb3**  
**更新日期：2025年10月19日**
---
> **本文针对 `VS Code + WSL: Ubuntu` 开发环境**，全面梳理 **Node.js、npm、pnpm、Yarn** 的**卸载、全局安装/升级、项目局部安装/升级**流程，适配 Linux 命令特性，解决权限冲突、版本混乱等常见问题。

---

## 📚 目录

1. [前言](#前言)
   - 环境前提
   - 工具关系说明
   - 全局 vs 局部概念
   - 推荐工具：nvm

2. [一、Node.js 管理（核心依赖）](#一nodejs-管理核心依赖)
   - 1.1 卸载旧版 Node.js（APT 安装）
   - 1.2 卸载 nvm 管理的 Node.js
   - 2.1 安装 nvm（Node 版本管理器）
   - 2.2 使用 nvm 安装/升级 Node.js
   - 3.1 项目级 Node.js 版本控制（`.nvmrc`）

3. [二、npm 管理（Node.js 内置包管理器）](#二npm-管理nodejs-内置)
   - 1. npm 是否需要卸载？
   - 2. 全局升级 npm
   - 3. 项目局部使用特定版本 npm

4. [三、Yarn 管理（替代 npm 的包管理器）](#三yarn-管理替代-npm-的包管理器)
   - 1.1 卸载 npm 安装的 Yarn 1.x
   - 1.2 卸载 Corepack 管理的 Yarn 2+
   - 1.3 卸载 APT 安装的 Yarn
   - 2.1 方案 A：npm 安装 Yarn 1.x（兼容性好）
   - 2.2 方案 B：Corepack 管理 Yarn 4.x（推荐）
   - 3.1 局部安装 Yarn 1.x
   - 3.2 局部切换 Yarn 2+（Corepack）

5. [四、pnpm 管理（高性能包管理器）](#四pnpm-管理高性能包管理器)
   - 1.1 卸载 npm 安装的 pnpm
   - 1.2 卸载 Corepack 管理的 pnpm
   - 1.3 卸载官方脚本安装的 pnpm
   - 2.1 方案 A：官方脚本安装 pnpm（推荐）
   - 2.2 方案 B：Corepack 管理 pnpm
   - 3.1 项目局部安装 pnpm

6. [五、WSL: Ubuntu 特有注意事项](#五wsl-ubuntu-特有注意事项)
   - 权限问题（避免 sudo 滥用）
   - VS Code 终端配置
   - 镜像配置（国内用户必备）

7. [六、推荐版本组合表](#六推荐版本组合表)

8. [七、常见问题排查](#七常见问题排查)

9. [结语](#结语)

---

## 前言

### ✅ 环境前提
- 已在 Windows 上启用 **WSL2** 并安装 **Ubuntu** 子系统。
- 在 **VS Code** 中已安装 **Remote - WSL 扩展**，终端显示类似：
  ```bash
  user@ubuntu:~$
  ```
  表示已成功连接至 WSL 环境。

### 🔧 工具关系说明
| 工具     | 说明 |
|----------|------|
| **Node.js** | JavaScript 运行时，必须首先安装。 |
| **npm**     | Node.js 内置的默认包管理器，随 Node 自动安装。 |
| **Yarn**    | Facebook 推出的替代 npm 的包管理器，更快更可靠。支持 1.x (Classic) 和 2+ (Berry)。 |
| **pnpm**    | 高性能、低磁盘占用的包管理器，基于硬链接机制。 |

> 💡 **Corepack** 是 Node.js 16.13+ 内置的包管理器调度工具，可统一管理 Yarn 和 pnpm 的版本。

### 🌍 全局 vs 局部

| 类型 | 安装位置 | 范围 | 示例命令 |
|------|---------|------|--------|
| **全局** | `~/.nvm/`, `/usr/local/bin`, `~/.local/bin` | 整个系统可用 | `npm install -g yarn` |
| **局部** | 当前项目的 `node_modules/.bin/` | 仅当前项目可用 | `npm install --save-dev pnpm` |

> ⚠️ 推荐：**使用 `npx` 调用局部安装的 CLI 工具**，如 `npx yarn add react`。

### 🛠️ 推荐工具：nvm（Node Version Manager）
强烈建议使用 `nvm` 管理 Node.js 版本，优势包括：
- 无需 `sudo` 安装 Node/npm，避免权限问题；
- 可轻松切换多个 Node.js 版本；
- 支持 `.nvmrc` 文件实现项目级版本约束。

---

## 一、Node.js 管理（核心依赖）

### 1.1 卸载“系统自带/APT 安装的 Node.js”

若之前通过 `apt` 安装过旧版 Node.js，请先彻底卸载：

```bash
# 卸载 nodejs 和 npm
sudo apt remove --purge nodejs npm

# 清理残留依赖
sudo apt autoremove

# 删除可能存在的全局模块目录
sudo rm -rf /usr/local/lib/node_modules
sudo rm -rf /home/$USER/.node_modules

# 验证是否已卸载
node --version  # 应无输出或提示 command not found
npm --version
```

### 1.2 卸载“nvm 管理的 Node.js”

不需要手动删除文件，直接使用 `nvm` 命令操作：

```bash
# 查看已安装的所有 Node 版本
nvm list

# 卸载指定版本（例如 v18.17.0）
nvm uninstall 18.17.0

# 若需重置所有版本（谨慎！）
rm -rf ~/.nvm
```

---

### 2.1 安装 nvm（Node 版本管理工具）

#### 步骤 1：安装基础依赖

```bash
sudo apt update && sudo apt install -y curl git
```

#### 步骤 2：下载并安装 nvm

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

> 🔁 国内用户可用 Gitee 镜像加速：
> ```bash
> curl -o- https://gitee.com/mirrors/nvm/raw/v0.39.7/install.sh | bash
> ```

#### 步骤 3：激活 nvm（无需重启终端）

根据你的 shell 类型执行以下命令：

```bash
# 如果是 bash（默认）
source ~/.bashrc

# 如果是 zsh（常见于新系统）
source ~/.zshrc
```

#### 步骤 4：验证安装

```bash
nvm --version  # 输出 0.39.7 即成功
```

> ❗ 若提示 `command not found`，请参考 [七、常见问题排查](#七常见问题排查) 第一条。

---

### 2.2 使用 nvm 安装/升级 Node.js

```bash
# 查看远程可用版本（可选）
nvm ls-remote | grep "LTS"

# 安装最新 LTS 版本（推荐）
nvm install 20.17.0

# 或安装最新稳定版
nvm install 22.17.1

# 切换到该版本
nvm use 20.17.0

# 设置为默认版本（开机自动加载）
nvm alias default 20.17.0

# 验证安装结果
node --version  # 输出 v20.17.0
npm --version   # 输出对应 npm 版本（如 10.8.2）
```

> ✅ 成功标志：`node` 和 `npm` 命令均可正常调用且版本正确。

---

### 3.1 项目局部 Node.js 版本（通过 `.nvmrc`）

确保团队成员使用一致的 Node.js 版本。

#### 创建 `.nvmrc` 文件

在项目根目录下创建：

```bash
echo "20.17.0" > .nvmrc
```

#### 自动切换版本

进入项目后运行：

```bash
cd /path/to/your/project
nvm use  # 读取 .nvmrc 并切换版本
```

> 💡 提示：可在 `package.json` 中添加脚本简化流程：
> ```json
> "scripts": {
>   "postinstall": "nvm use"
> }
> ```

---

## 二、npm 管理（Node.js 内置）

npm 随 Node.js 自动安装，重点在于**升级**与**局部版本控制**。

---

### 1. npm 是否需要卸载？

❌ **不能单独卸载 npm**，因为它与 Node.js 强耦合。  
✅ 如需“重置”，可通过以下方式：
- 升级 Node.js 至新版（自带新 npm）；
- 或清除缓存：

```bash
npm cache clean --force
```

---

### 2. 全局升级 npm

```bash
# 升级到最新稳定版（无需 sudo）
npm install -g npm@latest

# 或指定版本
npm install -g npm@10.9.2

# 验证
npm --version  # 应输出新版本号
```

> ✅ 注意：由 `nvm` 安装的 Node 不需要 `sudo`，否则可能导致权限错误。

---

### 3. 项目局部安装/升级 npm

让某个项目使用特定版本的 `npm`（不影响全局）：

```bash
cd your-project/

# 局部安装 npm 作为 devDependency
npm install npm@10.8.0 --save-dev

# 使用 npx 调用局部 npm
npx npm --version  # 输出 10.8.0
npx npm install lodash
```

> 💡 场景：测试不同 npm 版本的行为差异。

---

## 三、Yarn 管理（替代 npm 的包管理器）

Yarn 分两个主流系列：
- **1.x（Classic）**：兼容性强，适合老项目；
- **2+（Berry）**：功能丰富，支持 PnP 模式，推荐用于新项目。

---

### 1.1 卸载“npm 全局安装的 Yarn 1.x”

```bash
npm uninstall -g yarn
rm -rf ~/.yarn ~/.yarnrc ~/.cache/yarn
```

---

### 1.2 卸载“Corepack 管理的 Yarn 2+”

```bash
corepack disable yarn
rm -rf ~/.cache/node/corepack
```

---

### 1.3 卸载“APT 安装的 Yarn”

```bash
sudo apt remove --purge yarn
sudo rm -rf /usr/local/bin/yarn
```

---

### 2.1 方案 A：npm 安装 Yarn 1.x（兼容性最佳）

```bash
# 全局安装 Yarn 1.x
npm install -g yarn

# 验证
yarn --version  # 输出 1.22.22

# 升级
npm upgrade -g yarn
```

> ✅ 优点：简单直接；缺点：版本较旧。

---

### 2.2 方案 B：Corepack 管理 Yarn 4.x（推荐）

适用于 Node.js ≥16.13.0。

```bash
# 启用 Corepack
corepack enable

# 准备并激活 Yarn 最新版（如 4.9.4）
corepack prepare yarn@4.9.4 --activate

# 启用全局 yarn 命令
corepack enable yarn

# 验证（可能需重启终端）
yarn --version  # 输出 4.9.4
```

> ⚠️ 若下载慢或超时，设置国内镜像：

```bash
yarn config set registry https://registry.npmmirror.com
yarn config set yarnPath https://registry.npmmirror.com/yarn/v/4.9.4/packages/yarnpkg-cli/bin/yarn.js
```

---

### 3.1 局部安装 Yarn 1.x

```bash
cd your-project/
npm install yarn@1.22.22 --save-dev
npx yarn --version  # 输出 1.22.22
```

---

### 3.2 局部切换 Yarn 2+（Corepack）

在项目中独立使用 Yarn 4.x：

```bash
cd your-project/

# 初始化 package.json（如有则跳过）
npm init -y

# 激活项目专属 Yarn 版本
corepack prepare yarn@4.9.4 --activate

# 验证（当前项目生效）
yarn --version  # 输出 4.9.4
```

> ✅ 效果：此项目将使用 Yarn 4.x，其他项目不受影响。

---

## 四、pnpm 管理（高性能包管理器）

pnpm 以**速度快、节省磁盘空间**著称，特别适合大型 monorepo 项目。

---

### 1.1 卸载“npm 全局安装的 pnpm”

```bash
npm uninstall -g pnpm
rm -rf ~/.pnpm ~/.pnpm-store ~/.config/pnpm
```

---

### 1.2 卸载“Corepack 管理的 pnpm”

```bash
corepack disable pnpm
rm -rf ~/.cache/node/corepack
```

---

### 1.3 卸载“官方脚本安装的 pnpm”

```bash
rm -rf ~/.local/share/pnpm
rm -f ~/.local/bin/pnpm
```

---

### 2.1 方案 A：官方脚本安装 pnpm（推荐）

最干净、最标准的方式：

```bash
# 下载并安装最新版 pnpm
curl -fsSL https://get.pnpm.io/install.sh | sh -

# 生效配置
source ~/.bashrc   # 或 ~/.zshrc

# 验证
pnpm --version  # 输出如 9.7.1

# 升级（后续可用自身命令）
pnpm add -g pnpm
```

> ✅ 优点：自动加入 PATH，无需额外配置。

---

### 2.2 方案 B：Corepack 管理 pnpm

```bash
# 启用 Corepack
corepack enable

# 准备并激活 pnpm
corepack prepare pnpm@9.7.1 --activate

# 启用全局命令
corepack enable pnpm

# 验证
pnpm --version  # 输出 9.7.1
```

---

### 3.1 项目局部安装 pnpm

```bash
cd your-project/
npm install pnpm@9.7.1 --save-dev
npx pnpm --version  # 输出 9.7.1
```

> 💡 用途：CI/CD 中锁定版本，避免全局差异。

---

## 五、WSL: Ubuntu 特有注意事项

### 🔐 权限问题（避免 sudo 滥用）

使用 `nvm` 后，所有工具安装在用户目录（`~/.nvm`），**禁止使用 `sudo` 安装全局包**！

若出现 `EACCES: permission denied` 错误：

```bash
# 修复 npm 缓存权限
sudo chown -R $USER:$GROUP ~/.npm

# 修复通用缓存目录
sudo chown -R $USER:$GROUP ~/.cache
```

---

### 💻 VS Code 终端配置

- 确保左下角状态栏显示：`WSL: Ubuntu`
- 若 `nvm` 命令失效，手动加载配置：
  ```bash
  source ~/.bashrc  # 或 ~/.zshrc
  ```

> 💡 解决方案：在 VS Code 设置中指定默认 shell：
>
> `settings.json`:
> ```json
> "terminal.integrated.shell.linux": "/bin/bash"
> ```

---

### 🌐 镜像配置（国内用户必备）

加速包下载速度，统一设置如下：

```bash
# npm 镜像
npm config set registry https://registry.npmmirror.com

# pnpm 镜像
pnpm config set registry https://registry.npmmirror.com

# Yarn 镜像
yarn config set registry https://registry.npmmirror.com
```

> 📍 镜像源说明：[https://npmmirror.com](https://npmmirror.com) 是淘宝 NPM 镜像（原 cnpm）官方维护站点。

---

## 六、推荐版本组合表

| Node.js 版本       | npm 版本（内置） | Yarn 推荐版本         | pnpm 推荐版本 |
|--------------------|------------------|------------------------|--------------|
| `v20.17.0` (LTS)   | 10.8.2           | `1.22.22` 或 `4.9.4`   | `9.7.1`      |
| `v22.17.1` (Latest)| 10.9.2           | `1.22.22` 或 `4.9.4`   | `9.7.1`      |

> ✅ 建议生产项目使用 **Node.js LTS 版本 + Yarn 4.x / pnpm 9.x**

---

## 七、常见问题排查

### 1. `nvm: command not found`

原因：`.bashrc` 或 `.zshrc` 未正确加载 nvm。

✅ 解决方法：

编辑配置文件：

```bash
nano ~/.bashrc
```

在末尾添加：

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

然后重新加载：

```bash
source ~/.bashrc
```

---

### 2. `corepack: command not found`

原因：Node.js 版本低于 16.13.0。

✅ 解决方法：

升级 Node.js 至 LTS 或更高版本：

```bash
nvm install 20.17.0
nvm use 20.17.0
```

再检查：

```bash
node --version  # 必须 ≥ v16.13.0
corepack --version
```

---

### 3. `pnpm: command not found`（脚本安装后）

原因：`~/.local/bin` 未加入 PATH。

✅ 解决方法：

将路径加入 shell 配置：

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

---

### 4. Yarn 下载超时或失败

✅ 设置国内镜像：

```bash
yarn config set yarnPath https://registry.npmmirror.com/yarn/v/4.9.4/packages/yarnpkg-cli/bin/yarn.js
```

替换版本号为你使用的实际版本。

---

## 结语

本文完整覆盖了在 **VS Code + WSL: Ubuntu** 环境下对 **Node.js、npm、pnpm、Yarn** 的全生命周期管理方案：

- 使用 `nvm` 实现 Node.js 多版本自由切换；
- 利用 `Corepack` 统一管理 Yarn 和 pnpm；
- 区分**全局**与**局部**安装策略，提升灵活性；
- 针对 WSL 环境优化权限和路径配置；
- 提供国内镜像加速方案，提升开发效率。

📌 **最终建议工作流**：

```bash
# 1. 初始化环境
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.bashrc
nvm install 20.17.0
nvm use 20.17.0
nvm alias default 20.17.0

# 2. 启用现代包管理器
corepack enable
corepack prepare yarn@4.9.4 --activate
corepack prepare pnpm@9.7.1 --activate

# 3. 设置镜像
npm config set registry https://registry.npmmirror.com
pnpm config set registry https://registry.npmmirror.com
yarn config set registry https://registry.npmmirror.com
```

从此告别环境混乱，构建高效、稳定的前端/全栈开发环境 ✅
