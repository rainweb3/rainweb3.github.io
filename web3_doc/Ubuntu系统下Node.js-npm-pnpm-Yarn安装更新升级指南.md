# VS Code WSL: Ubuntu 下 Node.js、npm、pnpm、Yarn 安装更新升级指南
本文针对 **VS Code 连接 WSL: Ubuntu** 环境，详细梳理 **Node.js、npm、pnpm、Yarn** 的**卸载、全局安装/升级、项目局部安装/升级**流程，适配 Linux 命令特性，解决权限、版本冲突等常见问题。


## 前言
- **环境前提**：已在 VS Code 中安装「WSL 扩展」并连接 Ubuntu 子系统（终端显示 `user@ubuntu:~$` 即成功）。
- **工具关系**：Node.js 内置 npm；pnpm/Yarn 为替代 npm 的包管理器，可通过 npm 或 Corepack（Node.js 16.13+ 内置）安装。
- **全局 vs 局部**：
  - **全局**：工具安装在 `/home/用户名/.nvm/`（nvm 管理）或 `/usr/local/`，全系统可用。
  - **局部**：工具仅安装在项目 `node_modules/` 中，仅当前项目可用（需通过 `npx` 或项目脚本调用）。
- **推荐工具**：用 **nvm（Node Version Manager）** 管理 Node.js 版本，避免全局权限冲突和版本混乱。


## 一、Node.js 管理（核心依赖）
Ubuntu 系统自带的 Node.js 版本通常较旧，**优先使用 nvm 管理版本**，灵活切换且无权限问题。


### 1. 卸载旧版 Node.js
#### 1.1 卸载“系统自带/APT 安装的 Node.js”
若之前通过 `sudo apt install nodejs` 安装过 Node.js，需先卸载：
```bash
# 1. 卸载 Node.js 和 npm
sudo apt remove --purge nodejs npm
# 2. 清理残留依赖
sudo apt autoremove
# 3. 删除残留配置文件
sudo rm -rf /usr/local/lib/node_modules
sudo rm -rf /home/$USER/.node_modules
# 4. 验证卸载：无输出即成功
node --version
npm --version
```

#### 1.2 卸载“nvm 管理的 Node.js”
无需手动删除目录，通过 nvm 命令直接卸载指定版本：
```bash
# 1. 查看已安装的 Node 版本
nvm list
# 2. 卸载指定版本（如 v20.17.0）
nvm uninstall 20.17.0
```


### 2. 全局安装/升级 Node.js（nvm 推荐）
#### 2.1 安装 nvm（Node 版本管理工具）
1. 安装依赖（curl/git，用于下载 nvm）：
   ```bash
   sudo apt update && sudo apt install -y curl git
   ```
2. 下载并安装 nvm（官方脚本）：
   ```bash
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
   ```
   > 若 GitHub 访问慢，可用国内镜像：
   > ```bash
   > curl -o- https://gitee.com/mirrors/nvm/raw/v0.39.7/install.sh | bash
   > ```
3. 生效 nvm 配置（无需重启终端）：
   ```bash
   # 若使用 bash 终端
   source ~/.bashrc
   # 若使用 zsh 终端（VS Code 默认可能为 zsh）
   source ~/.zshrc
   ```
4. 验证 nvm 安装：
   ```bash
   nvm --version  # 输出 0.39.7 即成功
   ```

#### 2.2 用 nvm 安装/升级 Node.js
```bash
# 1. 查看可安装的 Node 版本（可选）
nvm ls-remote
# 2. 安装指定版本（推荐 LTS 版 v20.17.0 或最新版 v22.17.1）
nvm install 20.17.0
# 3. 切换到已安装版本（全局生效）
nvm use 20.17.0
# 4. 设置默认版本（避免重启终端后版本重置）
nvm alias default 20.17.0
# 5. 验证版本
node --version  # 输出 20.17.0 即成功
npm --version   # 输出对应内置 npm 版本（如 10.8.2）
```


### 3. 项目局部 Node.js 版本（仅当前项目生效）
通过 **.nvmrc 文件** 约束项目的 Node 版本，确保团队开发环境一致：
1. 在项目根目录创建 `.nvmrc` 文件：
   ```bash
   # 写入目标版本（如 20.17.0）
   echo "20.17.0" > .nvmrc
   ```
2. 进入项目目录，自动切换版本：
   ```bash
   cd 你的项目路径
   nvm use  # 读取 .nvmrc 并切换到对应版本（需已通过 nvm 安装）
   ```


## 二、npm 管理（Node.js 内置）
npm 随 Node.js 自动安装，无需单独全局安装，重点关注**升级**和**局部版本控制**。


