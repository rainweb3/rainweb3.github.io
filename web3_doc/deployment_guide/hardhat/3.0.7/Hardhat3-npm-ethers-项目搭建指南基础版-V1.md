# Hardhat3.0.7-npm-ethers-typescript 项目搭建指南（基础版 - V1）  
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
   - 5.4 解决 `lib: es2023` 编译错误（重要补充）

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
| **npm**     | `>=8.0.0`，推荐 `v10.9.3`  | 同上                       | Node.js 包管理器，用于安装和管理依赖 |
| **操作系统** | 任意支持终端的系统 | 推荐使用类 Unix 系统（macOS/Linux） | Windows 用户建议使用 WSL2 |

> ✅ 你当前的环境为：
> - `node --version` → **v22.18.0**
> - `npm --version` → **10.9.3**
>
> ✅ **完全符合要求，可直接进行下一步。**

---

### 1.2 默认依赖组件版本清单

项目初始化后将安装以下**精确版本**的依赖包：

| 依赖包名称 | 版本号 | 用途说明 |
|-----------|--------|----------|
| `hardhat` | `^3.0.7` | 核心框架，提供编译、测试、部署、本地节点等功能 |
| `@nomicfoundation/hardhat-toolbox-mocha-ethers` | `^3.0.0` | 工具箱扩展，集成 `ethers.js` 客户端、Mocha 测试框架等 |
| `@nomicfoundation/hardhat-ignition` | `^3.0.3` | 声明式部署系统，支持模块化、可复用的合约部署流程 |
| `typescript` | `~5.8.0` | 支持在项目中使用 TypeScript 编写测试与脚本 |
| `@types/node` | `^22.18.11` | 为 Node.js API 提供类型定义，提升开发体验 |
| `forge-std` | `github:foundry-rs/forge-std#v1.9.4` | Foundry 团队开发的标准 Solidity 库，包含 `Test` 基类、断言、模糊测试支持 |
| `ethers` | `^6.15.0` | Ethereum 客户端库，用于与链交互、签名、交易发送等 |
| `chai` | `^5.3.3` | 断言库，用于编写清晰的测试断言 |
| `mocha` | `^11.7.4` | 测试运行框架，配合 Chai 使用 |
| `@types/mocha` | `^10.0.10` | Mocha 的 TypeScript 类型定义 |
| `@types/chai` | `^4.3.20` | Chai 的 TypeScript 类型定义 |
| `@types/chai-as-promised` | `^8.0.2` | Chai 插件，支持异步断言 |

> ⚠️ 注意：
> - 所有依赖均通过 `npm install --save-dev` 安装为 **开发依赖（devDependencies）**
> - 版本由 `package.json` 中的 `devDependencies` 字段定义，使用语义化版本控制（`^` 和 `~`）
> - `forge-std` 是 Git 依赖，通过 GitHub 指定分支安装

---

### 1.3 依赖安装流程说明

> 注意：一般不建议直接使用官方指令，最好按照下面的步骤手动指定版本，因为官方指令会直接安装最新版本，而最新版本，可能还不稳定。
> 执行官方指令：npx hardhat --init

Hardhat CLI 在选择模板后会生成如下命令：

```bash
npm install --save-dev "hardhat@^3.0.7" "@nomicfoundation/hardhat-toolbox-mocha-ethers@^3.0.0" "@nomicfoundation/hardhat-ignition@^3.0.3" "@types/node@^22.18.11" "forge-std@github:foundry-rs/forge-std#v1.9.4" "typescript@~5.8.0" "ethers@^6.15.0" "chai@^5.3.3" "@types/chai@^4.3.20" "@types/chai-as-promised@^8.0.2" "@types/mocha@^10.0.10" "mocha@^11.7.4"
```

> ✅ 此命令将被自动执行，无需手动输入。

---

## 2. 项目搭建详细步骤

### 2.1 检查本地 Node.js 与 npm 环境

打开终端，依次执行以下命令，确认环境版本：

```bash
node --version
```
> 预期输出：`v22.18.0`

```bash
npm --version
```
> 预期输出：`10.9.3`

如果版本不符，请使用以下方式升级或管理版本：

