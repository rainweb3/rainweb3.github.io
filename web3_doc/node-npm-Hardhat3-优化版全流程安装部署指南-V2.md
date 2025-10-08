# node-npm-Hardhat3-优化版全流程安装部署指南-V2
**—— 从零到部署的完整实践 | 环境配置 · 项目结构 · 命令手册 · 验证闭环**
---
**更新时间：2025年10月8日**
---

## 📚 目录

1. [环境准备与版本兼容性矩阵](#1-环境准备与版本兼容性矩阵)  
2. [新建并初始化项目（交互式流程详解）](#2-新建并初始化项目交互式流程详解)  
3. [依赖安装过程深度解析](#3-依赖安装过程深度解析)  
4. [项目结构全解析](#4-项目结构全解析)  
5. [验证环境与编译合约](#5-验证环境与编译合约)  
6. [启动本地节点与交互控制台](#6-启动本地节点与交互控制台)  
7. [使用 Ignition 部署合约（修正版）](#7-使用-ignition-部署合约修正版)  
8. [运行测试：TypeScript + Solidity](#8-运行测试typescript--solidity)  
9. [使用 Viem 进行链上状态查询与交互](#9-使用-viem-进行链上状态查询与交互)  
10. [部署后验证与常用操作](#10-部署后验证与常用操作)  
11. [附录：Hardhat 常用命令速查表](#11-附录-hardhat-常用命令速查表)  
12. [总结与最佳实践建议](#12-总结与最佳实践建议)

---

## 1. 环境准备与版本兼容性矩阵

在开始构建以太坊智能合约项目前，确保您的开发环境满足以下**稳定、兼容、面向未来**的技术栈要求。本指南基于 **Hardhat v3** 的最新 Beta 版本设计，适用于所有新项目。

### ✅ 推荐技术栈（经实测验证）

| 组件 | 版本 | 是否最新 | 稳定性 | 支持情况 | 说明 |
|------|------|----------|--------|------------|----------------|
| **Node.js** | `v22.18.0` | ✅ 是 | ✅ 稳定 | 完全支持 | 推荐使用 LTS 版本；v22 对 ES 模块和性能优化更佳 |
| **npm** | `10.9.3` | ✅ 是 | ✅ 稳定 | 完全支持 | 与 Node.js v22 捆绑发布，无需额外升级 |
| **Hardhat** | `v3.0.6 (Beta)` | ⚠️ Beta | ✅ 推荐用于新项目 | 核心框架 | 新一代默认选择，集成 Ignition、Viem、Node Test Runner |
| **TypeScript** | `5.8.0` | ✅ 是 | ✅ 稳定 | 完全支持 | 支持现代类型系统与装饰器实验特性 |
| **Viem** | `2.30.0` | ✅ 是 | ✅ 稳定 | 官方推荐替代 Ethers.js | 轻量、TypeScript 优先的 Ethereum SDK |
| **EVM Compiler (solc)** | `0.8.28` | ✅ 是 | ✅ 稳定 | 默认内置 | 支持 Cancun 升级目标（EVM 1.0.0） |
| **@types/node** | `22.8.5` | ✅ 是 | ✅ 稳定 | 必需依赖 | 匹配 Node.js v22 的类型定义 |
| **forge-std** | `v1.9.4` | ✅ 是 | ✅ 稳定 | 可选但推荐 | Foundry 团队维护的标准库，用于 Solidity 测试 |
| **hardhat-toolbox-viem** | `5.0.0` | ✅ 是 | ✅ 稳定 | Hardhat v3 官方工具箱 | 替代旧版 `hardhat-toolbox`，专为 Viem 设计 |
| **hardhat-ignition** | `3.0.0` | ✅ 是 | ✅ 稳定 | 内置部署模块 | 声明式部署系统，支持复杂依赖拓扑 |

> 🔍 **版本说明**：
> - **Hardhat v3** 是重大重构版本，引入了 `Ignition` 部署引擎 和 `Node Test Runner`，不再强制依赖 Mocha。
> - 使用 `Viem` 而非 `Ethers.js` 成为官方推荐路径，带来更快的测试速度和更好的 TS 支持。
> - 所有依赖均通过 `npm install` 自动解析，版本锁定在 `package-lock.json` 中。

### 🧪 验证环境命令

请在终端中逐一执行以下命令，确认输出与推荐版本一致：

```bash
node -v   # 预期输出: v22.18.0
npm -v    # 预期输出: 10.9.3
```

✅ 若输出匹配上表，则环境准备就绪。

---

## 2. 新建并初始化项目（交互式流程详解）

### 注意：若存在“缓存污染”问题，通过“卸载重装命令”解决
```bash
npm cache clean --force
rm -rf node_modules package-lock.json
npm install
```

### 步骤 1：创建项目目录并进入

```bash
mkdir my-hardhat3-project && cd my-hardhat3-project
```

> 💡 确保当前目录为空。

---

### 步骤 2：启动 Hardhat 初始化向导

```bash
npx hardhat --init
```

> 💡 此命令将引导你完成交互式项目创建流程，无需手动安装 hardhat。

---

### 步骤 3：选择 Hardhat 版本

```
? Which version of Hardhat would you like to use?
  > Hardhat 3 Beta (recommended for new projects)
    Hardhat 2 (older version)
```

👉 **选择**：`Hardhat 3 Beta`

📌 **理由**：
- 使用最新的声明式部署系统 Ignition
- 原生支持 Viem 和 Node.js 内置测试运行器
- 更快的编译、测试、部署速度
- 更清晰的错误提示和调试体验

---

### 步骤 4：指定项目路径

```
? Where would you like to initialize the project?
Please provide either a relative or an absolute path:
```

👉 输入：`.` （表示当前目录）

📌 **提示**：请确保当前为空目录或已备份重要文件。

---

### 步骤 5：选择项目模板

```
? What type of project would you like to initialize?
  > A TypeScript Hardhat project using Node Test Runner and Viem
    A TypeScript Hardhat project using Mocha and Ethers.js
```

👉 **选择**：第一个选项（Node Test Runner + Viem）

📌 **优势**：
- 更快的测试执行速度（Node.js 原生 `node:test`）
- 更简洁的异步语法（`async/await`）
- 更强的 TypeScript 类型推断
- 官方未来主推方向

---

### 步骤 6：确认自动安装依赖

系统生成如下安装命令：

```bash
npm install --save-dev \
  "hardhat@3.0.6" \
  "@nomicfoundation/hardhat-toolbox-viem@5.0.0" \
  "@nomicfoundation/hardhat-ignition@3.0.0" \
  "@types/node@22.8.5" \
  "forge-std@foundry-rs/forge-std#v1.9.4" \
  "typescript@5.8.0" \
  "viem@2.30.0"
```

随后提示：

```
Do you want to run it now? (Y/n)
```

👉 输入：`Y` 或直接回车

📌 **结果**：npm 开始下载并安装所有必需依赖包。

---

## 3. 依赖安装过程深度解析

当用户确认后，npm 将执行依赖安装流程。

### 📦 安装日志关键信息

```
added 171 packages in 2m
53 packages are looking for funding
  run `npm fund` for details
✅ Dependencies installed ✨
🌟 We ❤️ open source – if you found this useful, please give us a star on GitHub!
```

### 🔧 核心依赖功能说明

| 包名 | 功能描述 |
|------|----------|
| `hardhat@3.0.6` | 主框架，提供编译、测试、部署、控制台等核心能力 |
| `@nomicfoundation/hardhat-toolbox-viem` | 集成插件集合：Chai/Viem/Ignition/TypeChain 等 |
| `@nomicfoundation/hardhat-ignition` | 声明式部署系统，支持故障恢复、状态追踪 |
| `viem@2.30.0` | 现代化 Ethereum 客户端库，轻量高效，TypeScript 友好 |
| `typescript@5.8.0` | 编译 `.ts` 文件，支持高级类型检查 |
| `@types/node@22.8.5` | 提供 Node.js API 的类型定义 |
| `forge-std` | 引入 `stdError.sol`, `console2.sol` 等调试辅助合约 |

> ⚠️ 注意：`forge-std` 从 GitHub 安装（非 npm），需网络通畅。

---

## 4. 项目结构全解析

初始化完成后，项目根目录结构如下：

```
my-hardhat3-project/
├── contracts/               # Solidity 合约源码
│   ├── Counter.sol          # 示例主合约
│   └── Counter.t.sol        # Solidity 测试合约（可选）
├── ignition/                # Ignition 部署模块
│   └── modules/
│       └── Counter.ts       # 定义如何部署 Counter 合约
├── node_modules/            # 第三方依赖（自动生成）
├── scripts/                 # 自定义脚本
│   └── send-op.ts           # 示例脚本：发送交易至 OP 链
├── test/                    # TypeScript 测试文件
│   └── Counter.ts           # 使用 Viem 编写的集成测试
├── artifacts/               # 编译产物（首次编译后生成）
├── cache/                   # Hardhat 缓存（运行时生成）
├── .gitignore               # Git 忽略规则
├── hardhat.config.ts        # 主配置文件
├── package.json             # 项目元数据与脚本定义
├── package-lock.json        # 锁定依赖版本
├── README.md                # 项目说明文档
└── tsconfig.json            # TypeScript 编译配置
```

### 📁 关键文件说明

| 路径 | 类型 | 功能说明 |
|------|------|----------|
| `contracts/*.sol` | Solidity | 存放所有智能合约代码 |
| `contracts/*.t.sol` | Solidity | 可用于编写基于 Foundry 风格的 Solidity 单元测试 |
| `ignition/modules/*.ts` | TypeScript | 定义合约部署计划，支持参数化、条件部署 |
| `scripts/*.ts` | TypeScript | 自定义脚本，可用于部署、调用、批量操作 |
| `test/*.ts` | TypeScript | 使用 Node.js 原生 `node:test` 运行器编写测试 |
| `hardhat.config.ts` | Config | 配置网络、编译器、插件、账户等全局设置 |
| `tsconfig.json` | Config | 控制 TypeScript 编译行为（如 target、module、strict） |
| `.gitignore` | Text | 忽略 `node_modules`, `artifacts`, `cache` 等非必要提交内容 |
| `package.json` | JSON | 定义 `scripts` 如 `"compile": "npx hardhat compile"` |

> 🌟 **新增特性（Hardhat v3）**：
> - `artifacts/` 和 `cache/` 在首次编译后自动生成。
> - 支持 `.t.sol` 文件作为 Solidity 测试用例。
> - `ignition/` 目录是 **声明式部署** 的核心，避免传统脚本中的“面条代码”。

---

## 5. 验证环境与编译合约

### ✅ 验证 Hardhat 安装成功

```bash
npx hardhat
```

预期输出应包含类似内容：

```
Hardhat version 3.0.6

Usage: hardhat [GLOBAL OPTIONS] <TASK> [SUBTASK] ...

AVAILABLE TASKS:

  build         Build project
  clean         Clear the cache and delete all artifacts
  compile       Compile Solidity contracts
  console       Open a REPL on a local network
  node          Start a local Ethereum JSON-RPC node
  run           Run a user-defined script
  test          Run tests
  ...
```

📌 出现任务列表即表示安装成功。

---

### 🔬 编译合约

```bash
npx hardhat compile
```

输出示例：

```
Compiling your Solidity contracts...
Compiled 1 Solidity file with solc 0.8.28 (evm target: cancun)
```

✅ 成功标志：
- 无语法错误
- 输出编译器版本与 EVM 目标
- 在 `artifacts/` 下生成 ABI 与 bytecode

> 💡 首次编译后，`artifacts/` 和 `cache/` 目录将被创建。

---

## 6. 启动本地节点与交互控制台

### 🖥️ 启动本地开发节点

在**新终端窗口**中运行：

```bash
npx hardhat node
```

📌 输出示例：

```
Started HTTP and WebSocket JSON-RPC server at http://127.0.0.1:8545/

Accounts:
┌──────────────────────────────────────────────────────────────┬────────────────────────────┐
│ (0) 0xf39fd...fffb92266 (10000 ETH)                            │                              │
│ Private Key: 0xac097...ff80                                   │                              │
└──────────────────────────────────────────────────────────────┴────────────────────────────┘
```

> 💡 该命令启动一个完整的本地以太坊网络（Hardhat Network），可用于测试和调试。

---

### 🛠️ 打开交互式控制台

在**另一个终端窗口**中运行：

```bash
npx hardhat console --network localhost
```

> 💡 此命令连接到 `localhost:8545` 上运行的节点。

---

## 7. 使用 Ignition 部署合约（修正版）

### 🚀 执行 Ignition 部署命令（带 deploymentId）

```bash
npx hardhat ignition deploy ./ignition/modules/Counter.ts --network localhost deployment-v1
```

📌 示例输出：
```
Hardhat Ignition 🚀

Deploying [ CounterModule ]

Batch #1
  Executed CounterModule#Counter

Batch #2
  Executed CounterModule#Counter.incBy

[ CounterModule ] successfully deployed 🚀

Deployed Addresses

CounterModule#Counter - 0x5FbDB2315678afecb367f032d93F642f64180aa3
```

> ✅ 部署成功后，合约地址将被记录，可用于后续交互。

---

### ✅ 关于 `deploymentId` 参数的正确使用说明

`deploymentId` 是一个**必填参数**，用于唯一标识一次部署会话。它必须在命令末尾提供，且不能省略。

#### ✅ 正确用法格式

```bash
npx hardhat ignition deploy <module-path> --network <network-name> --deployment-id <deploymentId>
```

#### ✅ 正确示例

```bash
# 部署到本地节点
npx hardhat ignition deploy ./ignition/modules/Counter.ts \
  --network localhost \
  --deployment-id my-deployment-id

# 部署到 Sepolia 测试网（需在hardhat.config.ts配置）
npx hardhat ignition deploy ./ignition/modules/Counter.ts \
  --network sepolia \
  --deployment-id sepolia-v1

✅ Sepolia 是当前以太坊官方推荐的 PoW 测试网，chainId: 11155111
🔧 配置 hardhat.config.ts 中的 Sepolia 网络：
sepolia: {
  url: "https://eth-sepolia.g.alchemy.com/v2/YOUR_ALCHEMY_KEY", // 或 Infura
  accounts: [process.env.PRIVATE_KEY],
  chainId: 11155111,
}

# 部署到主网
npx hardhat ignition deploy ./ignition/modules/Counter.ts --network mainnet prod-2025

⚠️ 主网部署需极度谨慎！确保：（测试及学习一定不要用主网部署）

合约已通过审计
已测试充分
使用多签钱包或代理模式（如 OpenZeppelin Proxy）
PRIVATE_KEY 安全隔离（建议用 dotenv + 限制访问）
```

> ✅ **`deploymentId` 可以是任意字符串**，如 `v1`, `testnet-phase1`, `prod-2025` 等，用于区分不同环境或版本的部署。

---

### 🔁 高级用法

- **强制重置并重新部署**（清除旧部署状态）：

```bash
npx hardhat ignition deploy --network localhost --reset deployment-v1
```

- **查看部署状态**：

```bash
npx hardhat ignition status deployment-v1
```

- **列出所有已保存的部署**：使用 find + xargs（wsl-Ubuntu/Linux/macOS 高效方式）

```bash
# 不指定 --network，让 Ignition 自动判断
ls ignition/deployments | xargs -I {} sh -c 'echo "=== Status: {} ==="; npx hardhat ignition status {}; echo'
```

- **查看帮助**：

```bash
npx hardhat ignition deploy --help
```

---

## 8. 运行测试：TypeScript + Solidity

### 🧪 运行所有测试（TS + Solidity）

```bash
npx hardhat test
```

输出示例：

```
Running Solidity tests
  contracts/Counter.t.sol:CounterTest
    ✔ test_InitialValue()
    ✔ test_IncByZero()
    ✔ testFuzz_Inc(uint8) (runs: 256)

  3 passing

Running node:test tests
  Counter
    ✔ Should emit the Increment event when calling inc() (644ms)
    ✔ The sum of events should match current value (1027ms)

  2 passing (79s)
```

✅ 测试通过表明：
- 合约逻辑正确
- 可通过 Viem 成功交互
- 事件触发符合预期

---

### 🧪 运行单个测试文件

```bash
npx hardhat test test/Counter.ts
```

### 🔍 过滤测试用例

```bash
npx hardhat test --grep "Increment"
```

仅运行包含 "Increment" 的测试用例。

---

## 9. 使用 Viem 进行链上状态查询与交互

### ✅ 前提条件

要在 `npx hardhat console` 中使用 `viem`，必须满足以下条件：

1.  **安装了 `@nomicfoundation/hardhat-viem` 插件**：
    ```bash
    npm install --save-dev @nomicfoundation/hardhat-viem
    ```

2.  **在 `hardhat.config.ts` 中启用了插件**：
    ```ts
    import { HardhatUserConfig } from "hardhat/config";
    import hardhatViem from "@nomicfoundation/hardhat-viem"; // 导入插件

    const config: HardhatUserConfig = {
      plugins: [
        hardhatViem, // 注册插件
      ],
      solidity: "0.8.28",
      networks: {
        localhost: {
          url: "http://localhost:8545",
        },
      },
    };

    export default config;
    ```

3.  **已编译合约**（可选，但推荐）：
    ```bash
    npx hardhat compile
    ```

---

### 🔹 步骤 1：启动控制台
```bash
npx hardhat console --network localhost
```

### 🔹 步骤 2：获取 `viem` 实例并查询余额

在控制台中依次执行以下命令：

```ts
// 1. 获取 viem 扩展功能
const { viem } = await hre.network.connect();

// 2. 获取 Public Client
const publicClient = await viem.getPublicClient();

// 3. 查询指定地址的余额（以 wei 为单位）
const balance = await publicClient.getBalance({
  address: "0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266",
});

// 4. 输出余额
console.log("Balance:", balance); // 例如: 10000000000000000000000n

// 5. （可选）转换为 ETH
console.log("Balance in ETH:", Number(balance) / 1e18); // 例如: 10000
```

#### 🔹 示例输出
```
> const { viem } = await hre.network.connect();
undefined
> const publicClient = await viem.getPublicClient();
undefined
> const balance = await publicClient.getBalance({ address: "0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266" });
undefined
> console.log("Balance:", balance);
Balance: 10000000000000000000000n
```

---

### 🧩 其他有用的 `viem` 查询命令

#### 查询最新区块号
```ts
const blockNumber = await publicClient.getBlockNumber();
console.log("Latest block number:", blockNumber);
```

#### 查询区块信息
```ts
const block = await publicClient.getBlock();
console.log("Block:", block);
```

#### 使用 `getWalletClients` 发送交易（示例）
```ts
const [wallet1] = await viem.getWalletClients();
const txHash = await wallet1.sendTransaction({
  to: "0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266",
  value: 1000000000000000000n, // 1 ETH
});
console.log("Transaction hash:", txHash);
```

---

### ⚠️ 常见问题排查

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| `hre.viem is undefined` | 未安装或未启用 `@nomicfoundation/hardhat-viem` 插件 | 检查 `package.json` 和 `hardhat.config.ts` |
| `TypeError: Class extends undefined` | `hardhat@3.x` 与某些插件版本冲突（如 `hardhat-ethers`） | 降级到 `hardhat@2.26.0` 或使用纯 `viem` 方案 |
| `address is required` | 查询余额时未提供 `address` 字段 | 确保 `getBalance()` 调用中包含 `{ address: "..." }` |

---

## 10. 部署后验证与常用操作

### ✅ 验证部署状态

```bash
npx hardhat ignition status deployment-v1
```

查看指定 `deploymentId` 的部署任务状态，支持中断后恢复。

---

### 🛠️ 其他实用命令建议

| 场景 | 命令 |
|------|------|
| 清理缓存和构建产物 | `npx hardhat clean` |
| 打开交互式控制台 | `npx hardhat console --network localhost` |
| 扁平化合约用于验证 | `npx hardhat flatten contracts/Counter.sol > flattened.sol` |
| 运行单个测试文件 | `npx hardhat test test/Counter.ts` |
| 列出预设账户 | `npx hardhat accounts` |

---

### 🔄 自定义脚本（推荐添加到 `package.json`）

```json
{
  "scripts": {
    "build": "npx hardhat compile",
    "test": "npx hardhat test",
    "start": "npx hardhat node",
    "deploy": "npx hardhat ignition deploy ./ignition/modules/Counter.ts --network localhost deployment-v1",
    "console": "npx hardhat console --network localhost",
    "clean": "npx hardhat clean"
  }
}
```

然后可简写为：

```bash
npm run build
npm run test
npm run start
npm run deploy
```

---

## 11. 附录：Hardhat 常用命令速查表

以下为 **Hardhat v3** 常用 CLI 命令汇总，按功能分类列出。

### 🧰 核心任务

| 命令 | 描述 |
|------|------|
| `npx hardhat` | 显示所有可用命令 |
| `npx hardhat --version` | 查看当前 Hardhat 版本 |
| `npx hardhat compile` | 编译所有 Solidity 合约 |
| `npx hardhat clean` | 删除 `artifacts/` 和 `cache/` 目录 |
| `npx hardhat run <script>` | 执行指定脚本（如部署脚本） |
| `npx hardhat console` | 启动交互式 JavaScript/TypeScript 控制台 |

---

### 🖥️ 本地网络与节点

| 命令 | 描述 |
|------|------|
| `npx hardhat node` | 启动本地 JSON-RPC 节点（监听 `localhost:8545`） |
| `npx hardhat run <script> --network localhost` | 在本地节点上运行脚本 |
| `npx hardhat run <script> --network hardhat` | 使用内存链运行脚本（默认） |

---

### 🧪 测试相关

| 命令 | 描述 |
|------|------|
| `npx hardhat test` | 运行所有测试（TS 和 `.t.sol`） |
| `npx hardhat test <file>` | 运行指定测试文件 |
| `npx hardhat test --grep "Increment"` | 仅运行包含关键字的测试用例 |
| `npx hardhat coverage` *(需插件)* | 生成测试覆盖率报告 |

---

### 🚀 部署与 Ignition

| 命令 | 描述 |
|------|------|
| `npx hardhat ignition deploy <module> <deploymentId>` | 使用 Ignition 部署指定模块（必须提供 deploymentId） |
| `npx hardhat ignition status <deploymentId>` | 查看指定部署的状态（支持中断恢复） |
| `npx hardhat ignition list` | 列出所有已定义的部署模块 |

---

### 📦 工具与辅助

| 命令 | 描述 |
|------|------|
| `npx hardhat flatten <file>` | 将合约及其依赖合并为单个文件（用于验证） |
| `npx hardhat verify <address>` | 验证合约源码（需连接 etherscan-like 服务） |
| `npx hardhat accounts` | 列出 Hardhat Network 预设账户 |
| `npx hardhat help <task>` | 查看某个命令的帮助文档 |

---

## 12. 总结与最佳实践建议

本文档全面梳理了基于 **Node.js v22 + npm 10 + Hardhat v3** 的现代以太坊开发环境搭建与全流程操作，涵盖：

- ✅ 精确的组件版本矩阵与兼容性分析  
- ✅ 详细的项目初始化交互步骤  
- ✅ 依赖安装原理与核心包说明  
- ✅ 项目结构图解与各文件职责  
- ✅ 编译、测试、部署、验证全流程演示  
- ✅ 完整的 Hardhat 命令速查手册  

> 🚀 **强烈建议新项目采用 Hardhat v3 + Viem + Node Test Runner 技术栈**，享受更现代、高效、可靠的开发体验。

📌 **最佳实践建议**：
1. 使用 `ignition` 进行声明式部署，避免脚本混乱。
2. 优先使用 `viem` 替代 `ethers.js`，提升性能与类型安全。
3. 利用 `.t.sol` 编写 Solidity 单元测试，提高覆盖率。
4. 将常用命令写入 `package.json` 的 `scripts` 中，简化操作。
5. 定期运行 `npx hardhat clean` 清理缓存，避免构建问题。

📌 文档内容已与实际操作完全对齐，适用于学习、教学与生产环境初始化参考。