### 1. 卸载 npm
npm 无法单独卸载（依赖 Node.js），若需重置，可：
1. 升级 Node.js 到最新版（自带最新 npm）；
2. 清理 npm 缓存：
   ```bash
   npm cache clean --force
   ```


### 2. 全局升级 npm
```bash
# 升级 npm 到最新稳定版（nvm 管理的 Node 无需 sudo）
npm install -g npm@latest
# 验证版本
npm --version  # 输出最新版（如 10.9.2）即成功
```


### 3. 项目局部安装/升级 npm
仅在当前项目中使用特定版本的 npm，不影响全局环境：
```bash
# 1. 进入项目目录
cd 你的项目路径
# 2. 局部安装指定版本 npm（写入 devDependencies）
npm install npm@10.8.0 --save-dev
# 3. 验证局部版本（需通过 npx 调用）
npx npm --version  # 输出 10.8.0 即成功
```
> 💡 用法：局部 npm 可通过 `npx npm <命令>` 执行（如 `npx npm install` 安装项目依赖）。


## 三、Yarn 管理（替代 npm 的包管理器）
Yarn 分为 **1.x（Classic）** 和 **2+（Berry）** 系列：1.x 兼容性强，2+ 功能更丰富（推荐通过 Corepack 管理）。


### 1. 卸载 Yarn
#### 1.1 卸载“npm 全局安装的 Yarn 1.x”
```bash
# 卸载全局 Yarn
npm uninstall -g yarn
# 清理残留缓存和配置
rm -rf ~/.yarn
rm -rf ~/.yarnrc
rm -rf ~/.cache/yarn
```

#### 1.2 卸载“Corepack 管理的 Yarn 2+”
```bash
# 禁用 Corepack 对 Yarn 的管理
corepack disable yarn
# 清理 Corepack 缓存
rm -rf ~/.cache/node/corepack
```

#### 1.3 卸载“APT 安装的 Yarn”
若之前通过 `sudo apt install yarn` 安装，需额外执行：
```bash
sudo apt remove --purge yarn
sudo rm -rf /usr/local/bin/yarn
```


### 2. 全局安装/升级 Yarn
#### 2.1 方案 A：npm 安装 Yarn 1.x（兼容性最佳）
```bash
# 1. 全局安装 Yarn 1.x 最新版
npm install -g yarn
# 2. 验证版本
yarn --version  # 输出 1.22.22 即成功
# 3. 升级 Yarn 1.x（如需）
npm upgrade -g yarn
```

#### 2.2 方案 B：Corepack 管理 Yarn 2+（推荐 4.x）
Corepack 是 Node.js 16.13+ 内置工具，无需单独安装：
```bash
# 1. 启用 Corepack（nvm 管理的 Node 无需 sudo）
corepack enable
# 2. 准备并激活 Yarn 4.x 最新版（如 4.9.4）
corepack prepare yarn@4.9.4 --activate
# 3. 全局关联 Yarn（确保命令优先使用 Corepack 版本）
corepack enable yarn
# 4. 验证版本（生效需重启 VS Code 终端）
yarn --version  # 输出 4.9.4 即成功
```

> ⚠️ 网络优化：若下载超时，配置国内镜像：
> ```bash
> yarn config set registry https://registry.npmmirror.com
> # 配置 Yarn 二进制文件镜像（关键）
> yarn config set yarnPath https://registry.npmmirror.com/yarn/v/4.9.4/packages/yarnpkg-cli/bin/yarn.js
> ```


### 3. 项目局部安装/升级 Yarn
#### 3.1 局部安装 Yarn 1.x
```bash
# 1. 进入项目目录
cd 你的项目路径
# 2. 局部安装 Yarn 1.22.22（写入 devDependencies）
npm install yarn@1.22.22 --save-dev
# 3. 验证局部版本
npx yarn --version  # 输出 1.22.22 即成功
```

#### 3.2 局部切换 Yarn 2+（Corepack）
在项目内独立使用 Yarn 4.x，不影响全局版本：
```bash
# 1. 进入项目目录
cd 你的项目路径
# 2. 初始化项目（生成 package.json）
npm init -y
# 3. 用 Corepack 激活项目内 Yarn 4.9.4
corepack prepare yarn@4.9.4 --activate
# 4. 验证局部版本（仅当前项目生效）
yarn --version  # 输出 4.9.4 即成功
```


## 四、pnpm 管理（高性能包管理器）
pnpm 以“磁盘空间占用少、安装速度快”为优势，支持 **npm 安装**、**Corepack 管理** 或 **官方脚本安装**（推荐后者，适配 Linux 环境）。