- 推荐使用 **`nvm`（Node Version Manager）** 管理多版本 Node.js：
  ```bash
  # 安装 nvm（首次）
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

  # 安装指定版本
  nvm install 22.18.0

  # 使用该版本
  nvm use 22.18.0
  ```

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
npx hardhat --init
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
> A TypeScript Hardhat project using Mocha and Ethers.js
  A TypeScript Hardhat project using Node Test Runner and Viem
```

✅ 选择：**`A TypeScript Hardhat project using Mocha and Ethers.js`**

> ✅ 优势：
> - 使用 TypeScript 提升代码安全性
> - 使用 `mocha` + `chai` 作为测试框架，稳定成熟
> - 集成 `ethers.js`，广泛支持的 Ethereum 交互库

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

✅ 输入：`Y`

> 系统将自动执行 `npm install` 安装所有必需依赖。

---

### 2.4 自动安装依赖包（根据实际命令）

系统将执行以下命令（与最新 `package.json` 一致）：

```bash
npm install --save-dev \
  "hardhat@^3.0.7" \
  "@nomicfoundation/hardhat-toolbox-mocha-ethers@^3.0.0" \
  "@nomicfoundation/hardhat-ignition@^3.0.3" \
  "@types/node@^22.18.11" \
  "forge-std@github:foundry-rs/forge-std#v1.9.4" \
  "typescript@~5.8.0" \
  "ethers@^6.15.0" \
  "chai@^5.3.3" \
  "@types/chai@^4.3.20" \
  "@types/chai-as-promised@^8.0.2" \
  "@types/mocha@^10.0.10" \
  "mocha@^11.7.4"
```

> ⏱️ 安装时间：约 1–3 分钟，取决于网络速度。

安装完成后，终端将显示类似信息：

```
added 275 packages from 102 contributors in 2m
found 0 vulnerabilities
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
- `node_modules/`：依赖包目录
- `package-lock.json`：依赖锁文件

> ✅ 若以上文件均存在，表示项目初始化成功。

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
    ✔ test_InitialValue()
    ✔ test_IncByZero()
    ✔ testFuzz_Inc(uint8) (runs: 256)

  3 passing

Running Mocha tests

  Counter
    ✔ Should emit the Increment event when calling the inc() function (1994ms)
    ✔ The sum of the Increment events should match the current value (2586ms)

  2 passing (5s)
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
| `ethers` 报错 | 依赖未正确安装 | 删除 `node_modules` 和 `package-lock.json`，重新 `npm install` |

---

## 4. 项目结构详解

### 4.1 实际项目结构

以下是**实际存在的目录与文件列表**：

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
├── node_modules/            # 第三方依赖包
├── scripts/                 # 部署脚本（可选）
│   └── send-op-tx.ts        # 示例脚本（发送 Optimism 交易）
├── test/                    # 测试文件
│   └── Counter.ts           # TypeScript 测试文件（使用 node:test）
├── .gitignore               # Git 忽略规则
├── hardhat.config.ts        # 主配置文件
├── package-lock.json        # 依赖锁文件
├── package.json             # 项目元信息与依赖
├── README.md                # 项目说明文档
├── tsconfig.json            # TypeScript 配置
```

> ✅ **重要说明**：
>
> - `artifacts/` 和 `cache/` 目录**不会在初始化时创建**，而是**在第一次运行测试或编译时自动生成**。
> - 当你执行 `npx hardhat test contracts/Counter.t.sol` 时，Hardhat 会：
>   1. 编译 `Counter.sol`
>   2. 将编译结果存储在 `artifacts/contracts/Counter.json`
>   3. 生成构建信息到 `artifacts/build-info/`
>   4. 缓存编译结果到 `cache/compile-cache.json`
>   5. 执行测试
>
> > 📌 **因此，`artifacts` 和 `cache` 是“动态生成”的，仅在需要时才出现。**

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
import { expect } from "chai";
import { network } from "hardhat";

const { ethers } = await network.connect();

describe("Counter", function () {
  it("Should emit the Increment event when calling the inc() function", async function () {
    const counter = await ethers.deployContract("Counter");

    await expect(counter.inc()).to.emit(counter, "Increment").withArgs(1n);
  });

  it("The sum of the Increment events should match the current value", async function () {
    const counter = await ethers.deployContract("Counter");
    const deploymentBlockNumber = await ethers.provider.getBlockNumber();

    // run a series of increments
    for (let i = 1; i <= 10; i++) {
      await counter.incBy(i);
    }

    const events = await counter.queryFilter(
      counter.filters.Increment(),
      deploymentBlockNumber,
      "latest",
    );

    // check that the aggregated events match the current value
    let total = 0n;
    for (const event of events) {
      total += event.args.by;
    }

    expect(await counter.x()).to.equal(total);
  });
});
```

