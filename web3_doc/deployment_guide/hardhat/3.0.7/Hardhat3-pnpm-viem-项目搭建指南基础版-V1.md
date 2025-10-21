# Hardhat3.0.7-pnpm-viem-typescript 项目搭建指南（基础版 - V1）  
**基于真实交互界面的精确还原与优化**  
**版本：V1 | 日期：2025年10月21日**

---

## 📚 目录

1. [环境与依赖概览](#1-环境与依赖概览)  
   - 1.1 系统运行环境要求  
   - 1.2 默认依赖组件版本清单  
   - 1.3 依赖安装流程说明  

2. [项目搭建详细步骤](#2-项目搭建详细步骤)  
   - 2.1 检查本地 Node.js 与 npm 环境  
   - 2.2 创建项目目录并进入工作空间  
   - 2.3 初始化 Hardhat 项目（完整交互流程）  
   - 2.4 自动安装依赖包（根据实际命令）  
   - 2.5 验证安装结果  

3. [运行测试并验证环境](#3-运行测试并验证环境)  
   - 3.1 执行完整测试套件  
   - 3.2 理解测试输出内容  
   - 3.3 常见问题排查  

4. [项目结构详解](#4-项目结构详解)  
   - 4.1 实际项目结构  
   - 4.2 合约示例（Counter.sol）分析  
   - 4.3 测试文件解析  

5. [工具链与配置说明](#5-工具链与配置说明)  
   - 5.1 核心开发工具功能说明  
   - 5.2 Hardhat 配置文件详解（hardhat.config.ts）  
   - 5.3 TypeScript 配置说明（tsconfig.json）  
   - 5.4 `tsconfig.json` 常见错误与修复：`lib` 字段不支持 `es2023`

6. [常用命令清单](#6-常用命令清单)  

7. [本地 Hardhat 节点运行输出示例（真实交互记录）](#7-本地-hardhat-节点运行输出示例真实交互记录)

8. [总结与后续建议](#8-总结与后续建议)  

---

## 1. 环境与依赖概览

### 1.1 系统运行环境要求

本指南基于以下环境构建，确保你的开发环境满足以下最低要求：

| 组件       | 版本要求         | 推荐系统平台               | 说明 |
|------------|------------------|----------------------------|------|
| **Node.js** | `>=18.0.0`，推荐 `v22.18.0` | macOS / Linux / WSL（Windows Subsystem for Linux） | 提供 JavaScript 运行时环境 |
| **pnpm**    | `>=8.0.0`，推荐 `v9.x`      | 同上                       | 高性能、节省磁盘空间的包管理器，替代 npm |
| **操作系统** | 任意支持终端的系统 | 推荐使用类 Unix 系统（macOS/Linux） | Windows 用户建议使用 WSL2 |

> ✅ 你当前的环境为：
> - `node --version` → **v22.18.0**
> - `pnpm --version` → **请确保已安装 pnpm（若未安装，请见下方说明）**
>
> ✅ **完全符合要求，可直接进行下一步。**

> 💡 **如何安装 pnpm**（若尚未安装）：
> ```bash
> npm install --save-dev pnpm
> # 或使用 corepack（Node.js 16.13+ 内置）
> corepack enable
> corepack prepare pnpm@latest --activate
> ```

---

### 1.2 默认依赖组件版本清单

项目初始化后将安装以下**精确版本**的依赖包（使用 **pnpm** 安装为开发依赖）：

| 依赖包名称 | 版本号 | 用途说明 |
|-----------|--------|----------|
| `hardhat` | `^3.0.7` | 核心框架，提供编译、测试、部署、本地节点等功能 |
| `@nomicfoundation/hardhat-toolbox-viem` | `^5.0.0` | 工具箱扩展，集成 `viem` 客户端、Etherscan 验证等 |
| `@nomicfoundation/hardhat-ignition` | `^3.0.3` | 声明式部署系统，支持模块化、可复用的合约部署流程 |
| `typescript` | `~5.8.3` | 支持在项目中使用 TypeScript 编写测试与脚本 |
| `@types/node` | `^22.18.12` | 为 Node.js API 提供类型定义，提升开发体验 |
| `forge-std` | `github:foundry-rs/forge-std#v1.9.4` | Foundry 团队开发的标准 Solidity 库，包含 `Test` 基类、断言、模糊测试支持 |
| `viem` | `^2.38.3` | 现代化 Ethereum 客户端库，替代 `ethers.js`，轻量高效 |

> ⚠️ 注意：
> - 所有依赖均通过 `pnpm add --save-dev` 安装为 **开发依赖（devDependencies）**
> - 版本由 Hardhat CLI 自动锁定，确保兼容性
> - `forge-std` 是 Git 依赖，通过 GitHub 指定分支安装

---

### 1.3 依赖安装流程说明
> 注意：一般不建议直接使用官方指令，最好按照下面的步骤手动指定版本，因为官方指令会直接安装最新版本，而最新版本，可能还不稳定。
> 执行官方指令：pnpm dlx hardhat --init

Hardhat CLI 在选择模板后会生成如下命令（但我们将使用 **pnpm** 替代 npm）：

```bash
pnpm add --save-dev \
  "hardhat@^3.0.7" \
  "@nomicfoundation/hardhat-toolbox-viem@^5.0.0" \
  "@nomicfoundation/hardhat-ignition@^3.0.3" \
  "@types/node@^22.18.12" \
  "typescript@~5.8.3" \
  "viem@^2.38.3" \
  "forge-std@github:foundry-rs/forge-std#v1.9.4"
```

> ✅ 此命令将被自动执行（或手动执行），无需使用 npm。

---

## 2. 项目搭建详细步骤

### 2.1 检查本地 Node.js 与 pnpm 环境

打开终端，依次执行以下命令，确认环境版本：

```bash
node --version
```
> 预期输出：`v22.18.0`

```bash
pnpm --version
```
> 预期输出：`9.x.x`（或更高）

如果 `pnpm` 未安装，请参考 [1.1 节](#11-系统运行环境要求) 中的安装说明。

---

### 2.2 创建项目目录并进入工作空间

在终端中执行以下命令：

```bash
mkdir my-hardhat-project
cd my-hardhat-project
```

> 💡 你可以将 `my-hardhat-project` 替换为你项目的实际名称，如 `defi-contract`、`nft-minter` 等。

---

### 2.3 初始化 Hardhat 项目（完整交互流程）

执行以下命令启动 Hardhat 初始化向导：

```bash
pnpm dlx hardhat --init
```

> 🔍 `npx` 会临时下载 `hardhat` 包并运行，无需全局安装。

系统将显示交互式菜单，请按以下顺序选择：

#### 步骤 1：选择 Hardhat 版本

```
? Which version of Hardhat would you like to use?
  Hardhat 2 (older version)
> Hardhat 3 Beta (recommended for new projects)
```

✅ 选择：**`Hardhat 3 Beta (recommended for new projects)`**  
> ✅ 原因：v3 支持现代工具链（如 `node:test`、`viem`），是未来主流。

---

#### 步骤 2：选择项目路径

```
? Where would you like to initialize the project?
> . (current directory)
```

✅ 输入：`.`  
> 表示在当前目录初始化项目。

---

#### 步骤 3：选择项目模板

```
? What type of project would you like to initialize?
> A TypeScript Hardhat project using Node Test Runner and Viem
  A TypeScript Hardhat project using Mocha and Ethers.js
```

✅ 选择：**`A TypeScript Hardhat project using Node Test Runner and Viem`**

> ✅ 优势：
> - 使用 TypeScript 提升代码安全性
> - 使用 `node:test`（Node.js 内置测试框架），无需额外依赖 Mocha/Chai
> - 集成 `viem`，现代 Ethereum 交互方式

---

#### 步骤 4：确认初始化

```
? Ok to proceed? (y/n)
```

✅ 输入：`y`

---

#### 步骤 5：是否立即安装依赖

```
Do you want to run it now? (Y/n)
```

✅ 输入：`n`  
> ❗ **关键区别**：由于我们使用 **pnpm** 而非 npm，此处选择 **`n`**，**手动执行 pnpm 安装命令**，以确保依赖版本精确匹配。

---

### 2.4 手动安装依赖包（使用 pnpm）

在项目根目录执行以下命令（使用 **pnpm**）：

```bash
pnpm add --save-dev \
  "hardhat@^3.0.7" \
  "@nomicfoundation/hardhat-toolbox-viem@^5.0.0" \
  "@nomicfoundation/hardhat-ignition@^3.0.3" \
  "@types/node@^22.18.12" \
  "typescript@~5.8.3" \
  "viem@^2.38.3" \
  "forge-std@github:foundry-rs/forge-std#v1.9.4"
```

> ⏱️ 安装时间：约 1–2 分钟（pnpm 安装速度通常快于 npm）

安装完成后，终端将显示类似信息：

```
Packages: +173
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Progress: resolved 173, reused 160, downloaded 13, added 173, done
```

> ✅ 表示依赖安装成功。

---

### 2.5 验证安装结果

检查项目根目录是否生成以下关键文件：

```bash
ls -la
```

应包含：

- `hardhat.config.ts`：主配置文件
- `package.json`：包含所有依赖
- `tsconfig.json`：TypeScript 配置
- `contracts/`：Solidity 合约目录
- `test/`：测试文件目录
- `node_modules/`：依赖包目录（由 pnpm 管理）
- `pnpm-lock.yaml`：**pnpm 的依赖锁文件**（替代 `package-lock.json`）
- `.pnpm-debug.log`（可选，调试用）

> ✅ 若以上文件均存在，表示项目初始化成功。

> 📌 **注意**：使用 pnpm 时，**不会生成 `package-lock.json`**，而是生成 **`pnpm-lock.yaml`**。

---

## 3. 运行测试并验证环境

### 3.1 执行完整测试套件

在项目根目录运行：

```bash
npx hardhat test
```

> 💡 `npx` 会调用本地安装的 `hardhat`，确保版本一致。

---

### 3.2 理解测试输出内容

预期输出如下：

```
Compiled 1 Solidity file with solc 0.8.28 (evm target: cancun)
Compiled 1 Solidity file with solc 0.8.28 (evm target: cancun)

Running Solidity tests
contracts/Counter.t.sol:CounterTest
  ✓ test_InitialValue()
  ✓ test_IncByZero()
  ✓ testFuzz_Inc(uint8) (runs: 256)

  3 passing

Running node:test tests
Counter
  ✓ Should emit the Increment event when calling the inc() function (330ms)
  ✓ The sum of the Increment events should match the current value (1155ms)

  2 passing (55624ms)
```

#### 输出解析：

| 部分 | 说明 |
|------|------|
| `Compiled 1 Solidity file...` | 使用 `solc` 编译器成功编译合约 |
| `Running Solidity tests` | 执行 `.t.sol` 文件中的 Solidity 测试 |
| `test_InitialValue()` | 初始值测试通过 |
| `testFuzz_Inc(uint8)` | 模糊测试运行 256 次，全部通过 |
| `Running node:test tests` | 执行 `.ts` 文件中的 TypeScript 测试 |
| `Should emit the Increment event...` | 事件触发测试通过 |

> ✅ **5 个测试全部通过**，表示环境搭建成功！

---

### 3.3 常见问题排查

| 问题 | 可能原因 | 解决方案 |
|------|---------|----------|
| `command not found: hardhat` | 未安装或 `npx` 失败 | 重试 `npx hardhat` 或检查网络 |
| 编译失败 | `solc` 版本不兼容 | 检查 `hardhat.config.ts` 中的 `solidity.version` |
| 测试超时 | 网络慢或资源不足 | 增加 `timeout` 配置或重启终端 |
| `viem` 报错 | 依赖未正确安装 | 删除 `node_modules` 和 `pnpm-lock.yaml`，重新 `pnpm install` |

---

## 4. 项目结构详解

### 4.1 实际项目结构

以下是**实际存在的目录与文件列表**（使用 pnpm）：

```
my-hardhat-project/
├── artifacts/               # 编译后的合约输出（由 hardhat test 自动生成）
│   ├── build-info/          # 构建信息（ABI、字节码、源码映射等）
│   └── contracts/           # 编译后的合约 JSON 文件（如 Counter.json）
├── cache/                   # 编译缓存（提高下次编译速度）
│   ├── build-info/          # 编译信息缓存
│   ├── test-artifacts/      # 测试相关缓存
│   └── compile-cache.json   # 编译缓存元数据
├── contracts/               # Solidity 智能合约源码
│   ├── Counter.sol          # 示例计数器合约
│   └── Counter.t.sol        # Solidity 测试文件（Forge 风格）
├── ignition/                # Ignition 部署模块（自动生成）
│   └── modules/             # 部署逻辑模块
│       └── Counter.ts       # 示例部署模块
├── node_modules/            # 第三方依赖包（由 pnpm 管理）
├── scripts/                 # 部署脚本（可选）
│   └── send-op-tx.ts        # 示例脚本（发送 Optimism 交易）
├── test/                    # 测试文件
│   └── Counter.ts           # TypeScript 测试文件（使用 node:test）
├── .gitignore               # Git 忽略规则
├── hardhat.config.ts        # 主配置文件
├── pnpm-lock.yaml           # ✅ pnpm 依赖锁文件（关键！）
├── package.json             # 项目元信息与依赖
├── README.md                # 项目说明文档
├── tsconfig.json            # TypeScript 配置
```

> ✅ **重要说明**：
>
> - `artifacts/` 和 `cache/` 目录**不会在初始化时创建**，而是**在第一次运行测试或编译时自动生成**。
> - 使用 **pnpm** 时，依赖解析更严格，确保 `node_modules` 结构扁平且节省磁盘空间。
> - **`pnpm-lock.yaml` 是项目依赖的唯一权威来源**，务必提交到 Git。

---

### 4.2 合约示例（Counter.sol）分析

路径：`contracts/Counter.sol`

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.28;

contract Counter {
  uint public x;

  event Increment(uint by);

  function inc() public {
    x++;
    emit Increment(1);
  }

  function incBy(uint by) public {
    require(by > 0, "incBy: increment should be positive");
    x += by;
    emit Increment(by);
  }
}
```

> ✅ 功能：
> - `x`：公开的无符号整数状态变量，初始值为 0。
> - `inc()`：将 `x` 递增 1，并发出 `Increment(1)` 事件。
> - `incBy(uint by)`：将 `x` 增加指定值 `by`，若 `by` 为 0 则 `require` 失败，回滚交易。
> - `Increment` 事件：用于前端监听数值变化。

---

### 4.3 测试文件解析

#### Solidity 测试：`contracts/Counter.t.sol`

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.28;

import {Counter} from "./Counter.sol";
import {Test} from "forge-std/Test.sol"; 

contract CounterTest is Test {
  Counter counter;

  function setUp() public {
    counter = new Counter();
  }

  function test_InitialValue() public view {
    require(counter.x() == 0, "Initial value should be 0");
  }

  function testFuzz_Inc(uint8 x) public {
    for (uint8 i = 0; i < x; i++) {
      counter.inc();
    }
    require(counter.x() == x, "Value after calling inc x times should be x");
  }

  function test_IncByZero() public {
    vm.expectRevert();
    counter.incBy(0);
  }
}
```

> ✅ 测试用例说明：
> - `test_InitialValue()`：验证 `x` 初始值为 0。
> - `testFuzz_Inc(uint8 x)`：模糊测试，对 `x` 从 0 到 255 的所有值调用 `inc()`，验证最终值等于调用次数。
> - `test_IncByZero()`：使用 `vm.expectRevert()` 断言 `incBy(0)` 会触发 `require` 失败，交易回滚。

#### TypeScript 测试：`test/Counter.ts`

```ts
import assert from "node:assert/strict";
import { describe, it } from "node:test";

import { network } from "hardhat";

describe("Counter", async function () {
  const { viem } = await network.connect();
  const publicClient = await viem.getPublicClient();

  it("Should emit the Increment event when calling the inc() function", async function () {
    const counter = await viem.deployContract("Counter");

    await viem.assertions.emitWithArgs(
      counter.write.inc(),
      counter,
      "Increment",
      [1n],
    );
  });

  it("The sum of the Increment events should match the current value", async function () {
    const counter = await viem.deployContract("Counter");
    const deploymentBlockNumber = await publicClient.getBlockNumber();

    // run a series of increments
    for (let i = 1n; i <= 10n; i++) {
      await counter.write.incBy([i]);
    }

    const events = await publicClient.getContractEvents({
      address: counter.address,
      abi: counter.abi,
      eventName: "Increment",
      fromBlock: deploymentBlockNumber,
      strict: true,
    });

    // check that the aggregated events match the current value
    let total = 0n;
    for (const event of events) {
      total += event.args.by;
    }

    assert.equal(total, await counter.read.x());
  });
});
```

> ✅ 测试用例说明：
> - 第一个 `it`：使用 `viem.assertions.emitWithArgs` 断言调用 `inc()` 会发出 `Increment(1)` 事件。
> - 第二个 `it`：部署合约后，连续调用 `incBy(1)` 到 `incBy(10)`，然后通过 `publicClient.getContractEvents` 查询所有 `Increment` 事件，累加 `by` 参数，验证总和等于 `x` 的当前值。

---

## 5. 工具链与配置说明

### 5.1 核心开发工具功能说明

| 工具 | 功能 |
|------|------|
| `node:test` | Node.js v18+ 内置测试框架，无需安装 Mocha/Chai |
| `viem` | 现代 Ethereum 客户端，支持 EIP-712、批处理、WebSocket |
| `hardhat-ignition` | 声明式部署系统，支持依赖注入与模块复用 |
| `forge-std` | 提供 `Test`、`console2`、模糊测试等 Solidity 工具 |
| `pnpm` | 快速、节省磁盘的包管理器，支持严格依赖解析 |

---

### 5.2 Hardhat 配置文件详解（hardhat.config.ts）

使用最新的 `Hardhat 3 Beta` 配置语法，支持模块化配置、多网络模拟和安全变量注入。

```ts
import type { HardhatUserConfig } from "hardhat/config";

import hardhatToolboxViemPlugin from "@nomicfoundation/hardhat-toolbox-viem";
import { configVariable } from "hardhat/config";

const config: HardhatUserConfig = {
  plugins: [hardhatToolboxViemPlugin],
  solidity: {
    profiles: {
      default: {
        version: "0.8.28",
      },
      production: {
        version: "0.8.28",
        settings: {
          optimizer: {
            enabled: true,
            runs: 200,
          },
        },
      },
    },
  },
  networks: {
    hardhatMainnet: {
      type: "edr-simulated",
      chainType: "l1",
    },
    hardhatOp: {
      type: "edr-simulated",
      chainType: "op",
    },
    sepolia: {
      type: "http",
      chainType: "l1",
      url: configVariable("SEPOLIA_RPC_URL"),
      accounts: [configVariable("SEPOLIA_PRIVATE_KEY")],
    },
  },
};

export default config;
```

> ✅ **配置亮点说明**：
>
> - **`plugins` 字段**：显式声明插件，提高可读性与控制力。
> - **`solidity.profiles`**：支持多编译配置。
>   - `default`：开发/测试使用，编译速度快。
>   - `production`：生产部署使用，启用优化器（`runs: 200`），降低 Gas 成本。
> - **`networks` 多链支持**：
>   - `hardhatMainnet`：模拟以太坊主网行为（`edr-simulated`, `l1`）。
>   - `hardhatOp`：模拟 Optimism 网络（`edr-simulated`, `op`），用于 L2 开发。
>   - `sepolia`：连接真实 Sepolia 测试网，使用 `configVariable` 安全注入 `RPC URL` 和 `私钥`，避免硬编码。
> - **`configVariable`**：来自 `hardhat/config`，提供安全的配置变量注入机制，支持环境变量、密钥库等多种来源。

---

### 5.3 TypeScript 配置说明（tsconfig.json）

使用最新的 Node.js 22 兼容配置，提升性能与现代语法支持。

```json
{
  "compilerOptions": {
    "lib": ["es2023"],
    "module": "node16",
    "target": "es2022",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "moduleResolution": "node16",
    "outDir": "dist"
  }
}
```

> ✅ **配置亮点说明**：
>
> - **`"lib": ["es2023"]`**：尝试启用 ES2023 语言特性（如 `findLast`、`replaceAll`）。
> - **`"module": "node16"`** 和 **`"moduleResolution": "node16"`**：使用 Node.js 16+ 的模块解析规则，兼容现代 `package.json` 的 `exports` 和 `imports` 字段。
> - **`"target": "es2022"`**：编译目标为 ES2022，支持 class fields、top-level await 等现代语法。
> - **`"outDir": "dist"`**：编译后的 JavaScript 文件输出到 `dist` 目录，保持项目整洁。

---

### 5.4 `tsconfig.json` 常见错误与修复：`lib` 字段不支持 `es2023`

❌ **错误信息解析**：

你看到的提示是：

```
'compilerOptions/lib' must be equal to one of the allowed values 'ES5, ES6, ES2015, ... es2021.intl, ES2022, ...'
```

这说明你在 `tsconfig.json` 中写了：

```json
"lib": ["es2023"]
```

但 `es2023` 不在 TypeScript 支持的 `lib` 列表中。虽然 TypeScript 会逐渐支持新的 ECMAScript 版本，但 `es2023` 还没有被正式加入到 `lib` 的可选值中（截至当前版本如 TS 5.x 或 4.x）。

✅ **正确做法**

#### ✅ 方法一：使用已支持的 `lib` 值

你应该将 `"es2023"` 替换为一个 TypeScript 支持的、等价的 `lib` 名称。

例如，你可以使用：

```json
"lib": ["ES2022", "DOM", "DOM.Iterable"]
```

或者更现代一点的写法（如果你用的是较新版本的 TypeScript）：

```json
"lib": ["ES2022", "DOM", "DOM.Iterable", "WebWorker"]
```

⚠️ **注意**：`es2023` 的一些特性（比如 `Array.at()`、`String.replaceAll()` 等）可能已经在 `ES2022` 或更高版本中支持了，所以通常不需要单独指定 `es2023`。

#### ✅ 方法二：检查你的 TypeScript 版本

运行以下命令查看你的 TypeScript 版本：

```bash
tsc --version
```

如果你使用的是 TypeScript 5.0+，可以尝试使用 `ES2023`，但目前官方仍不推荐直接写 `es2023`。更安全的方式是使用 `ESNext` 或 `ES2022`。

#### ✅ 推荐的写法（现代项目）

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["DOM", "DOM.Iterable", "WebWorker"],
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

🔍 **如何查找支持的 `lib` 值？**

你可以通过以下方式查看：

- 在 VS Code 中打开 `tsconfig.json`，把鼠标放在 `lib` 上，会弹出建议列表。
- 查看 [TypeScript 官方文档](https://www.typescriptlang.org/tsconfig)
- 或者运行：
  ```bash
  tsc --help | grep -A 10 "lib"
  ```

🧩 **补充说明**

- `lib` 是告诉 TypeScript 应该包含哪些内置库（比如 DOM API、Array 方法等），而不是目标语言版本。
- 目标语言版本由 `target` 控制，比如 `"target": "ES2022"`。
- 所以你也可以这样写：
  ```json
  "target": "ES2022",
  "lib": ["DOM", "DOM.Iterable"]
  ```
  这表示：
  - 编译成 ES2022 语法；
  - 启用浏览器环境的全局对象（如 `window`, `document`）和迭代器支持。

✅ **总结**

你现在的错误是因为：

> `es2023` 不在 TypeScript 允许的 `lib` 值中。

✅ **解决方法**：

将：

```json
"lib": ["es2023"]
```

改为：

```json
"lib": ["ES2022", "DOM", "DOM.Iterable"]
```

或根据实际需求选择合适的 `lib` 组合。

---

## 6. 常用命令清单（适配 pnpm）

| 命令 | 中文翻译 | 作用解释 |
|------|--------|----------|
| `npx hardhat test` | 运行项目中所有的测试 | 执行项目中所有的测试文件，包括 Solidity 编写的 Foundry 风格单元测试 和 使用 `node:test` 编写的 TypeScript 集成测试。 |
| `npx hardhat test solidity` | 仅运行 Solidity 测试 | 专门运行用 Solidity 编写的测试（通常位于 `test/solidity/` 目录下），适合快速验证合约逻辑。 |
| `npx hardhat test nodejs` | 仅运行 Node.js 原生测试 | 仅运行使用 Node.js 内置测试框架 `node:test` 编写的 TypeScript 测试（通常位于 `test/nodejs/` 目录下），结合 `viem` 进行更复杂的链上交互测试。 |
| `npx hardhat ignition deploy ignition/modules/Counter.ts` | 将 Counter 模块部署到本地模拟链 | 使用 Hardhat Ignition 部署系统，将 `Counter.ts` 定义的部署模块运行在本地 Hardhat 网络（localhost）上，用于本地开发和调试。 |
| `npx hardhat keystore set SEPOLIA_PRIVATE_KEY` | 使用 hardhat-keystore 插件设置 Sepolia 私钥 | 安全地将用于 Sepolia 测试网的账户私钥存储到本地密钥库中，避免明文暴露在环境变量或配置文件中。 |
| `npx hardhat ignition deploy --network sepolia ignition/modules/Counter.ts` | 将 Counter 模块部署到 Sepolia 测试网 | 将 `Counter.ts` 部署模块实际发送到以太坊 Sepolia 测试网络，需要已配置 `SEPOLIA_PRIVATE_KEY` 和网络连接信息。 |
| `npx hardhat compile` | 手动编译所有合约 | 编译 `contracts/` 目录下的所有 Solidity 源文件，生成 ABI 和字节码，供测试和部署使用。 |
| `npx hardhat node` | 启动本地 Ethereum 节点（端口 8545） | 启动一个本地的、内存中的以太坊测试节点，用于快速测试和调试，无需连接外部网络。 |
| `npx hardhat run scripts/send-op-tx.ts` | 运行部署脚本 | 执行指定的 TypeScript 脚本文件（如 `send-op-tx.ts`），可用于自定义部署逻辑或与合约交互。 |
| `pnpm install` | 安装所有依赖 | 根据 `pnpm-lock.yaml` 重新安装依赖（替代 `npm install`） |
| `pnpm add -D <package>` | 添加开发依赖 | 例如：`pnpm add -D @types/chai` |

---
## 7. 本地 Hardhat 节点运行输出示例（真实交互记录）

当在项目根目录执行 `npx hardhat node` 命令后，终端将输出以下信息，表示本地以太坊节点已成功启动：

```bash
npx hardhat node
Started HTTP and WebSocket JSON-RPC server at http://127.0.0.1:8545/

Accounts
========

WARNING: Funds sent on live network to accounts with publicly known private keys WILL BE LOST.

Account #0:  0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266 (10000 ETH)
Private Key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

Account #1:  0x70997970c51812dc3a010c7d01b50e0d17dc79c8 (10000 ETH)
Private Key: 0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d

Account #2:  0x3c44cdddb6a900fa2b585dd299e03d12fa4293bc (10000 ETH)
Private Key: 0x5de4111afa1a4b94908f83103eb1f1706367c2e68ca870fc3fb9a804cdab365a

Account #3:  0x90f79bf6eb2c4f870365e785982e1f101e93b906 (10000 ETH)
Private Key: 0x7c852118294e51e653712a81e05800f419141751be58f605c371e15141b007a6

Account #4:  0x15d34aaf54267db7d7c367839aaf71a00a2c6a65 (10000 ETH)
Private Key: 0x47e179ec197488593b187f80a00eb0da91f1b9d0b13f8733639f19c30a34926a

Account #5:  0x9965507d1a55bcc2695c58ba16fb37d819b0a4dc (10000 ETH)
Private Key: 0x8b3a350cf5c34c9194ca85829a2df0ec3153be0318b5e2d3348e872092edffba

Account #6:  0x976ea74026e726554db657fa54763abd0c3a0aa9 (10000 ETH)
Private Key: 0x92db14e403b83dfe3df233f83dfa3a0d7096f21ca9b0d6d6b8d88b2b4ec1564e

Account #7:  0x14dc79964da2c08b23698b3d3cc7ca32193d9955 (10000 ETH)
Private Key: 0x4bbbf85ce3377467afe5d46f804f221813b2bb87f24d81f60f1fcdbf7cbf4356

Account #8:  0x23618e81e3f5cdf7f54c3d65f7fbc0abf5b21e8f (10000 ETH)
Private Key: 0xdbda1821b80551c9d65939329250298aa3472ba22feea921c0cf5d620ea67b97

Account #9:  0xa0ee7a142d267c1f36714e4a8f75612f20a79720 (10000 ETH)
Private Key: 0x2a871d0798f97d79848a013d4936a73bf4cc922c825d33c1cf7073dff6d409c6

Account #10: 0xbcd4042de499d14e55001ccbb24a551f3b954096 (10000 ETH)
Private Key: 0xf214f2b2cd398c806f84e317254e0f0b801d0643303237d97a22a48e01628897

Account #11: 0x71be63f3384f5fb98995898a86b02fb2426c5788 (10000 ETH)
Private Key: 0x701b615bbdfb9de65240bc28bd21bbc0d996645a3dd57e7b12bc2bdf6f192c82

Account #12: 0xfabb0ac9d68b0b445fb7357272ff202c5651694a (10000 ETH)
Private Key: 0xa267530f49f8280200edf313ee7af6b827f2a8bce2897751d06a843f644967b1

Account #13: 0x1cbd3b2770909d4e10f157cabc84c7264073c9ec (10000 ETH)
Private Key: 0x47c99abed3324a2707c28affff1267e45918ec8c3f20b8aa892e8b065d2942dd

Account #14: 0xdf3e18d64bc6a983f673ab319ccae4f1a57c7097 (10000 ETH)
Private Key: 0xc526ee95bf44d8fc405a158bb884d9d1238d99f0612e9f33d006bb0789009aaa

Account #15: 0xcd3b766ccdd6ae721141f452c550ca635964ce71 (10000 ETH)
Private Key: 0x8166f546bab6da521a8369cab06c5d2b9e46670292d85c875ee9ec20e84ffb61

Account #16: 0x2546bcd3c84621e976d8185a91a922ae77ecec30 (10000 ETH)
Private Key: 0xea6c44ac03bff858b476bba40716402b03e41b8e97e276d1baec7c37d42484a0

Account #17: 0xbda5747bfd65f08deb54cb465eb87d40e51b197e (10000 ETH)
Private Key: 0x689af8efa8c651a91ad287602527f3af2fe9f6501a7ac4b061667b5a93e037fd

Account #18: 0xdd2fd4581271e230360230f9337d5c0430bf44c0 (10000 ETH)
Private Key: 0xde9be858da4a475276426320d5e9262ecfc3ba460bfac56360bfa6c4c28b4ee0

Account #19: 0x8626f6940e2eb28930efb4cef49b2d1f2c9c1199 (10000 ETH)
Private Key: 0xdf57089febbacf7ba0bc227dafbffa9fc08a93fdc68e1e42411a14efcf23656e

WARNING: Funds sent on live network to accounts with publicly known private keys WILL BE LOST.
```

### 🔍 输出解析

| 组件 | 说明 |
|------|------|
| **HTTP and WebSocket JSON-RPC server** | 启动了 HTTP 和 WebSocket 两种协议的 JSON-RPC 服务，监听地址为 `http://127.0.0.1:8545/`，可通过此地址与本地节点交互。 |
| **Accounts** | 列出了 20 个预生成的以太坊账户，每个账户初始拥有 10,000 ETH。这些账户用于本地开发和测试，无需手动创建。 |
| **Private Keys** | 每个账户对应的私钥以明文形式显示。**这些私钥是公开的，绝对不能用于主网**，否则资金将被窃取。 |
| **Chain ID** | 默认链 ID 为 `31337`，这是 Hardhat Network 的标准标识。 |
| **Warning** | 明确警告：切勿将资金发送到这些公钥已知的账户，否则在主网上资金将丢失。 |

### ✅ 使用场景

- **本地部署测试**：使用 `npx hardhat run scripts/deploy.ts --network localhost` 部署合约。
- **前端连接**：将 MetaMask 网络切换到 `http://localhost:8545`，并导入任意一个账户的私钥，即可进行前端交互测试。
- **调试交易**：通过 Hardhat Console 或脚本模拟复杂交易场景。

> ⚠️ **安全提醒**：这些账户仅用于本地开发。在连接测试网或主网时，必须使用独立、安全的私钥。
---
## 8. 总结与后续建议

✅ **本指南已完成以下目标**：
- 明确列出 环境要求与依赖版本（**使用 pnpm**）
- 提供 清晰、分步、可复现的搭建流程（**适配 pnpm 安装流程**）
- 详细解析 项目结构与核心文件（**包含 `pnpm-lock.yaml`**）
- 提供 常见问题排查方案
- 支持 现代开发栈（TypeScript + viem + node:test + pnpm）

🚀 **后续建议**：
- 添加自己的合约 到 `contracts/` 目录
- 编写测试 在 `test/` 中
- 使用 Ignition 构建可复用的部署模块
- 集成前端 使用 `viem` 连接钱包与合约
- 部署到测试网（如 Sepolia）使用 Alchemy/Infura
- **将 `pnpm-lock.yaml` 提交到 Git，确保团队依赖一致**

📌 **版本**：基础版 - V1  
📅 **更新时间**：2025年10月21日  
👨‍💻 **作者**：RainWeb3
