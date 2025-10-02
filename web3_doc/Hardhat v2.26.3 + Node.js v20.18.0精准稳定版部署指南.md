# **Hardhat 智能合约开发环境搭建与配置指南（基于 Hardhat v2.26.3 + Node.js v20.18.0 精准稳定版）**

> **版本：v4.8.0**  
> **最后更新时间：2025年10月3日**
> **目标：规避 `hardhat@3.x` 的兼容性问题，精准锁定稳定版 `v2.26.3`，确保在 Node.js v20.18.0 环境下构建无冲突、可部署、可维护的 TypeScript 项目。**

---
快速上手-可访问下载此模版项目：[v2.26.3 hardhat-project-template](https://github.com/rainweb3/hardhat-project-template/tree/main)
下载后-先执行：npm run reinstall（安装node_modules）
---
## 📚 目录（点击跳转）

- [🔧 一、环境准备（Node.js v20.18.0 稳定适配）](#-一环境准备nodejs-v20180-稳定适配)
  - [✅ 1.1 前提条件](#-11-前提条件)
- [🛠 二、初始化项目结构（强制使用 `hardhat@2.26.3`）](#-二初始化项目结构强制使用-hardhat2263)
  - [✅ 2.1 创建项目并初始化](#-21-创建项目并初始化)
  - [✅ 2.2 安装 Hardhat 稳定版本 v2.26.3（关键步骤）](#-22-安装-hardhat-稳定版本-v2263关键步骤)
  - [交互式选项详解（逐行说明）](#交互式选项详解逐行说明)
- [📁 三、项目结构演变：从原生生成到完整配置](#-三项目结构演变从原生生成到完整配置)
  - [✅ 3.1 初始生成的项目结构（执行 `npx hardhat init` 后）](#-31-初始生成的项目结构执行-npx-hardhat-init-后)
  - [✅ 3.2 完整项目结构（添加所有配置文件后）](#-32-完整项目结构添加所有配置文件后)
  - [✅ 3.3 新增/修改的配置文件说明（对比初始结构）](#-33-新增修改的配置文件说明对比初始结构)
- [⚙️ 四、修复 Ignition 导入错误与 Solidity 编译器版本不匹配问题](#-四修复-ignition-导入错误与-solidity-编译器版本不匹配问题)
  - [🚫 4.0 **`hardhat@2.26.3` 不支持 Ignition：原因与替代方案详解**](#-40-hardhat2263-不支持-ignition原因与替代方案详解)
  - [✅ 4.1 移除 Ignition 相关代码（`hardhat@2.26.3` 不支持）](#-41-移除-ignition-相关代码hardhat2263-不支持)
  - [✅ 4.2 修正 `hardhat.config.ts` 中的 Solidity 编译器版本](#-42-修正-hardhatconfigts-中的-solidity-编译器版本)
  - [✅ 4.3 更新 `package.json` 脚本（移除 Ignition 相关）](#-43-更新-packagejson-脚本移除-ignition-相关)
- [🧩 五、依赖版本对齐（精确锁定，避免 `3.x` 升级）](#-五依赖版本对齐精确锁定避免-3x-升级)
  - [✅ 5.1 清理并替换 `package.json`](#-51-清理并替换-packagejson)
  - [✅ 5.2 使用更新后的 `package.json`（项目名已更改为 `hardhat-project`）](#-52-使用更新后的-packagejson项目名已更改为-hardhat-project)
  - [✅ 5.3 安装最终依赖](#-53-安装最终依赖)
- [⚙️ 六、新增与修改的配置文件详解](#-六新增与修改的配置文件详解)
  - [✅ 6.1 `.prettierrc`（新增）](#-61-prettierrc新增)
  - [✅ 6.2 `eslint.config.js`（新增）](#-62-eslintconfigjs新增)
  - [✅ 6.3 `.editorconfig`（新增）](#-63-editorconfig新增)
  - [✅ 6.4 `.env`（新增）](#-64-env新增)
  - [✅ 6.5 `.gitignore`（增强）](#-65-gitignore增强)
  - [✅ 6.6 `.vscode/settings.json`（增强）](#-66-vscode-settingsjson增强)
- [⚠️ 七、修复 VS Code TypeScript 编译器错误（`tsconfig.json` 问题）](#-七修复-vs-code-typescript-编译器错误tsconfigjson-问题)
  - [🚫 7.0 问题描述](#-70-问题描述)
  - [🔍 7.1 根本原因分析](#-71-根本原因分析)
  - [✅ 7.2 推荐解决方案（无需修改源码）](#-72-推荐解决方案无需修改源码)
  - [✅ 7.3 替代方案（修改 `tsconfig.json`，不推荐）](#-73-替代方案修改-tsconfigjson不推荐)
- [🧪 八、验证环境（确保 `v2.26.3` 正常工作）](#-八验证环境确保-v2263-正常工作)
  - [✅ 8.1 编译合约](#-81-编译合约)
  - [✅ 8.2 运行测试](#-82-运行测试)
  - [✅ 8.3 启动本地节点](#-83-启动本地节点)
  - [✅ 8.4 部署测试](#-84-部署测试)
- [📦 九、智能合约开发常用命令汇总（含 ts-node 执行）](#-九智能合约开发常用命令汇总含-ts-node-执行)
- [⚠️ 十、安全审计问题说明与处理建议](#-十安全审计问题说明与处理建议)
- [✅ 十一、结论](#-十一结论)
-[📄 Hardhat 单文件编译指南](# 📄 Hardhat 单文件编译指南)
-[📚 Hardhat 项目中 ESLint & Prettier 集成问题全记录](📚 Hardhat 项目中 ESLint & Prettier 集成问题全记录)
---

## 🔧 一、环境准备（Node.js v20.18.0 稳定适配）

### ✅ 1.1 前提条件

- **Node.js**：`v20.18.0`（LTS，推荐）
- **npm**：`v10.8.2`（随 Node.js v20 安装）
- **Git**（可选）
- **文本编辑器**：推荐使用 **VS Code**

> ⚠️ **重要提醒**：`hardhat@3.x` 版本（如 `3.0.4`）为**早期主版本**，存在 API 变更、插件不兼容、文档缺失等问题，**不建议在生产或学习项目中使用**。

```bash
# 检查版本
node -v  # 预期输出：v20.18.0
npm -v   # 预期输出：10.8.2
```

> 💡 若版本不符，请使用 [nvm](https://github.com/nvm-sh/nvm) 切换：

```bash
# 安装 nvm（Linux/macOS）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# 重启终端后
nvm install 20.18.0
nvm use 20.18.0
nvm alias default 20.18.0
```

---

## 🛠 二、初始化项目结构（强制使用 `hardhat@2.26.3`）

### ✅ 2.1 创建项目并初始化

```bash
mkdir hardhat-project
cd hardhat-project
npm init -y
```

---

### ✅ 2.2 安装 Hardhat **稳定版本 v2.26.3**（关键步骤）

> ⚠️ **切勿使用 `npm install --save-dev hardhat`**，否则会安装最新的 `3.0.4`，该版本存在严重兼容性问题。

```bash
npm install --save-dev hardhat@2.26.3
npx hardhat init
```

---

#### 交互式选项详解（逐行说明）：

```
? What do you want to do? 
  Create a JavaScript project
> Create a TypeScript project
  Create a TypeScript project (with Viem)
  Create an empty hardhat.config.js
  Quit
```

✅ **选择：`Create a TypeScript project`**

> **原因**：本人的配置使用 `ethers` 而非 `viem`，选择此项将生成基于 `ethers` 和 `chai` 的标准 TypeScript 项目结构。

---

```
? Hardhat project root: /your/path/hardhat-project
```

✅ 回车确认默认路径。

---

```
? Do you want to add a .gitignore? (Y/n)
```

✅ 输入 `Y`，自动创建 `.gitignore`。

---

```
? Do you want to install this sample project's dependencies with npm (@nomicfoundation/hardhat-toolbox)? (Y/n) »
```

✅ **输入 `y`**

> **选项说明**：
> - 此选项询问是否自动安装 `@nomicfoundation/hardhat-toolbox` 及其依赖（如 `eslint`, `prettier`, `typescript`, `chai`, `ethers` 等）。
> - ✅ **选择 `y`**：自动安装推荐的开发依赖，节省手动配置时间，且版本与 `hardhat@2.26.3` 兼容。
> - ❌ 选择 `n`：需手动安装所有依赖，容易出错且耗时。
> - **注意**：即使选择 `y`，后续仍需用本人提供的 `package.json` 替换以锁定精确版本。

---

> ⚠️ **关于安装过程中的警告说明**
>
> 执行 `npm install` 时可能出现：
>
> ```bash
> npm warn deprecated glob@8.1.0: Glob versions prior to v9 are no longer supported
> npm warn deprecated eslint@8.57.1: This version is no longer supported...
> ```
>
> **结论**：这些是**非阻塞性警告**，源于 `hardhat-toolbox` 对旧版 `eslint` 的依赖。`hardhat@2.26.3 + toolbox@^4.0.0 + eslint@^8.57.0` 是当前最稳定的组合，**无需修复**。

---

## 📁 三、项目结构演变：从原生生成到完整配置

本节清晰区分 **初始生成结构** 与 **最终完整结构**，并说明新增/修改项。

### ✅ 3.1 初始生成的项目结构（执行 `npx hardhat init` 后）

执行 `npx hardhat init` 并选择 `Create a TypeScript project` 后，Hardhat 自动生成的项目结构如下：

```
hardhat-project/
├── contracts/                 # 存放 Solidity 智能合约源代码（.sol 文件）
│   └── Lock.sol               # 示例合约：带时间锁的转账合约
├── scripts/                   # 存放自定义部署或交互脚本（.ts 文件）
│   └── deploy.ts              # 示例：部署合约到指定网络的脚本
├── test/                      # 存放合约测试用例（.ts 文件）
│   └── Lock.ts                # 示例：对 Lock 合约进行单元测试
├── node_modules/              # npm 安装的所有依赖包（自动生成）
├── .gitignore                 # Git 忽略文件规则（已包含基础规则）
├── hardhat.config.ts          # Hardhat 主配置文件（网络、Solidity 版本等）
├── package-lock.json          # npm 依赖锁定文件（自动生成）
├── package.json               # 项目元信息与依赖声明
├── README.md                  # 项目说明文档
└── tsconfig.json              # TypeScript 编译配置
```

> ⚠️ **此时缺少**：`.prettierrc`, `eslint.config.js`, `.editorconfig`, `.env`, `.vscode/settings.json` 等高级配置文件。

---

### ✅ 3.2 完整项目结构（添加所有配置文件后）

在添加所有配置文件和环境变量后，最终的项目结构如下：

```
hardhat-project/
├── contracts/                 # 存放 Solidity 智能合约源代码（.sol 文件）
│   └── Lock.sol               # 示例合约：带时间锁的转账合约
├── scripts/                   # 存放自定义部署或交互脚本（.ts 文件）
│   └── deploy.ts              # 示例：部署合约到指定网络的脚本
├── test/                      # 存放合约测试用例（.ts 文件）
│   └── Lock.ts                # 示例：对 Lock 合约进行单元测试
├── node_modules/              # npm 安装的所有依赖包（自动生成）
├── .vscode/                   # VS Code 编辑器配置
│   └── settings.json          # 编辑器格式化、保存等行为配置
├── .env                       # 环境变量文件（本地开发使用，不应提交到 Git）
├── .env.local                 # 本地环境变量覆盖文件（Git 忽略）
├── encryptedKey.json          # 加密的私钥文件（Git 忽略）
├── .editorconfig              # 统一编辑器缩进、换行等风格
├── .eslint.config.js          # ESLint 代码规范检查配置
├── .gitignore                 # Git 忽略文件规则
├── .prettierrc                # Prettier 代码格式化配置
├── hardhat.config.ts          # Hardhat 主配置文件（网络、Solidity 版本等）
├── package-lock.json          # npm 依赖锁定文件（自动生成）
├── package.json               # 项目元信息与依赖声明
├── README.md                  # 项目说明文档
└── tsconfig.json              # TypeScript 编译配置
```

> ✅ **变化说明**：
> - 已**移除 `ignition/` 目录**，因为 `hardhat@2.26.3` 不支持 `hardhat-ignition`。
> - 新增 `.vscode/`, `.prettierrc`, `.eslint.config.js`, `.editorconfig`, `.env` 等配置文件。
> - `.gitignore` 内容被增强，以忽略更多文件。

---

### ✅ 3.3 新增/修改的配置文件说明（对比初始结构）

| 文件/目录 | 类型 | 作用说明 |
|----------|------|----------|
| `.prettierrc` | 新增 | Prettier 格式化配置，统一代码风格 |
| `eslint.config.js` | 新增 | ESLint 代码规范检查配置，集成 Prettier，确保代码质量 |
| `.editorconfig` | 新增 | 统一不同编辑器的缩进、换行等基础风格 |
| `.env` | 新增 | 存放私钥、RPC URL 等敏感环境变量 |
| `.env.local` | 新增 | 本地环境变量覆盖，优先级高于 `.env` |
| `encryptedKey.json` | 新增 | 存放加密后的私钥文件 |
| `.vscode/settings.json` | 新增 | VS Code 编辑器行为配置，实现保存时自动格式化与修复 |
| `.gitignore` | 修改 | 在初始基础上增强，新增忽略 `dist/`, `cache/`, `coverage/`, `encryptedKey.json`, `.env.*` 等 |

---

## ⚙️ 四、修复 Ignition 导入错误与 Solidity 编译器版本不匹配问题

> 🛠 **问题描述**：
>
> 1. `import { buildModule } from "@nomicfoundation/hardhat-ignition/modules";` 报错：找不到模块。
> 2. `npx hardhat compile` 报错：`Error HH606: The project cannot be compiled`，Solidity 版本 `^0.8.28` 与配置不匹配。
>
> **根本原因**：
> - `hardhat@2.26.3` **不支持** `hardhat-ignition`，该功能是 `hardhat@3.x` 的实验性功能。
> - `hardhat-toolbox` 默认配置的 Solidity 编译器版本可能不包含 `0.8.28`。

---

### 🚫 4.0 **`hardhat@2.26.3` 不支持 Ignition：原因与替代方案详解**

> **⚠️ 核心原因说明**：
>
> `@nomicfoundation/hardhat-ignition` 是 Hardhat 团队为 **Hardhat v3.x 系列**开发的**全新、实验性**的模块化部署系统。它旨在简化复杂合约系统的部署流程，提供更强大的依赖管理和部署状态追踪能力。
>
> **`hardhat@2.26.3`（属于 v2.x 系列）发布于 Hardhat Ignition 功能之前，其代码库中完全不包含 `hardhat-ignition` 模块。** 因此，尝试在 `v2.26.3` 项目中导入 `@nomicfoundation/hardhat-ignition/modules` 会直接导致模块解析失败，报错：
>
> ```
> Error: Cannot find module '@nomicfoundation/hardhat-ignition/modules'
> ```
>
> **这不是配置错误，而是版本不兼容的必然结果。**
>
> **📌 重要提示**：即使本人手动通过 `npm install @nomicfoundation/hardhat-ignition` 安装该插件，它也无法在 `v2.26.3` 上正常工作，因为其底层依赖和 API 与 v2.x 不兼容。

---

#### ✅ **替代方案与操作步骤**

由于 `hardhat@2.26.3` 不支持 Ignition，必须使用 **传统的部署方式**，即通过 Hardhat Runtime Environment (HRE) 中的 `ethers` 插件来手动部署合约。

**替代方案：使用 `ethers.deployContract()`**

这是 Hardhat v2.x 的标准部署方法，稳定、可靠、文档完善。

**操作步骤如下**：

1. **删除 `ignition/` 目录**（如果存在）：
   ```bash
   rm -rf ignition
   ```

2. **修改或创建部署脚本**（如 `scripts/deploy.ts`），使用 `ethers.deployContract` 代替 `buildModule`：

   ```ts
   import { ethers } from "hardhat";

   async function main() {
     // 部署 Lock 合约，构造函数参数为解锁时间戳
     const unlockTime = Math.floor(Date.now() / 1000) + 60; // 60秒后解锁
     const lockedAmount = ethers.parseEther("1"); // 锁定1 ETH

     const lock = await ethers.deployContract("Lock", [unlockTime], {
       value: lockedAmount
     });

     await lock.waitForDeployment();
     console.log(`Lock contract deployed to ${lock.target}`);
   }

   main()
     .then(() => process.exit(0))
     .catch((error) => {
       console.error(error);
       process.exit(1);
     });
   ```

   > ✅ **说明**：
   > - `ethers.deployContract("Lock", [unlockTime], { value: lockedAmount })`：直接部署合约，传入构造函数参数和交易选项（如 `value`）。
   > - `await lock.waitForDeployment()`：等待部署交易上链并确认。
   > - `lock.target`：获取部署后的合约地址。

3. **无需修改 `hardhat.config.ts`** 来支持此方案，`ethers` 插件已由 `@nomicfoundation/hardhat-toolbox` 自动加载。

4. **更新 `package.json` 脚本**，移除 `deploy:ignition` 等相关命令，仅保留基于 `scripts/` 的部署命令。

---

### ✅ 4.1 移除 Ignition 相关代码（`hardhat@2.26.3` 不支持）

> **（此节为 4.0 节的实践总结）**

1. **删除 `ignition/` 目录**（如果存在）：
   ```bash
   rm -rf ignition
   ```

2. **修改 `scripts/deploy.ts`**（如果引用了 `buildModule`）：
   ```ts
   import { ethers } from "hardhat";

   async function main() {
     const lock = await ethers.deployContract("Lock", [Date.now() + 1000]);
     await lock.waitForDeployment();
     console.log(`Lock deployed to ${lock.target}`);
   }

   main().catch((error) => {
     console.error(error);
     process.exitCode = 1;
   });
   ```

   > ✅ **说明**：使用 `ethers.deployContract` 代替 `buildModule`，这是 `hardhat@2.x` 的标准部署方式。

---

### ✅ 4.2 修正 `hardhat.config.ts` 中的 Solidity 编译器版本

确保 `hardhat.config.ts` 中的 `solidity` 配置包含 `0.8.28`：

```ts
import { HardhatUserConfig } from "hardhat/config";
import "@nomicfoundation/hardhat-toolbox";

const config: HardhatUserConfig = {
  solidity: {
    version: "0.8.28",
    settings: {
      optimizer: {
        enabled: true,
        runs: 200,
      },
    },
  },
  networks: {
    hardhat: {
      chainId: 1337,
    },
  },
  paths: {
    sources: "./contracts",
    tests: "./test",
    cache: "./cache",
    artifacts: "./artifacts"
  },
  mocha: {
    timeout: 40000
  }
};

export default config;
```

> ✅ **关键点**：
> - 明确设置 `version: "0.8.28"`，与 `Lock.sol` 中的 `pragma solidity ^0.8.28;` 匹配。
> - `hardhat-toolbox` 支持 `0.8.28`，无需额外安装编译器。

---

### ✅ 4.3 更新 `package.json` 脚本（移除 Ignition 相关）

确保 `package.json` 中的脚本不包含 `ignition` 相关命令：

```json
"scripts": {
  "ts-node": "npx ts-node",
  "build": "tsc",
  "compile": "hardhat compile",
  "test": "hardhat test",
  "node": "hardhat node",
  "deploy:local": "hardhat run scripts/deploy.ts --network localhost",
  "deploy:sepolia": "hardhat run scripts/deploy.ts --network sepolia",
  "clean": "npx rimraf node_modules package-lock.json dist artifacts cache coverage types",
  "outdated": "npm outdated",
  "format": "prettier --plugin prettier-plugin-solidity --write \"**/*.{js,cjs,ts,sol}\"",
  "format:check": "prettier --plugin prettier-plugin-solidity --check \"**/*.{js,cjs,ts,sol}\"",
  "lint": "eslint \"**/*.{ts,js,sol}\" --ext .ts,.js",
  "lint:fix": "eslint \"**/*.{ts,js,sol}\" --ext .ts,.js --fix",
  "reinstall": "npm run clean && npm install"
}
```

> ✅ **说明**：已移除 `deploy:ignition` 等不适用于 `hardhat@2.26.3` 的脚本。

---

## 🧩 五、依赖版本对齐（精确锁定，避免 `3.x` 升级）

### ✅ 5.1 清理并替换 `package.json`

```bash
rm -rf node_modules package-lock.json
```

### ✅ 5.2 使用更新后的 `package.json`（项目名已更改为 `hardhat-project`）

```json
{
  "name": "hardhat-project",
  "version": "1.0.0",
  "main": "hardhat.config.js",
  "scripts": {
    "ts-node": "npx ts-node",
    "build": "tsc",
    "compile": "hardhat compile",
    "test": "hardhat test",
    "node": "hardhat node",
    "deploy:local": "hardhat run scripts/deploy.ts --network localhost",
    "deploy:sepolia": "hardhat run scripts/deploy.ts --network sepolia",
    "clean": "npx rimraf node_modules package-lock.json dist artifacts cache coverage types",
    "outdated": "npm outdated",
    "format": "prettier --plugin prettier-plugin-solidity --write \"**/*.{js,cjs,ts,sol}\"",
    "format:check": "prettier --plugin prettier-plugin-solidity --check \"**/*.{js,cjs,ts,sol}\"",
    "lint": "eslint \"**/*.{ts,js,sol}\" --ext .ts,.js",
    "lint:fix": "eslint \"**/*.{ts,js,sol}\" --ext .ts,.js --fix",
    "reinstall": "npm run clean && npm install"
  },
  "devDependencies": {
    "@nomicfoundation/hardhat-toolbox": "^4.0.0",
    "@types/chai": "^4.3.10",
    "@types/fs-extra": "^11.0.4",
    "@types/glob": "^8.1.0",
    "@types/minimatch": "^5.1.2",
    "@types/node": "^20.19.13",
    "@typescript-eslint/eslint-plugin": "^6.21.0",
    "@typescript-eslint/parser": "^6.21.0",
    "chai": "^4.3.10",
    "dotenv": "^16.4.5",
    "eslint": "^8.57.0",
    "eslint-config-prettier": "^9.1.0",
    "eslint-plugin-prettier": "^5.1.3",
    "ethers": "^6.15.0",
    "forge-std": "github:foundry-rs/forge-std#v1.9.4",
    "hardhat": "^2.26.3",
    "minimatch": "^9.0.3",
    "prettier": "^3.2.5",
    "prettier-plugin-solidity": "^1.1.3",
    "ts-node": "^10.9.2",
    "typescript": "~5.8.3"
  },
  "overrides": {
    "globby": {
      "@types/glob": "$@types/glob"
    },
    "minimatch": {
      "@types/minimatch": "5.1.2"
    }
  },
  "dependencies": {
    "crypto-js": "^4.2.0"
  },
  "description": "This project showcases a Hardhat 3 Beta project using `mocha` for tests and the `ethers` library for Ethereum interactions.",
  "directories": {
    "test": "test"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

> ✅ **关键点**：
> - `"name": "hardhat-project"`：项目名称已按要求更新。
> - `"hardhat": "^2.26.3"`：明确锁定 `2.x` 分支，避免意外升级到 `3.x`。
> - `"@nomicfoundation/hardhat-toolbox": "^4.0.0"`：与 `hardhat@2.26.3` 完全兼容。
> - `"eslint": "^8.57.0"`：`toolbox` 所需，不可升级至 v9。

---

### ✅ 5.3 安装最终依赖

```bash
npm install
```

> ⚠️ 安装后可能出现 `13 low severity vulnerabilities`，请参见下方 **[十、安全审计问题说明与处理建议](#-十安全审计问题说明与处理建议)**。

---

## ⚙️ 六、新增与修改的配置文件详解

### ✅ 6.1 `.prettierrc`（新增）

```json
{
  "semi": false,
  "singleQuote": false,
  "printWidth": 80,
  "tabWidth": 4,
  "useTabs": false,
  "endOfLine": "lf",
  "arrowParens": "avoid",
  "plugins": ["prettier-plugin-solidity"]
}
```

> **作用**：定义代码格式化规则。
> - `semi: false`：不使用分号结尾。
> - `singleQuote: false`：使用双引号。
> - `printWidth: 80`：每行最大 80 字符。
> - `tabWidth: 4`：缩进 4 空格。
> - `useTabs: false`：使用空格缩进。
> - `endOfLine: "lf"`：使用 LF 换行符（Unix 风格）。
> - `arrowParens: "avoid"`：箭头函数参数为单个时省略括号。
> - `plugins: ["prettier-plugin-solidity"]`：支持 Solidity 格式化。

---

### ✅ 6.2 `eslint.config.js`（新增）

```js
module.exports = {
  env: {
    es2021: true,
    node: true,
  },
  extends: [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "prettier"
  ],
  parser: "@typescript-eslint/parser",
  parserOptions: {
    ecmaVersion: 12,
    sourceType: "module"
  },
  plugins: ["@typescript-eslint", "prettier"],
  rules: {
    "prettier/prettier": "error",
    "no-console": "off",
    "no-debugger": "warn"
  },
  ignorePatterns: ["node_modules/", "dist/"]
};
```

> **作用**：ESLint 代码检查配置。
> - 继承 `eslint:recommended` 和 `@typescript-eslint/recommended`。
> - 集成 `prettier`，冲突时以 Prettier 为准。
> - `prettier/prettier: "error"`：格式错误视为 ESLint 错误。
> - `no-console: "off"`：允许使用 `console.log`。
> - `no-debugger: "warn"`：调试器语句仅警告。
> - `ignorePatterns`：忽略 `node_modules/` 和 `dist/` 目录。

---

### ✅ 6.3 `.editorconfig`（新增）

```ini
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 4
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true

[*.sol]
indent_size = 4

[*.md]
trim_trailing_whitespace = false
```

> **作用**：统一不同编辑器的基础风格。
> - `[*]`：所有文件使用 UTF-8、4 空格缩进、LF 换行。
> - `[*.sol]`：Solidity 文件缩进为 4 空格。
> - `[*.md]`：Markdown 文件允许行尾空格。

---

### ✅ 6.4 `.env`（新增）

```env
REMOTE_TEST_PRIVATE_KEY="私钥"
REMOTE_TEST_RPC_URL="测试网地址"
ENCRYPTION_PASSWORD="加密密码"
```

> **作用**：存储敏感环境变量，**不应提交到 Git**。

---

### ✅ 6.5 `.gitignore`（增强）

```gitignore
# Node modules
/node_modules

# Compilation output
/dist

# Hardhat Build Artifacts
/artifacts
/cache
/coverage
/types

# IDE & Editor
.vscode/*
!.vscode/settings.json

# Environment
.env
encryptedKey.json
.env.local

# Logs
*.log

# Prettier & ESLint cache
.node_modules
.cache
eslint-cache

# OS
.DS_Store
Thumbs.db
```

> **作用**：在初始 `.gitignore` 基础上增强，新增忽略 `dist/`, `cache/`, `coverage/`, `encryptedKey.json`, `.env.*` 等敏感或构建产物。

---

### ✅ 6.6 `.vscode/settings.json`（增强）

```json
{
    "editor.tabSize": 4,
    "editor.detectIndentation": false,
    "editor.insertSpaces": true,
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
        "source.fixAll.eslint": "explicit"
    },
    "files.autoSave": "onFocusChange",
    "files.eol": "\n",
    "typescript.validate.enable": true,
    "javascript.validate.enable": false
}
```

> **作用**：配置 VS Code 实现保存时自动格式化（Prettier）和修复（ESLint）。

---

## ⚠️ 七、修复 VS Code TypeScript 编译器错误（`tsconfig.json` 问题）

### 🚫 7.0 问题描述

在 VS Code 中打开项目时，`node_modules/@nomicfoundation/hardhat-toolbox/src/tsconfig.json` 文件出现以下 TypeScript 编译错误：

```text
无法读取文件“.../node_modules/config/typescript/tsconfig.json”。
Parent configuration missing
找不到文件“.../node_modules/@nomicfoundation/hardhat-core/src”。
找不到文件“.../node_modules/@nomicfoundation/hardhat-chai-matchers”。
找不到文件“.../node_modules/@nomicfoundation/hardhat-ethers/src”。
...
```

这些错误虽然**不影响项目编译、测试和部署的实际运行**，但会在 VS Code 的“问题”面板中显示，可能干扰开发体验。

---

### 🔍 7.1 根本原因分析

这些错误源于 `@nomicfoundation/hardhat-toolbox` 包内部的 `tsconfig.json` 文件配置：

```json
{
  "extends": "../config/typescript/tsconfig.json",
  "compilerOptions": {
    "composite": true,
    "outDir": "../../dist"
  },
  "references": [
    { "path": "../hardhat-core/src" },
    { "path": "../hardhat-chai-matchers" },
    { "path": "../hardhat-network-helpers" },
    { "path": "../hardhat-ethers/src" },
    { "path": "../hardhat-waffle/src" }
  ]
}
```

**问题本质**：

1. **`extends` 路径错误**：`../config/typescript/tsconfig.json` 不存在于 `node_modules` 中。该路径是 Hardhat 团队在**源码仓库**中使用的，用于构建流程。
2. **`references` 路径失效**：`"../hardhat-core/src"` 等路径在 `node_modules` 中无法解析，因为 `hardhat-core` 等包的源码（`src/` 目录）**不会被发布到 npm**。npm 发布的是编译后的 `dist/` 文件。
3. **开发构建配置**：此 `tsconfig.json` 是 `hardhat-toolbox` 项目在**开发和构建阶段**使用的配置，用于将 `toolbox` 与 `core`、`ethers` 等插件组合编译。一旦发布，此配置对使用者已无实际意义。

> ✅ **结论**：这些错误是**无害的**。VS Code 的 TypeScript 语言服务试图解析所有可见的 `tsconfig.json` 文件，但 `node_modules` 中的此类配置文件**不应被主项目继承或使用**。

---

## #✅ 7.2 推荐解决方案（无需修改源码）
最安全、最推荐的做法是：让 VS Code 忽略 `node_modules` 中的 `tsconfig.json` 文件。

###  ✅ 方法一：修改 VS Code 设置（全局或工作区）

在 VS Code 的设置中添加以下配置，阻止其扫描 `node_modules` 目录下的 `tsconfig.json` 文件：

```json
{
  "typescript.tsdk": "node_modules/typescript/lib",
  "typescript.enableTsServerTrace": false,
  "typescript.preferences.includePackageJsonAutoImports": "auto",
  "typescript.suggest.autoImports": true,
  "typescript.validate.enable": true,
  "javascript.validate.enable": true,
  "typescript.tsc.autoDetect": "on",
  "typescript.preferences.renameShorthandProperties": true,
  "typescript.suggest.enabled": true,
  "typescript.updateImportsOnFileMove.enabled": "prompt",
  "typescript.preferences.includeInlayParameterNameHints": "literals",
  "typescript.inlayHints.parameterNames.enabled": false,
  "typescript.inlayHints.variableTypes.enabled": false,
  "typescript.inlayHints.propertyDeclarationTypes.enabled": false,
  "typescript.inlayHints.functionLikeReturnTypes.enabled": false,
  "typescript.inlayHints.enumMemberValues.enabled": false,
  "typescript.preferences.quoteStyle": "auto",
  "typescript.suggest.completeJSDocs": true,
  "typescript.suggest.autoImportSuggestionsMode": "recentlyUsed",
  "typescript.suggest.showModuleSuggestions": true,
  "typescript.suggest.showClassSuggestions": true,
  "typescript.suggest.showInterfaceSuggestions": true,
  "typescript.suggest.showConstructorSuggestions": true,
  "typescript.suggest.showModuleSuggestions": true,
  "typescript.suggest.showVariableSuggestions": true,
  "typescript.suggest.showPropertySuggestions": true,
  "typescript.suggest.showFunctionSuggestions": true,
  "typescript.suggest.showMethodSuggestions": true,
  "typescript.suggest.showEventSuggestions": true,
  "typescript.suggest.showOperatorSuggestions": true,
  "typescript.suggest.showKeywordSuggestions": true,
  "typescript.suggest.showSnippetSuggestions": true,
  "typescript.suggest.showPathSuggestions": true,
  "typescript.suggest.showDeprecatedItems": true,
  "typescript.suggest.autoImports": true,
  "typescript.suggest.names": true,
  "typescript.suggest.paths": true,
  "typescript.suggest.packageJson": true,
  "typescript.suggest.importModuleSpecifier": "auto",
  "typescript.suggest.importModuleSpecifierEnding": "auto",
  "typescript.suggest.includeCompletionsForImportStatements": true,
  "typescript.suggest.includeCompletionsForModuleExports": true,
  "typescript.suggest.includeCompletionsWithInsertText": true,
  "typescript.suggest.includeCompletionsWithSnippetText": true,
  "typescript.suggest.filterSuggestions": true,
  "typescript.suggest.autoImports": true,
  "typescript.preferences.includePackageJsonAutoImports": "auto"
}
```

更关键的是，确保 VS Code 不加载 `node_modules` 中的配置文件。可在工作区 `.vscode/settings.json` 中添加：

```json
{
  "typescript.preferences.disableSuggestions": false,
  "typescript.suggest.enabled": true,
  "typescript.validate.enable": true,
  "typescript.tsc.autoDetect": "on",
  "typescript.tsdk": "node_modules/typescript/lib",
  "typescript.disableAutomaticTypeAcquisition": false,
  "typescript.suggest.autoImports": true,
  "typescript.suggest.includeCompletionsForImportStatements": true,
  "typescript.suggest.includeCompletionsWithSnippetText": true,
  "typescript.suggest.filterSuggestions": true,
  "typescript.suggest.autoImports": true,
  "typescript.preferences.includePackageJsonAutoImports": "auto",
  "typescript.preferences.quoteStyle": "auto",
  "typescript.preferences.renameShorthandProperties": true,
  "typescript.preferences.includeInlayParameterNameHints": "none",
  "typescript.inlayHints.parameterNames.enabled": false,
  "typescript.inlayHints.variableTypes.enabled": false,
  "typescript.inlayHints.propertyDeclarationTypes.enabled": false,
  "typescript.inlayHints.functionLikeReturnTypes.enabled": false,
  "typescript.inlayHints.enumMemberValues.enabled": false,
  "typescript.updateImportsOnFileMove.enabled": "prompt",
  "typescript.suggest.completeJSDocs": true,
  "typescript.suggest.autoImportSuggestionsMode": "all",
  "typescript.suggest.showModuleSuggestions": true,
  "typescript.suggest.names": true,
  "typescript.suggest.paths": true,
  "typescript.suggest.packageJson": true,
  "typescript.suggest.importModuleSpecifier": "auto",
  "typescript.suggest.importModuleSpecifierEnding": "auto",
  "typescript.preferences.disableAutomaticTypeAcquisition": false
}
```

或者，直接通过 VS Code 图形界面操作：

1. 打开命令面板（Ctrl+Shift+P）
2. 输入并选择 “Preferences: Open Settings (JSON)”
3. 在打开的 `settings.json` 文件中添加：

```json
{
  "typescript.preferences.disableSuggestions": false,
  "typescript.suggest.enabled": true,
  "typescript.validate.enable": true,
  "typescript.tsc.autoDetect": "on",
  "typescript.tsdk": "node_modules/typescript/lib",
  "typescript.disableAutomaticTypeAcquisition": false,
  "typescript.suggest.autoImports": true
}
```

这样配置后，VS Code 将优先使用项目根目录的 `tsconfig.json`，并忽略 `node_modules` 中可能干扰的配置文件，从根本上避免类型冲突问题。
---
**简化并提供正确方案如下：**

---

### ✅ 7.2 推荐解决方案（无需修改源码）

**最安全、最推荐的做法是：让 VS Code 忽略 `node_modules` 中的 `tsconfig.json` 文件。**

#### ✅ 方法一：修改 VS Code 设置（工作区级）

在项目根目录的 `.vscode/settings.json` 中添加：

```json
{
  "typescript.validate.enable": true,
  "typescript.validate.disableDeepValidation": true,
  "typescript.suggest.autoImports": false,
  "typescript.preferences.includePackageJsonAutoImports": "off",
  "typescript.tsserver.experimental.enableProjectDiagnostics": false
}
```

> **说明**：
> - `"typescript.validate.disableDeepValidation": true`：禁用对 `node_modules` 的深度类型验证，可显著减少错误提示。
> - 其他设置用于优化 TypeScript 语言服务性能。

#### ✅ 方法二：使用 `tsconfig.json` 排除（项目级）

在项目根目录的 `tsconfig.json` 中添加 `exclude` 字段：

```json
{
  "compilerOptions": {
    // ... 你的现有配置
  },
  "exclude": [
    "node_modules"
  ]
}
```

> **说明**：明确告诉 TypeScript 编译器忽略 `node_modules` 目录。虽然语言服务可能仍会索引，但可减少错误。

#### ✅ 方法三：关闭 `node_modules` 的 TS 验证（VS Code 设置）

在 VS Code 的 `settings.json` 中：

```json
{
  "typescript.validate.enable": false
}
```

> **⚠️ 警告**：此方法会完全关闭 TypeScript 验证，**不推荐**。

---

### ✅ 7.3 替代方案（修改 `tsconfig.json`，不推荐）

**仅当上述方法无效且错误严重影响开发时考虑。**

1. **定位文件**：
   ```bash
   # Windows
   notepad "node_modules/@nomicfoundation/hardhat-toolbox/src/tsconfig.json"
   ```

2. **修改为**：
   ```json
   {
     "compilerOptions": {
       "outDir": "../../dist"
     }
   }
   ```

3. **保存后重启 VS Code**。

> ❌ **为什么不推荐**：
> - 修改 `node_modules` 是**临时且危险的**。
> - `npm install` 会**覆盖修改**。
> - 可能破坏包的构建信息。

---

## 🧪 八、验证环境（确保 `v2.26.3` 正常工作）

### ✅ 8.1 编译合约

```bash
npx hardhat compile
```

> ✅ 输出：`Compiled 1 Solidity file successfully`

---

### ✅ 8.2 运行测试

```bash
npx hardhat test
```

> ✅ 输出：`1 passing`

---

### ✅ 8.3 启动本地节点

```bash
npx hardhat node
```

> ✅ 输出：`Started HTTP and WebSocket JSON-RPC server at http://127.0.0.1:8545/`

---

### ✅ 8.4 部署测试

```bash
npx hardhat run scripts/deploy.ts --network localhost
```

> ✅ 输出：`Lock with 1 ETH and unlock timestamp ... deployed to ...`

---

## 📦 九、智能合约开发常用命令汇总（含 ts-node 执行）

| 命令 | 说明 |
|------|------|
| `npx hardhat compile` | 编译 Solidity 合约 |
| `npx hardhat test` | 运行测试用例 |
| `npx hardhat node` | 启动本地 Hardhat 节点（本地测试网） |
| `npx hardhat run scripts/deploy.ts --network localhost` | 部署合约到本地节点 |
| `npx hardhat run scripts/deploy.ts --network sepolia` | 部署合约到 Sepolia 测试网 |
| `npm run format` | 格式化代码（通常使用 Prettier） |
| `npm run lint` | 检查代码规范（通常使用 ESLint） |
| `npm run clean` | 清理构建产物（如 `artifacts/`, `cache/` 等） |
| `npm run reinstall` | 删除 `node_modules` 并重新安装依赖 |
| `npm run ts-node -- your-script.ts` | 使用 ts-node 直接执行任意 TypeScript 脚本 |
| `npx ts-node your-script.ts` | 直接通过 npx 执行 TypeScript 脚本（无需 npm run） |
| `npx hardhat clean` | 清理 Hardhat 编译缓存和构建产物（包括 `artifacts/` 和 `cache/` 目录） |
| `npx tsc` | 将 TypeScript 文件编译为 JavaScript（需确保 `tsconfig.json` 配置正确） |

> ✅ **`ts-node` 使用场景说明**：
>
> 当本人编写了一些独立的 TypeScript 脚本（例如用于数据处理、API 调用、批量操作等），但不想将其作为 Hardhat 任务运行时，可以使用 `ts-node` 直接执行：
>
> ```bash
> # 创建一个独立的工具脚本
> echo 'console.log("Hello from ts-node!");' > tools/hello.ts
>
> # 执行该脚本
> npx ts-node tools/hello.ts
> ```
>
> 输出：
> ```
> Hello from ts-node!
> ```
>
> **优势**：
> - 无需编译即可运行 `.ts` 文件。
> - 支持 ES6+ 语法和 TypeScript 类型检查。
> - 适合快速原型开发和调试工具脚本。

---

## ⚠️ 十、安全审计问题说明与处理建议

执行 `npm install` 后运行 `npm audit` 可能出现 **13 条低危漏洞**，主要涉及：

- `cookie <0.7.0` → `@sentry/node` → `hardhat`
- `tmp <=0.2.3` → `solc`

### 🔍 问题分析

1. **根源**：这些漏洞来自 `hardhat` 及其依赖链（如 `@sentry/node`, `solc`），属于**开发工具链的间接依赖**。
2. **影响范围**：**仅限于本地开发环境**，**不会影响**：
   - 智能合约代码本身
   - 部署到链上的字节码
   - 钱包私钥或用户资产
3. **无法修复原因**：
   - `hardhat@2.26.3` 依赖 `@sentry/node@^7.59.0`，而该版本依赖 `cookie@^0.5.0`。
   - `solc` 依赖 `tmp`，但 `solc` 已通过安全审计，且 `tmp` 的漏洞在编译场景下**不可利用**。

### ✅ 处理建议

```bash
# 接受警告（推荐）
npm audit --omit=dev
```

> 或者，若需消除报告：
>
> ```bash
> npm audit fix --force
> ```
>
> **但请注意**：`--force` 可能引入不兼容版本，**不推荐在生产项目中使用**。

### 📌 结论

- ✅ **这些漏洞不影响合约安全性或功能**。
- ✅ **当前依赖组合（`hardhat@2.26.3 + toolbox@^4.0.0`）是经过验证的稳定方案**。
- ❌ **不要为了“0漏洞”而升级至 `hardhat@3.x`**，那将引入更大的兼容性风险。

---

## ✅ 十一、结论

本方案已**完全规避 `hardhat@3.x` 的不稳定性**，精准构建于 `v2.26.3`：

- ✅ **项目名称已更新为 `hardhat-project`**，所有相关引用均已同步。
- ✅ **强制安装 `hardhat@2.26.3`**，避免 `3.0.4` 的兼容性问题。
- ✅ 明确解释 `Do you want to install ... dependencies? (Y/n)` 选项，推荐 `y`。
- ✅ **清晰区分初始结构与最终结构**，便于理解配置演变过程。
- ✅ 所有配置与本人提供的文件**100% 一致**。
- ✅ 项目可编译、测试、部署、格式化。
- ✅ 在 `Node.js v20.18.0 + npm 10.8.2` 下验证通过。
- ✅ **完整结构**：包含 `node_modules/` 目录，反映真实项目状态。
- ✅ **团队协作友好**：通过 `.editorconfig`, `.prettierrc`, `.eslintrc.js`, `.vscode/settings.json` 实现代码风格统一。
- ✅ **新增 `ts-node` 执行能力**：支持直接运行 `.ts` 脚本，提升开发效率。
- ✅ **已修复 Ignition 导入错误**：`hardhat@2.26.3` 不支持 `hardhat-ignition`，已移除相关代码并使用标准 `ethers.deployContract`。
- ✅ **已修复 Solidity 编译器版本不匹配**：在 `hardhat.config.ts` 中明确设置 `version: "0.8.28"`。
- ✅ **已修复 VS Code TypeScript 错误**：详细说明了 `node_modules/@nomicfoundation/hardhat-toolbox/src/tsconfig.json` 报错的根本原因，并提供了**推荐的非侵入式解决方案**（修改 `.vscode/settings.json` 或 `tsconfig.json` 的 `exclude`），避免修改 `node_modules`。
- ✅ **特别说明**：`hardhat@2.26.3` **不支持** `hardhat-ignition`，该功能是 `hardhat@3.x` 的实验性功能。已提供基于 `ethers.deployContract()` 的稳定替代方案，并详细说明了原因和操作步骤。

> ✅ **此为当前最稳定、最可靠的 Hardhat 开发环境搭建方案。**

---

**✅ 安装成功！现在可以安全地进行智能合约开发了！** 🎉

---

# 📄 Hardhat 单文件编译指南

> 本文档详细说明如何在 Hardhat 项目中实现 **通用、简单、通过命令指定文件名编译单个 Solidity 文件** 的方法，并解决常见问题。

---

## 🧩 1. 背景

`npx hardhat compile` 是 Hardhat 的默认编译命令，但它：

- ❌ 不支持直接编译单个 `.sol` 文件（如 `npx hardhat compile Contract.sol`）
- ✅ 支持**增量编译**：只重新编译修改过的文件

因此，若要实现“**指定文件编译**”，需借助自定义脚本或配置。

---

## 🔧 2. `solc` 版本兼容性说明

### 当前配置（`package.json`）

```json
"devDependencies": {
  "solc": "^0.8.30"
}
```

### 是否兼容？

- ✅ **技术上可用**
- ❌ **不推荐使用 `^0.8.30`**

### 原因

- `npx hardhat compile` 使用的是 Hardhat 内部管理的 `solc`，版本由 `hardhat.config.js` 决定
- `package.json` 中的 `solc` 仅用于**自定义脚本**（如 `require('solc')`）
- 若脚本与 Hardhat 使用的版本不一致，可能导致：
  - ABI 不一致
  - 部署验证失败
  - 字节码差异

### ✅ 推荐做法

确保 `solc` 版本与 `hardhat.config.js` 一致：

```js
// hardhat.config.js
module.exports = {
  solidity: "0.8.8", // ← Hardhat 实际使用的版本
};
```

```bash
# 安装精确匹配的 solc
npm install solc@0.8.8 --save-dev
```

```json
"devDependencies": {
  "solc": "0.8.8"
}
```

> ✅ 避免版本混乱，确保一致性。

---

## ⚠️ 3. 常见错误：`Invalid EVM version requested`

### 错误信息

```bash
❌ Compilation failed:
Invalid EVM version requested.
```

### 原因

在 `solc` 编译配置中设置了不支持的 `evmVersion`：

```js
evmVersion: 'paris'
```

- `paris` 是 **最新升级后的 EVM 版本**
- `solc <= 0.8.19` **不支持 `paris`**
- 最高支持版本为 `london`

### ✅ 解决方案

#### 方法 1：删除 `evmVersion`（推荐）

让 `solc` 使用默认 EVM 版本，自动兼容：

```js
settings: {
  optimizer: {
    enabled: true,
    runs: 200
  }
  // 不指定 evmVersion
}
```

#### 方法 2：改为兼容版本

```js
evmVersion: 'london'
```

#### 方法 3：升级 `solc`（如需 `paris`）

```bash
npm install solc@0.8.20 --save-dev
```

然后可安全使用：

```js
evmVersion: 'paris'
```

---

## ✅ 4. 通用单文件编译脚本（推荐）

### 目标

- 一行命令编译任意 `.sol` 文件
- 支持参数传入
- 输出 ABI 和 BIN
- 兼容现有 Hardhat 项目

---

### 步骤 1：安装 `solc`

```bash
npm install solc@0.8.8 --save-dev
```

> ✅ 版本需与 `hardhat.config.js` 一致

---

### 步骤 2：创建脚本 `scripts/compile-sol.js`

```js
// scripts/compile-sol.js
const fs = require('fs');
const path = require('path');
const solc = require('solc');

const DEFAULT_SOLC_VERSION = '0.8.8';
const OUTPUT_DIR = path.join(__dirname, '..', 'artifacts', 'compiled');

if (!fs.existsSync(OUTPUT_DIR)) {
  fs.mkdirSync(OUTPUT_DIR, { recursive: true });
}

function help() {
  console.log(`
Usage: node scripts/compile-sol.js <SolidityFile.sol> [options]

Options:
  --version, -v <version>   Specify Solidity version (default: ${DEFAULT_SOLC_VERSION})
  --optimize, -o            Enable optimizer (default: true)
  --runs <n>                Set optimizer runs (default: 200)
  --help, -h                Show this help

Example:
  node scripts/compile-sol.js contracts/SimpleStorage.sol
  node scripts/compile-sol.js contracts/Token.sol --version 0.8.20
  `);
  process.exit(0);
}

const args = process.argv.slice(2);
if (args.length === 0 || args.includes('--help') || args.includes('-h')) {
  help();
}

const filePath = args[0];
let optimize = true;
let runs = 200;

for (let i = 1; i < args.length; i++) {
  if (args[i] === '--optimize' || args[i] === '-o') {
    optimize = true;
  } else if (args[i] === '--runs') {
    runs = parseInt(args[++i]);
  }
}

if (!fs.existsSync(filePath)) {
  console.error(`❌ File not found: ${filePath}`);
  process.exit(1);
}

const source = fs.readFileSync(filePath, 'utf8');
const fileName = path.basename(filePath);
const contractName = fileName.replace('.sol', '');

console.log(`🔨 Compiling ${fileName} with solc ${solc.version()}...`);

const input = {
  language: 'Solidity',
  sources: {
    [fileName]: {
      content: source,
    },
  },
  settings: {
    outputSelection: {
      '*': {
        '*': ['abi', 'evm.bytecode.object'],
      },
    },
    optimizer: {
      enabled: optimize,
      runs: runs,
    }
    // ✅ 不设置 evmVersion，避免兼容问题
  },
};

const output = JSON.parse(solc.compile(JSON.stringify(input)));

if (output.errors) {
  const errors = output.errors.filter(e => e.severity === 'error');
  if (errors.length > 0) {
    console.error('❌ Compilation failed:');
    errors.forEach(err => console.error(err.formattedMessage));
    process.exit(1);
  }
}

const contracts = output.contracts[fileName];
const firstContract = Object.keys(contracts)[0];
const contract = contracts[firstContract];

const abiPath = path.join(OUTPUT_DIR, `${contractName}.abi.json`);
const binPath = path.join(OUTPUT_DIR, `${contractName}.bin`);

fs.writeFileSync(abiPath, JSON.stringify(contract.abi, null, 2));
fs.writeFileSync(binPath, contract.evm.bytecode.object);

console.log(`✅ Compiled: ${fileName}`);
console.log(`   ABI:  ${abiPath}`);
console.log(`   BIN:  ${binPath}`);
```

---

### 步骤 3：添加 npm script

```json
"scripts": {
  "compile:sol": "node scripts/compile-sol.js"
}
```

---

### 步骤 4：使用方式

```bash
# 编译 SimpleStorage.sol
npm run compile:sol contracts/SimpleStorage.sol

# 指定优化次数
npm run compile:sol contracts/Token.sol -- --runs 1000

# 查看帮助
node scripts/compile-sol.js --help
```

---

### 输出示例

```bash
🔨 Compiling SimpleStorage.sol with solc 0.8.8...
✅ Compiled: SimpleStorage.sol
   ABI:  artifacts/compiled/SimpleStorage.abi.json
   BIN:  artifacts/compiled/SimpleStorage.bin
```

---

## 📂 输出目录结构

```
artifacts/
└── compiled/
    ├── SimpleStorage.abi.json
    └── SimpleStorage.bin
```

---

## 🔄 5. `npx hardhat compile` 行为说明

运行：

```bash
npx hardhat compile
```

输出：

```
Nothing to compile
No need to generate any newer typings.
```

### ✅ 是否正常？

**是！这是正常行为。**

- 表示合约未修改，Hardhat 使用缓存
- TypeChain 也无需重新生成类型

### 强制重新编译

```bash
# 方法 1：强制编译
npx hardhat compile --force

# 方法 2：清理后编译
npx hardhat clean
npx hardhat compile
```

---

## ✅ 6. 总结与最佳实践

| 项目 | 推荐做法 |
|------|---------|
| `solc` 版本 | 与 `hardhat.config.js` 一致，精确版本 |
| 单文件编译 | 使用 `compile-sol.js` 脚本 |
| EVM 版本 | 不设置 `evmVersion`，让编译器自动选择 |
| 增量编译 | 日常开发使用 `npx hardhat compile` |
| 类型生成 | 由 Hardhat Toolbox 自动管理 |

---

## 🚀 下一步建议

```bash
# 编译单个文件
npm run compile:sol contracts/SimpleStorage.sol

# 清理并全量编译
npx hardhat clean && npx hardhat compile

# 验证编译产物
ls artifacts/contracts/
```

---

> 📝 **文档更新时间：2025年10月3日**  
> 适用于 Hardhat 2.x + TypeScript 项目  
--- 

✅ **现在你已拥有一套完整、可靠、可复用的单文件编译方案！**

---

# 📚 Hardhat 项目中 ESLint & Prettier 集成问题全记录  
## —— 从 `npm run lint` 报错到最终稳定方案的完整复盘（2025 精确版）

> **作者**：Rain  
> **时间**：2025年10月3日  
> **目标**：完整还原在 **Hardhat + TypeScript** 项目中，从 `npm run lint` 报错出发，经历 Solhint 替代 → 验证失败 → 回归 ESLint → 版本冲突排查 → 最终稳定配置的全过程。  
> **特别强调**：**`hardhat@2.26.3` 与 `ESLint v9` 不兼容，必须使用 `ESLint v8`**。

---

## 🧭 完整问题演进脉络图

```text
1. 起点：npm run lint 执行报错（ESLint 无法处理 .sol）
   ↓
2. 尝试方案 A：引入 solhint 替代检查
   ↓
3. 验证失败：solhint 安装失败、配置不生效、插件废弃
   ↓
4. 回归本质：尝试让 ESLint 支持 .sol 文件
   ↓
5. 错误方向：尝试升级到 ESLint v9 + Flat Config
   ↓
6. 发现根本冲突：hardhat 依赖 eslint@^8.0.0，不兼容 v9
   ↓
7. 正确方向：锁定 ESLint v8 + eslint-plugin-solidity（旧版兼容）
   ↓
8. 最终验证：npm run lint 成功，.sol 文件被检查
   ↓
✅ 解决方案：ESLint v8 + eslint-plugin-prettier + prettier-plugin-solidity
```

---

## 1️⃣ 起点：`npm run lint` 执行报错

### ❌ 问题现象

执行：
```bash
npm run lint
```

报错：
```bash
Error: Failed to load plugin "solidity" declared in ".eslintrc.js": Cannot find module 'eslint-plugin-solidity'
```

或：
```bash
Error: Parsing error: The keyword 'pragma' is reserved
```
---

## 2️⃣ 尝试方案 A：引入 `solhint` 作为替代方案

### 🤔 初始建议（错误方向）

> “你可以尝试使用 `solhint` 来检查 Solidity 文件。”

建议操作：
```bash
npm install --save-dev solhint
echo '{ "extends": "solhint:recommended" }' > .solhint.json
npx solhint "contracts/**/*.sol"
```

### ✅ 短期效果
- `solhint` 能独立运行
- 检测出部分 Solidity 问题

### ⚠️ 但未解决根本问题
- `npm run lint` 仍然失败（因为 ESLint 仍无法处理 `.sol`）
- 需要额外运行 `npx solhint`，流程割裂

---

## 3️⃣ 验证失败：`solhint` 本身问题重重

### ❌ 问题 1：`solhint` 安装后命令无法执行

```bash
npm install --save-dev solhint@latest
npx solhint
# 错误：command not found
```

**原因**：`solhint@1.x` 为实验版本，发布不完整。

**修复**：
```bash
npm install --save-dev solhint@0.0.13
```

✅ 成功运行，但版本为 **2021 年发布**，严重过时。

---

### ❌ 问题 2：`.solhint.json` 配置不生效

配置：
```json
{
  "rules": {
    "func-visibility": "error"
  }
}
```

**结果**：无报错，规则未触发。

**排查**：`solhint --print-config` 显示配置未加载。

---

### ❌ 问题 3：与 Prettier 集成失败

```bash
npm install --save-dev solhint-plugin-prettier
```

`.solhint.json`：
```json
"plugins": ["prettier"],
"rules": { "prettier/prettier": "error" }
```

**结果**：无格式化提示，插件未生效。

**结论**：`solhint-plugin-prettier` 最后更新于 2020 年，**已废弃**。

---

### ❌ 问题 4：VS Code 插件不兼容

- 安装 “Solhint” VS Code 插件
- 无波浪线提示
- 插件市场评论：“已停止维护”

---

## 4️⃣ 回归本质：尝试让 ESLint 支持 `.sol` 文件

### 🤔 新思路
> “能否让 ESLint 直接解析 `.sol` 文件？”

### 🔍 发现 `eslint-plugin-solidity`

- npm 包：`eslint-plugin-solidity`
- 作用：让 ESLint 支持 Solidity 语法
- **关键点**：该包最后更新为 2020 年，且不支持 ESLint v9

---

## 5️⃣ 错误方向：尝试升级到 ESLint v9 + Flat Config

### ❌ 尝试升级 ESLint

```bash
npm install --save-dev eslint@^9.0.0
```

### ❌ 执行 `npm run lint` 报错

```bash
Error: The plugin "@nomicfoundation/hardhat-toolbox" requires an ESLint version ^8.0.0
```

### 🔍 深入排查

查看 `@nomicfoundation/hardhat-toolbox` 的 `package.json`：
```json
"devDependencies": {
  "eslint": "^8.0.0"
}
```

> ✅ **确认**：`hardhat-toolbox` **仅兼容 ESLint v8**，**不支持 v9**。

---

## 6️⃣ 正确方向：锁定 ESLint v8 + 兼容方案

### ✅ 决策
> **放弃 ESLint v9，回归 v8**，并寻找 `eslint-plugin-solidity` 的替代方案。

### 🔍 新发现
- `prettier-plugin-solidity` 已支持 **格式化 + 基础 linting**
- `eslint-plugin-prettier` 可将 Prettier 的格式化结果暴露为 ESLint 错误
- 可通过 `eslint-plugin-prettier` 间接实现 `.sol` 文件的“检查”

---

## 7️⃣ 最终解决方案：ESLint v8 + Prettier 统一格式化

### ✅ 保留现有配置（已正确）

```json
"scripts": {
  "lint": "npm run lint:js && npm run format:check",
  "lint:fix": "npm run lint:js:fix && npm run format",
  "lint:js": "eslint \"**/*.{ts,js}\" --ext .ts,.js",
  "lint:js:fix": "eslint \"**/*.{ts,js}\" --ext .ts,.js --fix",
  "format": "prettier --plugin prettier-plugin-solidity --write \"**/*.{js,cjs,ts,sol}\"",
  "format:check": "prettier --plugin prettier-plugin-solidity --check \"**/*.{js,cjs,ts,sol}\""
}
```

### ✅ `devDependencies`（关键版本）

```json
"devDependencies": {
  "eslint": "^8.57.0",
  "prettier": "^3.2.5",
  "prettier-plugin-solidity": "^1.1.3",
  "eslint-plugin-prettier": "^5.1.3",
  "@typescript-eslint/eslint-plugin": "^7.18.0",
  "@typescript-eslint/parser": "^7.18.0"
}
```

### ✅ `.eslintrc.js`（推荐配置）

```js
// .eslintrc.js
module.exports = {
  root: true,
  env: {
    node: true,
    mocha: true,
  },
  extends: [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:prettier/recommended", // 启用 eslint-plugin-prettier
  ],
  parser: "@typescript-eslint/parser",
  parserOptions: {
    ecmaVersion: 2022,
    sourceType: "module",
    project: "./tsconfig.json",
  },
  plugins: ["@typescript-eslint", "prettier"],
  rules: {
    "prettier/prettier": "error", // Prettier 错误作为 ESLint 错误
    "@typescript-eslint/no-unused-vars": "error",
  },
  ignorePatterns: ["artifacts/", "cache/", "coverage/", "node_modules/"],
};
```

### ✅ VS Code `settings.json`

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "eslint.validate": [
    "javascript",
    "typescript",
    "javascriptreact",
    "typescriptreact"
    // ❌ 不添加 "solidity"，因为 ESLint 不直接解析 .sol
  ],
  "solidity.formatter": "prettier",
  "solidity.compileUsingRemoteVersion": "v0.8.20+commit.c7f1c3b1"
}
```

---

## 8️⃣ 最终验证：`npm run lint` 成功执行

### ✅ 执行命令

```bash
npm run lint
```

### ✅ 输出结果

```bash
# 1. 执行 lint:js
> eslint "**/*.{ts,js}" --ext .ts,.js

# 2. 执行 format:check
> prettier --plugin prettier-plugin-solidity --check "**/*.{js,cjs,ts,sol}"

# 如果 .sol 文件格式错误，prettier 会报错，导致 lint 失败
```

### ✅ 工作流说明
- `.ts/.js` 文件：由 ESLint 检查 + 自动修复
- `.sol` 文件：由 Prettier 格式化 + `format:check` 验证
- `npm run lint`：统一入口，确保所有文件符合规范

---

## ✅ 最终状态

| 功能 | 状态 | 说明 |
|------|------|------|
| `npm run lint` | ✅ 成功 | 统一检查所有文件 |
| Solidity 检查 | ✅ | 通过 `prettier --check` 实现 |
| 自动修复 | ✅ | `npm run lint:fix` 支持 |
| Prettier 集成 | ✅ | 支持 `.sol` 格式化 |
| VS Code 支持 | ✅ | 格式化 + ESLint 提示 |

---

## 📌 复盘总结：技术决策路径

| 阶段 | 决策 | 结果 | 教训 |
|------|------|------|------|
| 1. 起点 | `npm run lint` 报错 | ❌ | 需先定位错误来源 |
| 2. 尝试 | 引入 `solhint` | ⚠️ 临时方案 | 替代方案可能引入新问题 |
| 3. 验证 | `solhint` 失败 | ❌ | 不可靠，生态断裂 |
| 4. 探索 | 尝试 ESLint v9 | ❌ | 与 `hardhat` 不兼容 |
| 5. 回归 | 锁定 ESLint v8 | ✅ 正确方向 |
| 6. 解决 | 使用 `prettier --check` 作为 .sol 检查 | ✅ 成功 | 利用现有工具链 |

---

## ✅ 最终结论

> **`solhint` 已不可靠，不应作为主要 linter**。

> **`eslint-plugin-solidity` 已废弃，不推荐使用**。

> **`hardhat@2.26.3` 仅兼容 `ESLint v8`，不可升级到 v9**。

> **正确方案**：
> 1. 使用 `ESLint v8` 检查 `.ts/.js` 文件
> 2. 使用 `prettier-plugin-solidity` 格式化 `.sol` 文件
> 3. 使用 `npm run format:check` 作为 `.sol` 的“检查”手段
> 4. 通过 `npm run lint` 统一执行

---

## 📎 参考资料

1. [hardhat-toolbox package.json](https://github.com/NomicFoundation/hardhat-toolbox/blob/main/package.json)（依赖 eslint@^8.0.0）
2. [prettier-plugin-solidity](https://www.npmjs.com/package/prettier-plugin-solidity)
3. [eslint-plugin-prettier](https://www.npmjs.com/package/eslint-plugin-prettier)

---
> **复盘完毕**。此文档完整还原了从问题提出、错误尝试、版本冲突排查到最终稳定方案的全过程，确保信息真实、路径清晰，**特别强调了 `hardhat` 与 `ESLint v9` 的不兼容性**，可供团队复盘与知识传承。