> ✅ 测试用例说明：
>
> - 第一个 `it`：使用 `ethers.deployContract` 部署 `Counter` 合约，然后通过 `expect(...).to.emit(...)` 断言调用 `inc()` 会发出 `Increment(1)` 事件。使用 `1n` 表示 BigInt，确保与 Solidity 中的 `uint` 类型精确匹配。
> - 第二个 `it`：
>   - 使用 `getDeploymentBlockNumber` 获取部署后的区块号，作为事件查询的起始点。
>   - 连续调用 `incBy(i)` 10 次。
>   - 使用 `counter.queryFilter(counter.filters.Increment(), ...)` 查询从部署到当前的所有 `Increment` 事件。
>   - 使用 `total` 累加所有事件的 `by` 参数（初始为 `0n`，确保使用 BigInt运算）。
>   - 最后使用 `expect(await counter.x()).to.equal(total)` 验证状态变量 `x` 的值等于所有事件参数的总和。
>
> ✅ **亮点**：
> - 使用 `ethers.deployContract()` 替代旧的 `getContractFactory().deploy()`，语法更简洁。
> - 显式使用 `BigInt` (`1n`, `0n`) 以确保与 Solidity 的 `uint` 完全兼容，避免类型错误。
> - 使用 `counter.filters.Increment()` 构造事件过滤器，提高可读性和类型安全性。
> - 使用 `queryFilter` 结合部署区块号，确保只查询相关事件，避免污染。

---

## 5. 工具链与配置说明

### 5.1 核心开发工具功能说明

| 工具 | 功能 |
|------|------|
| `mocha` | 测试运行框架，支持异步测试、钩子函数（beforeEach, afterEach） |
| `chai` | 断言库，提供 `expect`、`should`、`assert` 三种风格 |
| `ethers` | Ethereum 客户端库，支持部署、交互、签名、事件监听 |
| `hardhat-ignition` | 声明式部署系统，支持依赖注入与模块复用 |
| `forge-std` | 提供 `Test`、`console2`、模糊测试等 Solidity 工具 |

---

### 5.2 Hardhat 配置文件详解（hardhat.config.ts）

```ts
import type { HardhatUserConfig } from "hardhat/config";

import hardhatToolboxMochaEthersPlugin from "@nomicfoundation/hardhat-toolbox-mocha-ethers";
import { configVariable } from "hardhat/config";

const config: HardhatUserConfig = {
  plugins: [hardhatToolboxMochaEthersPlugin],
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
> - `plugins`: 使用插件机制加载 `@nomicfoundation/hardhat-toolbox-mocha-ethers`，实现更灵活的配置管理。
> - `solidity.profiles`: 支持多编译配置文件，`default` 用于开发，`production` 启用优化器用于生产环境部署。
> - `networks.hardhatMainnet` / `hardhatOp`: 新增模拟 L1 和 OP Stack 链的本地网络类型，便于跨链应用开发。
> - `configVariable`: 安全地引用环境配置变量，替代明文 `process.env`，增强安全性与可维护性。
> - `type: "http"` 和 `chainType`: 更精确地描述网络连接属性，提升配置语义清晰度。

---

### 5.3 TypeScript 配置说明（tsconfig.json）

```json
/* Based on https://github.com/tsconfig/bases/blob/501da2bcd640cf95c95805783e1012b992338f28/bases/node22.json */
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
> - `"lib": ["es2023"]`：明确指定包含 ES2023 的标准库，提供最新的语言特性支持。
> - `"module": "node16"`：使用 Node.js 16 引入的原生 ESM 模块系统，支持 `.mjs` 和混合导入导出。
> - `"target": "es2022"`：编译目标为 ECMAScript 2022，平衡新特性和兼容性。
> - `"moduleResolution": "node16"`：与 `"module": "node16"` 匹配，启用 Node.js 16 的模块解析策略，正确处理 ESM 和 CJS 互操作。
> - `"outDir": "dist"`：编译输出目录设置为 `dist`，便于分离源码与构建产物。
> - `"strict": true`：开启严格模式，包括 `noImplicitAny`、`strictNullChecks` 等，提升代码质量与安全性。

