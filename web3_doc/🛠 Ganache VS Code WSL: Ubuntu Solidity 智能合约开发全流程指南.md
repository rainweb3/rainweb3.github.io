# 🛠 Ganache + VS Code WSL: Ubuntu + Solidity 智能合约开发全流程指南

本指南详细介绍了如何在 **Windows + WSL: Ubuntu** 环境下，使用 **Ganache** 本地测试链、**VS Code** 开发环境和 **Solidity** 编写、部署、测试以太坊智能合约的完整流程。

涵盖：
- Ganache 安装与配置
- VS Code 连接 WSL: Ubuntu
- Solidity 环境搭建
- 合约编写、编译、部署与测试

> ✅ 适用系统：Windows 10/11 + WSL2 + Ubuntu 20.04/22.04  
> ✅ 开发工具：VS Code + Remote - WSL 扩展  
> ✅ 测试链：Ganache（GUI 版）

---

## 一、前置条件

确保已完成以下环境准备：

| 工具 | 安装方式 | 验证命令 |
|------|----------|----------|
| **WSL2 + Ubuntu** | `wsl --install` | `wsl -l -v` |
| **VS Code** | [code.visualstudio.com](https://code.visualstudio.com/) | `code --version` |
| **Remote - WSL 扩展** | VS Code 扩展市场搜索安装 | 在 VS Code 中查看已安装扩展 |
| **Node.js (≥16)** | 在 WSL 中安装 | `node -v` |
| **npm / yarn** | 通常随 Node.js 安装 | `npm -v` |

---

## 二、安装与配置 Ganache（本地测试链）

### 1. 下载 Ganache

前往官方下载页面：  
👉 https://www.trufflesuite.com/ganache/

选择 **Windows 版本**（`.exe` 安装包）下载并安装。

> 💡 Ganache 是 Truffle Suite 的一部分，提供图形化界面的本地以太坊测试链。

---

### 2. 启动 Ganache 并创建工作区

1. 打开 **Ganache** 应用
2. 点击 **"New Workspace"** → 选择 **"Ethereum"**
3. 命名工作区（如 `Solidity-Dev`），点击 **"Save Workspace As..."** 保存配置
4. 在 **"Server"** 标签页中配置：
   - **Port Number**: `7545`（默认）
   - **Automine**: ✅ 启用（自动挖矿，无需手动触发）
5. 点击右上角 **"Save"** 保存配置
6. 点击 **"Quick Start"** 或 **"Start"** 启动本地链

> ✅ 启动成功后，你会看到：
> - 10 个预 funded 的测试账户（含私钥）
> - 当前区块高度、Gas 价格、RPC 服务器地址（`http://127.0.0.1:7545`）

---

## 三、在 WSL: Ubuntu 中搭建 Solidity 开发环境

### 1. 打开 VS Code 并连接 WSL

1. 打开 VS Code
2. 按 `Ctrl+Shift+P` → 输入并选择：`Remote-WSL: New Window`
3. 新窗口左下角显示 `WSL: Ubuntu`，表示已连接
4. 打开终端：`Ctrl + `` `

---

### 2. 安装 Node.js 与 npm（若未安装）

```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs
```

验证：
```bash
node -v  # 应 ≥ v16
npm -v
```

---

### 3. 初始化项目并安装依赖

```bash
mkdir solidity-project && cd solidity-project
npm init -y
npm install --save-dev hardhat
npx hardhat
```

选择：
```
✔ What do you want to do? · Create a JavaScript project
✔ Hardhat Project root: · /home/yourname/solidity-project
```

> ✅ 自动生成 `hardhat.config.js`、`contracts/`、`scripts/`、`test/` 目录

---

### 4. 安装 Solidity 编译器

```bash
npm install --save-dev @nomicfoundation/hardhat-toolbox
```

该工具箱包含：
- `hardhat-ethers`：合约交互
- `hardhat-chai-matchers`：测试断言
- `hardhat-gas-reporter`：Gas 报告
- `solidity-coverage`：覆盖率
- 自动集成 Solidity 编译器

---

## 四、配置 Hardhat 连接 Ganache

编辑 `hardhat.config.js`：

```js
require("@nomicfoundation/hardhat-toolbox");

/** @type import('hardhat/config').HardhatUserConfig */
module.exports = {
  solidity: "0.8.24",

  networks: {
    ganache: {
      url: "http://127.0.0.1:7545", // Ganache 默认 RPC 地址
      accounts: [
        "0x58a22d817d09f01148d97b7308894d89286a816697593945894137b69529b675", // 账户1私钥（从 Ganache 复制）
        "0x4d5db4107d237df6a3d58ee5f70ae63d73d7658d6a16a39bf18d1e4e82a30b5b", // 账户2私钥
        // 可添加更多
      ],
    },
  },
};
```

> 🔐 **安全提示**：Ganache 账户私钥仅用于本地测试，切勿用于主网！

---

## 五、编写一个简单的 Solidity 智能合约

### 1. 创建合约文件

在 `contracts/` 目录下创建 `Greeter.sol`：

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.24;

contract Greeter {
    string private greeting;

    constructor(string memory _greeting) {
        greeting = _greeting;
    }

    function greet() public view returns (string memory) {
        return greeting;
    }

    function setGreeting(string memory _greeting) public {
        greeting = _greeting;
    }
}
```

---

## 六、编译合约

在 WSL 终端执行：

```bash
npx hardhat compile
```

> ✅ 输出：
> ```
> Compiling 1 solidity file...
> Successfully compiled 1 file with Solidity 0.8.24
> ```

编译后的 ABI 和 Bytecode 会生成在 `artifacts/` 目录。

---

## 七、部署合约到 Ganache

### 1. 创建部署脚本

在 `scripts/` 目录下创建 `deploy.js`：

```js
const hre = require("hardhat");

async function main() {
  const Greeter = await hre.ethers.getContractFactory("Greeter");
  const greeter = await Greeter.deploy("Hello, Ganache!");

  await greeter.waitForDeployment();
  console.log("Greeter deployed to:", await greeter.getAddress());
}

main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```

---

### 2. 部署到 Ganache

```bash
npx hardhat run scripts/deploy.js --network ganache
```

> ✅ 成功输出：
> ```
> Greeter deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
> ```

刷新 Ganache 界面，你会看到：
- 新增一笔交易（Deploy）
- 账户余额减少（Gas 费用）
- 区块高度增加（因 Automine 启用）

---

## 八、测试合约（使用 Hardhat）

### 1. 创建测试文件

在 `test/` 目录下创建 `greeter.test.js`：

```js
const { expect } = require("chai");

describe("Greeter", function () {
  let greeter;

  beforeEach(async function () {
    const Greeter = await ethers.getContractFactory("Greeter");
    greeter = await Greeter.deploy("Hello, World!");
    await greeter.waitForDeployment();
  });

  it("Should return the new greeting once it's changed", async function () {
    expect(await greeter.greet()).to.equal("Hello, World!");

    await greeter.setGreeting("Hola, mundo!");
    expect(await greeter.greet()).to.equal("Hola, mundo!");
  });
});
```

---

### 2. 运行测试

```bash
npx hardhat test --network ganache
```

> ✅ 输出：
> ```
> Greeter
>   ✓ Should return the new greeting once it's changed (45ms)
> 
> 1 passing (52ms)
> ```

---

## 九、在 VS Code 中增强开发体验

### 1. 安装推荐扩展

在 **VS Code（WSL 环境）** 中安装：

- **Solidity** by Juan Blanco（语法高亮、编译）
- **Prettier** - Code formatter
- **Error Lens**（内联错误提示）
- **Hardhat for VS Code**（实验性）

---

### 2. 配置 Solidity 编译器版本

在项目根目录创建 `.vscode/settings.json`：

```json
{
  "solidity.compileUsingRemoteVersion": "0.8.24",
  "solidity.defaultCompiler": "remote"
}
```

> 💡 VS Code 将自动使用远程 Solidity 编译器进行语法检查。

---

## 十、常见问题与解决

### ❌ 问题 1：`Error: connect ECONNREFUSED 127.0.0.1:7545`

**原因**：Ganache 未启动或端口不一致  
**解决**：
- 确保 Ganache 正在运行
- 检查 `hardhat.config.js` 中的 `url` 是否为 `7545`
- 在 Windows 中检查防火墙是否阻止

---

### ❌ 问题 2：`Invalid JSON RPC response`

**原因**：Ganache 已启动但未响应  
**解决**：
- 重启 Ganache
- 尝试更换端口（如 `8545`）并同步修改配置

---

### ❌ 问题 3：账户余额不足

**原因**：Ganache 默认账户有 100 ETH，但部署复杂合约可能耗尽  
**解决**：
- 在 Ganache 界面中复制新账户使用
- 或在 `hardhat.config.js` 中添加更多私钥

---

### ❌ 问题 4：WSL 无法访问 `127.0.0.1:7545`

**原因**：网络隔离问题  
**解决**：
- Ganache 默认监听 `127.0.0.1`（仅主机访问）
- WSL 可通过 `host.docker.internal` 或 `127.0.0.1` 访问主机服务（Windows 10+ 支持）
- 若失败，尝试在 Ganache 设置中绑定 `0.0.0.0`（不推荐生产）

---

## 十一、总结：完整开发流程

| 步骤 | 工具 | 命令 / 操作 |
|------|------|-------------|
| 1. 启动测试链 | Ganache | 打开应用 → Start |
| 2. 连接 WSL | VS Code | `Remote-WSL: New Window` |
| 3. 初始化项目 | Hardhat | `npx hardhat` |
| 4. 编写合约 | Solidity | `contracts/Greeter.sol` |
| 5. 编译合约 | Hardhat | `npx hardhat compile` |
| 6. 部署合约 | Hardhat | `npx hardhat run scripts/deploy.js --network ganache` |
| 7. 测试合约 | Mocha + Chai | `npx hardhat test --network ganache` |
| 8. 查看结果 | Ganache GUI | 观察交易、区块、余额变化 |

---

## 🎯 后续建议

- 使用 **Hardhat Console** 调试：`console.log()`（需 `hardhat/console.sol`）
- 集成 **TypeScript**：创建 `hardhat.config.ts`
- 使用 **Etherscan 验证**（测试网）
- 探索 **Foundry**（Rust 风格开发框架）

---

## 🔗 参考资料

- [Ganache 官网](https://www.trufflesuite.com/ganache)
- [Hardhat 官方文档](https://hardhat.org/docs)
- [Solidity 官方文档](https://docs.soliditylang.org/)
- [Truffle Suite GitHub](https://github.com/trufflesuite)

> 🚀 现在你已具备完整的本地 Solidity 开发环境，可开始构建去中心化应用（DApp）！