### 1. 卸载 pnpm
#### 1.1 卸载“npm 全局安装的 pnpm”
```bash
npm uninstall -g pnpm
# 清理残留缓存和配置
rm -rf ~/.pnpm
rm -rf ~/.pnpm-store
rm -rf ~/.config/pnpm
```

#### 1.2 卸载“Corepack 管理的 pnpm”
```bash
corepack disable pnpm
rm -rf ~/.cache/node/corepack
```

#### 1.3 卸载“官方脚本安装的 pnpm”
```bash
# 删除 pnpm 安装目录
rm -rf ~/.local/share/pnpm
# 删除全局命令链接
rm -rf ~/.local/bin/pnpm
```


### 2. 全局安装/升级 pnpm
#### 2.1 方案 A：官方脚本安装（推荐，适配 Linux）
```bash
# 1. 下载并安装 pnpm 最新版（无需 sudo）
curl -fsSL https://get.pnpm.io/install.sh | sh -
# 2. 生效配置（bash 终端）
source ~/.bashrc
# 或 zsh 终端
source ~/.zshrc
# 3. 验证版本
pnpm --version  # 输出最新版（如 9.7.1）即成功
# 4. 升级 pnpm（如需）
pnpm add -g pnpm
```

#### 2.2 方案 B：Corepack 管理 pnpm
```bash
# 1. 启用 Corepack
corepack enable
# 2. 准备并激活 pnpm 9.7.1
corepack prepare pnpm@9.7.1 --activate
# 3. 全局启用 pnpm
corepack enable pnpm
# 4. 验证版本
pnpm --version  # 输出 9.7.1 即成功
```


### 3. 项目局部安装/升级 pnpm
```bash
# 1. 进入项目目录
cd 你的项目路径
# 2. 局部安装 pnpm 9.7.1
npm install pnpm@9.7.1 --save-dev
# 3. 验证局部版本
npx pnpm --version  # 输出 9.7.1 即成功
```


## 五、WSL: Ubuntu 特有注意事项
### 1. 权限问题（避免 sudo 滥用）
- **nvm 管理的 Node**：安装路径在 `~/.nvm/`，无需 `sudo` 即可全局安装工具（避免权限混乱）。
- **权限被拒绝**：若执行命令提示 `EACCES: permission denied`，修复目录权限：
  ```bash
  sudo chown -R $USER:$GROUP ~/.npm
  sudo chown -R $USER:$GROUP ~/.cache
  ```

### 2. VS Code 终端配置
- **默认终端切换**：确保 VS Code 终端连接 WSL（左下角显示 `WSL: Ubuntu`），而非 Windows 终端。
- **nvm 命令不生效**：若重启终端后 `nvm --version` 提示“命令不存在”，手动加载配置：
  ```bash
  source ~/.bashrc  # 或 ~/.zshrc
  ```

### 3. 镜像配置（国内用户必备）
为加速下载，统一配置 npm/pnpm/Yarn 镜像：
```bash
# npm 镜像
npm config set registry https://registry.npmmirror.com
# pnpm 镜像
pnpm config set registry https://registry.npmmirror.com
# Yarn 镜像
yarn config set registry https://registry.npmmirror.com
```


## 六、推荐版本组合表
| Node.js 版本 | npm 版本（内置） | Yarn 推荐版本 | pnpm 推荐版本 |
|--------------|------------------|---------------|---------------|
| v20.17.0（LTS） | 10.8.2           | 1.22.22 / 4.9.4 | 9.7.1         |
| v22.17.1（最新） | 10.9.2           | 1.22.22 / 4.9.4 | 9.7.1         |


## 七、常见问题排查
1. **nvm 安装后命令不生效**：  
   检查 `~/.bashrc` 或 `~/.zshrc` 中是否有 `nvm` 相关配置，若无则手动添加：
   ```bash
   export NVM_DIR="$HOME/.nvm"
   [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
   ```

2. **Corepack 提示“command not found”**：  
   确保 Node.js 版本 ≥16.13.0（`node --version` 验证），旧版本无 Corepack。

3. **pnpm 全局命令无法执行**：  
   检查 `~/.local/bin` 是否在系统 PATH 中，若不在则添加到 `~/.bashrc`：
   ```bash
   export PATH="$HOME/.local/bin:$PATH"
   source ~/.bashrc
   ```


通过以上流程，可在 VS Code WSL: Ubuntu 环境下高效管理 Node.js、npm、pnpm、Yarn 的版本，避免环境冲突，适配区块链开发（如 Hardhat/Truffle）、前端开发等场景。