---

### 5.4 解决 `lib: es2023` 编译错误（重要补充）

你遇到的问题是：

> `'compilerOptions'lib'` must be equal to one of the allowed values: 'ES5', 'ES6', 'ES2015', ...

这说明你在 `tsconfig.json` 文件中配置的 `"lib": ["es2023"]` **不被 TypeScript 编译器支持**，因为当前版本的 TypeScript 不支持 `es2023` 作为 `lib` 的值。

#### 🔍 问题原因

TypeScript 的 `lib` 选项用于指定内置库文件（如 DOM、Array、Promise 等），这些库对应的是 ECMAScript 标准的某个版本。  
虽然 ES2023 是一个真实存在的标准（比如引入了 `Array.fromAsync`、`Atomics.waitAsync` 等），但 **TypeScript 的 `lib` 字段目前还没有正式支持 `es2023` 这个名称**。

你看到的错误提示列出了所有允许的 `lib` 值，其中最新的可能是 `ES2022` 或 `ESNext`。

#### ✅ 解决方案

##### ✅ 方案一：使用 `ES2022` 替代 `es2023`

```json
"lib": ["ES2022"]
```

这是目前最接近 `es2023` 的可用选项。它包含了到 2022 年为止的所有 ES 特性，比如：

- `BigInt`
- `Symbol.asyncIterator`
- `WeakRef`, `FinalizationRegistry`
- `Array.prototype.at()`
- 等等

> 注意：ES2023 中的新特性（如 `Array.fromAsync`, `String.prototype.replaceAll`, `Array.prototype.findLast`）在 `ES2022` 中**没有包含**，所以如果你用到了这些，可能需要手动添加或使用 polyfill。

##### ✅ 方案二：使用 `ESNext`（推荐）

如果你希望启用最新的实验性或未来特性（包括 ES2023 的内容），可以使用：

```json
"lib": ["ESNext"]
```

`ESNext` 包含了所有已知的最新 ECMAScript 特性，包括尚未稳定发布的提案。但注意：

- 它可能包含一些不稳定或非标准的 API。
- 在生产环境中需谨慎使用。

##### ✅ 方案三：手动添加特定的 lib（高级用法）

如果你只想启用某些新特性，比如 `Array.fromAsync`，你可以尝试添加：

```json
"lib": ["ES2022", "ES2023.Array"]
```

但目前 TypeScript **不支持这种粒度的命名方式**，所以不可行。

#### 📌 最佳实践建议

| 目标 | 推荐配置 |
|------|---------|
| 使用现代 JS 特性（到 2022 年） | `"lib": ["ES2022"]` |
| 使用最新特性（包括 ES2023） | `"lib": ["ESNext"]` |
| 生产环境 + 兼容性 | `"lib": ["DOM", "DOM.Iterable", "ES2022"]` |

#### 🧩 补充：检查你的 TypeScript 版本

运行以下命令查看你当前的 TypeScript 版本：

```bash
tsc --version
```

确保你使用的是较新的版本（例如 v5.0+），因为 `ES2022` 和 `ESNext` 支持在较新版本中更好。

#### ✅ 修改后的 tsconfig.json 示例

```json
{
  "compilerOptions": {
    "lib": [
      "ES2022"
    ],
    "module": "node16",
    "target": "es2022",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "moduleResolution": "node16",
    "outDir": "dist",
    "forceConsistentCasingInFileNames": true
  }
}
```

或者如果你想启用更多未来特性：

```json
"lib": ["ESNext"]
```

#### 🚀 小贴士：如何知道哪些 `lib` 可用？

你可以通过以下方式查看：

