# node-npm-Hardhat3-基础版安装-V1

---
**更新日期：2025年10月8日**

## 目录
1. 环境准备  
2. 新建并初始化项目  
3. 安装过程详解  
4. 项目结构解析  
5. 验证安装与编译  
6. 后续开发建议  

---

### 1. 环境准备

在开始之前，请确保你的开发环境已正确配置：

- **Node.js 版本**：`v22.18.0`  
- **npm 版本**：`10.9.3`  

验证命令：

```bash
node -v
npm -v
```

> ✅ 输出应分别为 `v22.18.0` 和 `10.9.3`，表示环境满足要求。

---

### 2. 新建并初始化项目

#### 步骤 1：启动 Hardhat 初始化流程

在终端中运行以下命令：

```bash
npx hardhat --init
```

系统将显示欢迎界面，并引导你完成一系列交互式选择。

<img width="608" height="187" alt="image" src="https://github.com/user-attachments/assets/c47550f6-2daf-4b8e-b3cc-f0cbb0ade377" />

> 📌 图片说明：Hardhat v3.0.6 欢迎界面，顶部为蓝色像素风格的 "Hardhat" 标志。  
> ✅ 显示版本信息：`Welcome to Hardhat v3.0.6`

---

#### 步骤 2：选择 Hardhat 版本

系统提示选择使用哪个版本的 Hardhat：

```
? Which version of Hardhat would you like to use?
  > Hardhat 3 Beta (recommended for new projects)
  Hardhat 2 (older version)
```

👉 选择：**Hardhat 3 Beta**

<img width="689" height="258" alt="image" src="https://github.com/user-attachments/assets/795cf015-f93e-4387-97be-ffdfc2f1993c" />

> 📌 图片说明：版本选择界面，高亮选中 “Hardhat 3 Beta”，推荐用于新项目。

---

#### 步骤 3：指定项目路径

接下来，系统询问项目初始化路径：

```
? Where would you like to initialize the project?
Please provide either a relative or an absolute path:
```

输入当前目录 `.` 或其他目标路径。

👉 输入：`.`

<img width="576" height="275" alt="image" src="https://github.com/user-attachments/assets/dca3fc35-69e6-435c-9d76-5a66558557ac" />

> 📌 图片说明：路径输入阶段，光标停留在空白行等待输入，用户即将输入 `.`。

---

#### 步骤 4：选择项目类型

系统提供两种 TypeScript 项目模板：

```
? What type of project would you like to initialize?
  A TypeScript Hardhat project using Node Test Runner and Viem
  A TypeScript Hardhat project using Mocha and Ethers.js
```

👉 选择第一个选项：**A TypeScript Hardhat project using Node Test Runner and Viem**

这是 Hardhat v3 的现代默认方案，支持更高效的测试和部署流程。
<img width="673" height="331" alt="image" src="https://github.com/user-attachments/assets/a693411a-24d6-47da-a948-909b522a7402" />


> 📌 图片说明：项目类型选择界面，已自动选中“Node Test Runner + Viem”选项。

---

#### 步骤 5：确认是否立即安装依赖

系统会提示你需要运行以下命令来安装依赖：

```bash
npm install --save-dev "hardhat@3.0.6" "@nomicfoundation/hardhat-toolbox-viem@5.0.0" "@nomicfoundation/hardhat-ignition@3.0.0" "@types/node@22.8.5" "forge-std@foundry-rs/forge-std#v1.9.4" "typescript@5.8.0" "viem@2.30.0"
```

然后询问：

```
Do you want to run it now? (Y/n)
```

👉 输入：`true` 或直接按回车（默认 Y）

<img width="909" height="433" alt="image" src="https://github.com/user-attachments/assets/27f5c7c5-5f94-4606-836a-4b5921c4b587" />

> 📌 图片说明：确认是否执行安装命令，用户输入 `true`，触发 npm 安装流程。

---

### 3. 安装过程详解

当用户选择 `true` 后，Hardhat 自动执行 `npm install` 命令。

<img width="1635" height="609" alt="image" src="https://github.com/user-attachments/assets/721e67d9-a409-4930-aa87-f9284e363b17" />

> 📌 图片说明：npm 安装过程输出日志，显示：
>
> - 成功添加了 **171 个包**
> - 耗时约 2 分钟
> - 提示 `53 packages are looking for funding`
> - 最终提示：✅ Dependencies installed ✨
> - 鼓励用户给 Hardhat GitHub 项目点赞 🌟

> 💡 注意：安装过程中会下载多个核心依赖：
>
> - `hardhat@3.0.6`：主框架
> - `hardhat-toolbox-viem`：集成 Viem 支持
> - `hardhat-ignition`：智能合约部署模块
> - `viem@2.30.0`：现代 Ethereum SDK
> - `typescript@5.8.0`：TypeScript 编译器
> - `@types/node@22.8.5`：Node.js 类型定义
> - `forge-std`：来自 Foundry 的标准库（用于测试）

---

### 4. 项目结构解析

初始化完成后，项目根目录结构如下所示：

```
MY-HARDHAT3/
├── contracts/
│   ├── Counter.sol
│   └── Counter.t.sol
├── ignition/
│   └── modules/
│       └── Counter.ts
├── node_modules/
├── scripts/
│   └── send-op.ts
├── test/
│   └── Counter.ts
├── .gitignore
├── hardhat.config.ts
├── package-lock.json
├── package.json
├── README.md
└── tsconfig.json
```
<img width="299" height="373" alt="image" src="https://github.com/user-attachments/assets/6bca84d3-890b-4a5c-bced-1063ab01574f" />

 
> 📌 图片说明：VS Code 中打开的项目文件夹视图，清晰展示所有顶层文件和目录。

