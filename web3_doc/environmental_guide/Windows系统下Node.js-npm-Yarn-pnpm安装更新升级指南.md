# Windows 系统下 Node.js、npm、Yarn、pnpm 安装更新升级指南（2025年）
---
**作者：RainWeb3**  
---

## 目录

1. [前言](#前言)
   - [工具关系](#工具关系)
   - [全局 vs 局部](#全局-vs-局部)
   - [推荐工具](#推荐工具)

2. [一、Node.js 管理（核心依赖）](#一nodejs-管理核心依赖)
   - [1. 卸载旧版 Node.js](#1-卸载旧版-nodejs)
     - [1.1 卸载“直接安装的 Node.js”（非 nvm 管理）](#11-卸载直接安装的-nodejs非-nvm-管理)
     - [1.2 卸载“nvm-windows 管理的 Node.js”](#12-卸载nvm-windows-管理的-nodejs)
   - [2. 全局安装/升级 Node.js（nvm-windows 推荐）](#2-全局安装升级-nodejs-nvm-windows-推荐)
     - [2.1 安装 nvm-windows（版本管理工具）](#21-安装-nvm-windows版本管理工具)
     - [2.2 用 nvm 安装/升级 Node.js](#22-用-nvm-安装升级-nodejs)
   - [3. 项目局部 Node.js 版本（仅当前项目生效）](#3-项目局部-nodejs-版本仅当前项目生效)

3. [二、npm 管理（Node.js 内置）](#二npm-管理nodejs-内置)
   - [1. 卸载 npm](#1-卸载-npm)
   - [2. 全局升级 npm](#2-全局升级-npm)
   - [3. 项目局部安装/升级 npm](#3-项目局部安装升级-npm)

4. [三、Yarn 管理（替代 npm 的包管理器）](#三yarn-管理替代-npm-的包管理器)
   - [1. 卸载 Yarn](#1-卸载-yarn)
     - [1.1 卸载“npm 全局安装的 Yarn 1.x”](#11-卸载npm-全局安装的-yarn-1x)
     - [1.2 卸载“Corepack 管理的 Yarn 2+”](#12-卸载corepack-管理的-yarn-2)
   - [2. 全局安装/升级 Yarn](#2-全局安装升级-yarn)
     - [2.1 方案 A：npm 安装 Yarn 1.x（兼容性最佳）](#21-方案-a-npm-安装-yarn-1x兼容性最佳)
     - [2.2 方案 B：Corepack 管理 Yarn 2+（推荐 4.x）](#22-方案-b-corepack-管理-yarn-2推荐-4x)
   - [3. 项目局部安装/升级 Yarn](#3-项目局部安装升级-yarn)
     - [3.1 局部安装 Yarn 1.x](#31-局部安装-yarn-1x)
     - [3.2 局部切换 Yarn 2+（Corepack）](#32-局部切换-yarn-2corepack)

5. [四、pnpm 管理（高性能包管理器）](#四pnpm-管理高性能包管理器)
   - [1. 卸载 pnpm](#1-卸载-pnpm)
     - [1.1 卸载“npm 全局安装的 pnpm”](#11-卸载npm-全局安装的-pnpm)
     - [1.2 卸载“Corepack 管理的 pnpm”](#12-卸载corepack-管理的-pnpm)
   - [2. 全局安装/升级 pnpm](#2-全局安装升级-pnpm)
     - [2.1 方案 A：npm 安装 pnpm](#21-方案-a-npm-安装-pnpm)
     - [2.2 方案 B：Corepack 管理 pnpm](#22-方案-b-corepack-管理-pnpm)
   - [3. 项目局部安装/升级 pnpm](#3-项目局部安装升级-pnpm)

6. [五、常见问题与注意事项](#五常见问题与注意事项)
   - [1. 版本冲突：全局 vs 局部](#1-版本冲突全局-vs-局部)
   - [2. 网络超时：配置国内镜像](#2-网络超时配置国内镜像)
   - [3. 命令不生效：重启终端](#3-命令不生效重启终端)
   - [4. 权限问题：管理员身份运行](#4-权限问题管理员身份运行)

7. [六、工具版本对应表（推荐组合）](#六工具版本对应表推荐组合)

---

## 前言

### 工具关系
- **Node.js** 是 JavaScript 运行环境，内置 **npm**。
- **Yarn** 和 **pnpm** 是功能更强大、性能更优的第三方包管理器，可替代 npm。
- 推荐使用 **Corepack**（Node.js 内置）统一管理 Yarn 和 pnpm 的版本。

### 全局 vs 局部
- **全局安装**：通过 `-g` 参数安装，命令可直接在任意路径调用（如 `yarn --version`）。
- **局部安装**：安装在项目 `node_modules/.bin` 目录中，需通过 `npx` 或 `package.json` 脚本调用（如 `npx yarn`）。

### 推荐工具
- 使用 **nvm-windows** 管理 Node.js 多版本，避免版本冲突和卸载残留。
- 使用 **Corepack** 管理 Yarn 和 pnpm，确保项目依赖一致性。

---

## 一、Node.js 管理（核心依赖）

Node.js 是所有工具的运行基础，建议通过 **nvm-windows** 统一管理。

### 1. 卸载旧版 Node.js

#### 1.1 卸载“直接安装的 Node.js”（非 nvm 管理）
1. 打开 **控制面板 → 程序和功能**，找到“Node.js”，右键卸载；
2. 清理残留文件：
   ```cmd
   # 删除 Node 安装目录（默认路径，根据实际安装位置调整）
   rmdir /s /q "C:\Program Files\nodejs"
   # 删除用户级缓存和配置
   rmdir /s /q "%USERPROFILE%\AppData\Roaming\npm"
   rmdir /s /q "%USERPROFILE%\AppData\Roaming\npm-cache"
   rmdir /s /q "%USERPROFILE%\.node-gyp"
   ```
3. 重启 CMD 验证：`node --version` 提示“命令不存在”即成功。

#### 1.2 卸载“nvm-windows 管理的 Node.js”
无需手动卸载，通过 nvm 命令切换/删除版本即可：
```cmd
# 查看已安装的 Node 版本
nvm list
# 删除指定版本（如 22.17.1）
nvm uninstall 22.17.1
```

### 2. 全局安装/升级 Node.js（nvm-windows 推荐）

#### 2.1 安装 nvm-windows（版本管理工具）
1. 下载最新版 nvm-windows：[GitHub Releases](https://github.com/coreybutler/nvm-windows/releases)（选择 `nvm-setup.exe`）；
2. 双击安装，按提示选择安装路径（建议默认 `C:\Users\你的用户名\AppData\Roaming\nvm`）；
3. 验证安装：
   ```cmd
   nvm version  # 输出 nvm 版本即成功
   ```

#### 2.2 用 nvm 安装/升级 Node.js
```cmd
# 1. 查看可安装的 Node 版本（可选）
nvm list available
# 2. 安装指定版本（如 LTS 版 20.17.0 或最新版 22.17.1）
nvm install 20.17.0
# 3. 切换到已安装版本（全局生效）
nvm use 20.17.0
# 4. 验证版本
node --version  # 输出 20.17.0 即成功
```

> ⚠️ 注意：切换版本时需以 **管理员身份运行 CMD**，否则可能提示“权限不足”。

### 3. 项目局部 Node.js 版本（仅当前项目生效）

Node.js 通常不“局部安装”，但可通过 **`.nvmrc` 文件** 约束项目的 Node 版本：
1. 在项目根目录创建 `.nvmrc` 文件，写入目标版本：
   ```txt
   20.17.0  # 项目要求的 Node 版本
   ```
2. 进入项目目录，自动切换版本：
   ```cmd
   nvm use  # 会读取 .nvmrc 并切换到对应版本
   ```

---

## 二、npm 管理（Node.js 内置）

npm 随 Node.js 自动安装，无法单独卸载，重点关注 **升级** 和 **局部版本控制**。

### 1. 卸载 npm
npm 无法单独卸载（依赖 Node.js），若需重置 npm，可：
1. 升级 Node.js 到最新版（会自带最新 npm）；
2. 清理 npm 缓存：
   ```cmd
   npm cache clean --force
   ```

### 2. 全局升级 npm
```cmd
# 升级 npm 到最新稳定版
npm install -g npm@latest
# 验证版本
npm --version  # 输出最新版（如 10.9.2）即成功
```

### 3. 项目局部安装/升级 npm
仅在当前项目中使用特定版本的 npm（避免影响全局）：
```cmd
# 1. 进入项目目录
cd 你的项目路径
# 2. 局部安装指定版本 npm（写入 devDependencies）
npm install npm@10.8.0 --save-dev
# 3. 验证局部版本（需通过 npx 调用）
npx npm --version  # 输出 10.8.0 即成功
```

> 💡 用法：局部 npm 可通过 `npx npm <命令>` 执行（如 `npx npm install` 安装项目依赖）。

---

## 三、Yarn 管理（替代 npm 的包管理器）

Yarn 分为 **1.x（Classic）** 和 **2+（Berry）** 两个系列：
- **1.x**：稳定、兼容性好，可通过 npm 安装；
- **2+**：性能更强、功能更丰富，推荐通过 **Corepack** 管理。

### 1. 卸载 Yarn

#### 1.1 卸载“npm 全局安装的 Yarn 1.x”
```cmd
npm uninstall -g yarn
# 清理残留缓存
rmdir /s /q "%USERPROFILE%\AppData\Local\Yarn"
rmdir /s /q "%USERPROFILE%\AppData\Roaming\Yarn"
```

#### 1.2 卸载“Corepack 管理的 Yarn 2+”
```cmd
# 禁用 Corepack 对 Yarn 的管理
corepack disable yarn
# 清理 Corepack 缓存
rmdir /s /q "%USERPROFILE%\.cache\node\corepack"
```

### 2. 全局安装/升级 Yarn

#### 2.1 方案 A：npm 安装 Yarn 1.x（兼容性最佳）
```cmd
# 1. 全局安装 Yarn 1.x 最新版
npm install -g yarn
# 2. 验证版本
yarn --version  # 输出 1.22.22 即成功
# 3. 升级 Yarn 1.x（如需）
npm upgrade -g yarn
```

#### 2.2 方案 B：Corepack 管理 Yarn 2+（推荐 4.x）
Corepack 是 Node.js 16.13+ 内置的包管理器管理工具，无需单独安装：
```cmd
# 1. 启用 Corepack
corepack enable
# 2. 准备并激活 Yarn 4.x 最新版（如 4.9.4）
corepack prepare yarn@4.9.4 --activate
# 3. 全局启用 Yarn
corepack enable yarn
# 4. 验证版本（重启 CMD 后）
yarn --version  # 输出 4.9.4 即成功
```

> ⚠️ 注意：若提示“网络超时”，配置国内镜像：
> ```cmd
> yarn config set registry https://registry.npmmirror.com
> ```

### 3. 项目局部安装/升级 Yarn

#### 3.1 局部安装 Yarn 1.x
```cmd
# 1. 进入项目目录
cd 你的项目路径
# 2. 局部安装 Yarn 1.22.22
npm install yarn@1.22.22 --save-dev
# 3. 验证局部版本
npx yarn --version  # 输出 1.22.22 即成功
```

#### 3.2 局部切换 Yarn 2+（Corepack）
在项目内独立使用 Yarn 4.x，不影响全局：
```cmd
# 1. 进入项目目录
cd 你的项目路径
# 2. 初始化项目（生成 package.json）
npm init -y
# 3. 用 Corepack 激活项目内 Yarn 4.9.4
corepack prepare yarn@4.9.4 --activate
# 4. 验证局部版本
yarn --version  # 输出 4.9.4 即成功（仅当前项目生效）
```

---

## 四、pnpm 管理（高性能包管理器）

pnpm 以“节省磁盘空间、快速安装”为特点，支持硬链接和符号链接，推荐使用。

### 1. 卸载 pnpm

#### 1.1 卸载“npm 全局安装的 pnpm”
```cmd
npm uninstall -g pnpm
# 清理残留缓存
rmdir /s /q "%USERPROFILE%\.pnpm-store"
rmdir /s /q "%USERPROFILE%\AppData\Local\pnpm"
```

#### 1.2 卸载“Corepack 管理的 pnpm”
```cmd
corepack disable pnpm
rmdir /s /q "%USERPROFILE%\.cache\node\corepack"
```

### 2. 全局安装/升级 pnpm

#### 2.1 方案 A：npm 安装 pnpm
```cmd
# 1. 全局安装 pnpm 最新版
npm install -g pnpm
# 2. 验证版本
pnpm --version  # 输出最新版（如 9.7.1）即成功
# 3. 升级 pnpm
pnpm add -g pnpm
```

#### 2.2 方案 B：Corepack 管理 pnpm
```cmd
# 1. 启用 Corepack
corepack enable
# 2. 准备并激活 pnpm 9.7.1
corepack prepare pnpm@9.7.1 --activate
# 3. 全局启用 pnpm
corepack enable pnpm
# 4. 验证版本（重启 CMD 后）
pnpm --version  # 输出 9.7.1 即成功
```

### 3. 项目局部安装/升级 pnpm
```cmd
# 1. 进入项目目录
cd 你的项目路径
# 2. 局部安装 pnpm 9.7.1
npm install pnpm@9.7.1 --save-dev
# 3. 验证局部版本
npx pnpm --version  # 输出 9.7.1 即成功
```

---

## 五、常见问题与注意事项

### 1. 版本冲突：全局 vs 局部
- 优先通过 `npx <工具名> --version` 确认局部版本，避免全局版本干扰；
- 例：`npx yarn --version` 显示项目局部 Yarn 版本，`yarn --version` 显示全局版本。

### 2. 网络超时：配置国内镜像
```cmd
# npm 镜像
npm config set registry https://registry.npmmirror.com
# Yarn 镜像
yarn config set registry https://registry.npmmirror.com
# pnpm 镜像
pnpm config set registry https://registry.npmmirror.com
```

### 3. 命令不生效：重启终端
安装/升级后需 **关闭当前 CMD 并重新打开**，确保环境变量更新。

### 4. 权限问题：管理员身份运行
执行 `nvm use`、`corepack enable` 等命令时，需以 **管理员身份运行 CMD**（右键 CMD → 以管理员身份运行）。

---

## 六、工具版本对应表（推荐组合）

| Node.js 版本       | npm 版本（内置） | Yarn 推荐版本               | pnpm 推荐版本 |
|--------------------|------------------|-----------------------------|---------------|
| 20.17.0（LTS）     | 10.8.2           | 1.22.22 / 4.9.4             | 9.7.1         |
| 22.17.1（最新）    | 10.9.2           | 1.22.22 / 4.9.4             | 9.7.1         |

---

✅ **总结**：通过 **nvm-windows** 管理 Node.js 版本，**Corepack** 管理 Yarn 和 pnpm，结合局部安装策略，可实现 Windows 系统下前端工具链的灵活、稳定、无冲突管理，适用于多项目、多团队协作开发场景。