- 查看 [TypeScript 官方文档](https://www.typescriptlang.org/tsconfig#lib)
- 或者运行：
  ```bash
  tsc --help | grep -A 10 "lib"
  ```

#### ✅ 总结

> ❌ 错误原因：`"es2023"` 不是 TypeScript 支持的 `lib` 值。  
> ✅ 正确做法：改用 `"ES2022"` 或 `"ESNext"`。

如果你正在使用 Node.js 22，并且想支持 ES2023 的特性，推荐使用：

```json
"lib": ["ESNext"]
```

并结合 `target: "es2022"` 或 `es2023`（如果编译器支持），同时确保项目中使用了对应的 polyfill 或 runtime 支持。

---

## 6. 常用命令清单

| **命令** | **中文翻译** | **作用解释** | **使用场景** | **注意事项** |
|----------|--------------|--------------|--------------|--------------|
| `npx hardhat test` | 运行项目中所有的测试 | 执行项目中所有的测试文件，包括 Solidity 编写的 Foundry 风格单元测试 和 使用 `mocha+chai` 编写的 TypeScript 集成测试。 | 开发过程中全面验证合约逻辑和集成逻辑 | 需确保测试环境（如 Hardhat Network）已正确配置 |
| `npx hardhat test solidity` | 仅运行 Solidity 编写的单元测试 | 仅运行使用 Foundry 风格编写的 `.sol` 测试文件，验证合约内部逻辑。 | 快速验证合约内部逻辑，无需启动 JavaScript/TypeScript 环境 | 要求 Solidity 测试文件位于 `contracts/` 目录下，且符合 Foundry 风格（如使用 `assertEq` 等） |
| `npx hardhat test mocha` | 仅运行使用 Mocha + ethers.js 编写的 TypeScript 集成测试 | 仅运行 `.ts` 测试文件，执行基于 Mocha 框架的集成测试。 | 测试合约与外部交互、复杂场景模拟、跨合约调用等 | 需安装 `@nomicfoundation/hardhat-ethers` 和 `mocha`，测试文件通常为 `.ts` 后缀 |
| `npx hardhat compile` | 手动编译所有合约 | 编译 `contracts/` 目录下的所有 Solidity 源文件，生成 ABI 和字节码，供测试和部署使用。 | 在部署或测试前手动触发编译 | 编译结果存储在 `artifacts/` 目录 |
| `npx hardhat node` | 启动本地 Ethereum 节点（端口 8545） | 启动一个本地的、内存中的以太坊测试节点，用于快速测试和调试，无需连接外部网络。 | 本地开发、调试、模拟交易 | 默认链 ID 为 31337，提供 20 个预充值账户 |
| `npx hardhat run scripts/send-op-tx.ts` | 运行部署脚本 | 执行指定的 TypeScript 脚本文件（如 `deploy.ts`），可用于自定义部署逻辑或与合约交互。 | 执行一次性脚本，如部署、升级、批量调用 | 脚本需导出默认函数或使用 `main()` 执行 |
| `npx hardhat ignition deploy ignition/modules/Counter.ts` | 将 Counter 模块部署到本地模拟链 | 使用 Hardhat Ignition 部署系统，将 `Counter.ts` 定义的部署模块运行在本地 Hardhat 网络（localhost）上，用于本地开发和调试。 | 本地开发、调试部署流程 | 默认连接到 `localhost` 网络（Hardhat 内置），无需私钥 |
| `npx hardhat keystore set SEPOLIA_PRIVATE_KEY` | 通过 `hardhat-keystore` 插件安全地设置 Sepolia 网络的私钥配置变量 | 首次配置部署账户私钥，避免明文暴露在环境变量中 | 首次配置部署账户私钥，提高安全性 | 私钥将加密存储在 `.hardhat/keystore` 目录；需确保插件已安装 |
| `npx hardhat ignition deploy --network sepolia ignition/modules/Counter.ts` | 将 Counter 模块部署到 Sepolia 测试网 | 将 `Counter.ts` 部署模块实际发送到以太坊 Sepolia 测试网络，需要已配置 `SEPOLIA_RPC_URL` 和 `PRIVATE_KEY`。 | 将合约部署到公共测试网进行真实环境测试 | 必须提前设置 `SEPOLIA_PRIVATE_KEY`；账户需有 Sepolia ETH 用于支付 Gas |

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
- 明确列出 环境要求与依赖版本
- 提供 清晰、分步、可复现的搭建流程
- 详细解析 项目结构与核心文件
- 提供 常见问题排查方案
- 支持 现代开发栈（TypeScript + ethers + mocha+chai）

🚀 **后续建议**：
- 添加自己的合约 到 `contracts/` 目录
- 编写测试 在 `test/` 中
- 使用 Ignition 构建可复用的部署模块
- 集成前端 使用 `ethers` 连接钱包与合约
- 部署到测试网（如 Sepolia）使用 Alchemy/Infura

📌 **版本**：基础版 - V1  
📅 **更新时间**：2025年10月21日  
👨‍💻 **作者**：RainWeb3

---