#### 文件与目录详细说明（列表形式）

| 路径 | 类型 | 功能说明 |
|------|------|----------|
| `contracts/Counter.sol` | Solidity 文件 | 主合约代码，实现计数器功能 |
| `contracts/Counter.t.sol` | Solidity 测试文件 | 可用于编写 Solidity 单元测试（可选） |
| `ignition/modules/Counter.ts` | TypeScript 文件 | 定义 Ignition 部署逻辑，描述如何部署 `Counter` 合约 |
| `scripts/send-op.ts` | TypeScript 脚本 | 用于发送交易或部署合约的自动化脚本 |
| `test/Counter.ts` | TypeScript 测试文件 | 使用 Viem + Node Test Runner 编写集成测试 |
| `hardhat.config.ts` | 配置文件 | 全局配置项，包括编译器、网络、插件等 |
| `package.json` | JSON 文件 | 项目元数据和脚本命令定义 |
| `tsconfig.json` | 配置文件 | TypeScript 编译器设置，如 target、module 等 |
| `.gitignore` | 文本文件 | Git 忽略规则，避免提交 `node_modules`, `artifacts` 等 |
| `package-lock.json` | JSON 文件 | 锁定依赖版本，保证构建一致性 |

> 🔍 补充说明：
>
> - `artifacts/` 和 `cache/` 目录会在首次编译时自动生成。
> - 所有 `.sol` 文件都位于 `contracts/` 下，支持 `.t.sol` 扩展名作为测试用合约。
> - `ignition/` 是 Hardhat v3 引入的新特性，用于声明式部署。

---

### 5. 验证安装与编译

#### 验证安装成功

进入项目目录后，运行：

```bash
npx hardhat
```

预期输出：
<img width="1123" height="848" alt="image" src="https://github.com/user-attachments/assets/57eb0f08-caf9-4b8d-a247-04933bfb416f" />

```
Hardhat version 3.0.6

Usage: hardhat [GLOBAL OPTIONS] <TASK> [SUBTASK] [TASK OPTIONS] [--] [TASK ARGUMENTS]

AVAILABLE TASKS:

  build                         Build project
  clean                         Clear the cache and delete all artifacts
  compile                       Build project (alias for build)
  console                       Open a hardhat console
  flatten                       Flatten and print contracts and their dependencies
  node                          Start a JSON-RPC server on top of Hardhat Network
  run                           Run a user-defined script after compiling the 
  ...
```

> ✅ 若能看到命令列表，说明 Hardhat 已成功安装。

#### 编译合约

运行以下命令进行编译：

```bash
npx hardhat compile
```

控制台输出示例：
<img width="494" height="40" alt="image" src="https://github.com/user-attachments/assets/e0f53c56-b748-47da-b061-8aaaba419f79" />


```
Compiling your Solidity contracts...
Compiled 1 Solidity file with solc 0.8.28 (evm target: cancun)
```

> ✅ 编译成功意味着：
> - Solidity 语法正确
> - 编译器版本匹配
> - 依赖链完整

---

### 6. 后续开发建议

#### 启动本地网络节点

```bash
npx hardhat node
```

如图所示：
<img width="896" height="1149" alt="image" src="https://github.com/user-attachments/assets/0c66348b-b4c8-447e-b322-67d801bcb26d" />


```
Account #0:  0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266 (10000 ETH)
Private Key: 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80
...
```
这将在本地启动一个以太坊网络节点（`localhost:8545`），可用于测试和部署。

#### 部署合约

```bash
npx hardhat run scripts/send-op.ts --network localhost
```
如图所示：
<img width="550" height="152" alt="image" src="https://github.com/user-attachments/assets/358fd5f4-8da6-469b-b2d5-157696071d35" />

```
Compiling your Solidity contracts...

Nothing to compile

Sending transaction using the OP chain type
Sending 1 wei from 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266 to itself
Estimated L1 gas: 1600n
Sending L2 transaction
Transaction sent successfully
```
该命令会调用 `send-op.ts` 脚本，在本地网络上部署 `Counter` 合约。

#### 运行测试

```bash
npx hardhat test
```
如图所示：
<img width="787" height="395" alt="image" src="https://github.com/user-attachments/assets/b9e997d2-b34e-4727-a1ab-ef86a02fa784" />

```
Compiling your Solidity contracts...

Nothing to compile

Running Solidity tests

  contracts/Counter.t.sol:CounterTest
    ✔ test_InitialValue()
    ✔ test_IncByZero()
    ✔ testFuzz_Inc(uint8) (runs: 256)

  3 passing

Running node:test tests

  Counter
    ✔ Should emit the Increment event when calling the inc() function (644ms)
    ✔ The sum of the Increment events should match the current value (1027ms)

  2 passing (79542ms)
```
使用 Node Test Runner + Viem 执行 TypeScript 测试。

---

### 总结

本文档完整还原了从环境准备 → 初始化流程 → 依赖安装 → 项目结构 → 验证测试的全过程。

所有步骤均经过精确对齐，确保内容与实际操作一致。

--- 
