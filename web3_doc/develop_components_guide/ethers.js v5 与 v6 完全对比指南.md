# 📚 ethers.js v5 与 v6 完全对比指南

`ethers.js` 是以太坊生态中最流行的 JavaScript/TypeScript 库之一，用于与以太坊节点交互、签名交易、调用智能合约等。  
**v6 版本**（发布于 2023 年）是一次重大重构，带来了性能提升、API 精简、类型安全增强，但也引入了大量**不兼容变更**。

本文全面梳理 **ethers v5 与 v6 的核心差异**，涵盖函数变更、调用方式、常见错误、兼容性分析，并补充你可能尚未意识到的重要问题，助你平滑迁移或新项目选型。

---

## 🔍 一、总体概览：v5 vs v6 核心变化

| 维度 | ethers.js v5 | ethers.js v6 |
|------|--------------|--------------|
| 发布时间 | 2020 年 | 2023 年 Q3 |
| 设计目标 | 功能完整、易用 | 更小、更快、更安全、更现代 |
| 包大小 | 较大（~120KB gzipped） | 更小（~70KB gzipped） |
| 类型系统 | 基于 JS + TS 类型定义 | 全面重构的 TS 类型系统（更强） |
| API 风格 | 面向对象，部分冗余 | 函数式 + 精简 API，更一致 |
| 向后兼容 | ❌ 不兼容 v4 | ❌ 不兼容 v5 |
| 浏览器支持 | 支持 IE11（通过 polyfill） | 放弃 IE11，仅支持现代浏览器 |
| 默认导出 | `ethers` 对象 | 精细化命名导出 |

> ✅ **建议**：
> - 新项目：**直接使用 v6**
> - 老项目：评估成本后逐步迁移

---

## 🧱 二、核心模块与导入方式变更

### v5 写法
```js
import { ethers } from 'ethers';

const provider = new ethers.providers.JsonRpcProvider('...');
const wallet = new ethers.Wallet(privateKey, provider);
```

### v6 写法
```js
import { JsonRpcProvider, Wallet } from 'ethers';

const provider = new JsonRpcProvider('...');
const wallet = new Wallet(privateKey, provider);
```

### 主要变化
- **移除顶层 `ethers` 命名空间**：不再有 `ethers.providers`, `ethers.Wallet` 等。
- **改为精细化命名导出**：所有类、函数、常量直接从 `ethers` 导出。
- **模块更清晰**：每个功能独立导出，Tree-shaking 更高效。

---

## 🔄 三、Provider（提供者）变更

### 1. 类名变更
| v5 | v6 |
|----|----|
| `ethers.providers.JsonRpcProvider` | `JsonRpcProvider` |
| `ethers.providers.Web3Provider` (MetaMask) | `BrowserProvider` |
| `ethers.providers.InfuraProvider` | `InfuraProvider` |
| `ethers.providers.AlchemyProvider` | `AlchemyProvider` |

### v6 示例
```js
import { BrowserProvider } from 'ethers';

// MetaMask 连接
const provider = new BrowserProvider(window.ethereum);
const signer = await provider.getSigner();
```

> ⚠️ 注意：`Web3Provider` → `BrowserProvider`，语义更准确。

---

## 🔐 四、Wallet（钱包）与 Signer（签名者）

### 1. Wallet 构造函数
| v5 | v6 |
|----|----|
| `new ethers.Wallet(privateKey, provider)` | `new Wallet(privateKey, provider)` |

✅ 用法基本一致，但导入方式不同。

### 2. Signer 抽象层级变化
- v5 中 `Signer` 是抽象类，`Wallet` 和 `VoidSigner` 继承它。
- v6 中 `Signer` 仍是基类，但接口更简洁。

### 3. 获取 Signer 方式（MetaMask）
#### v5
```js
const provider = new ethers.providers.Web3Provider(window.ethereum);
const signer = provider.getSigner(); // 返回 Signer 实例
```

#### v6
```js
const provider = new BrowserProvider(window.ethereum);
const signer = await provider.getSigner(); // ✅ 注意：现在是 async
```

> ⚠️ **重大变更**：`getSigner()` 在 v6 中变为 `async`，必须 `await`！

---

## 📦 五、Contract（合约）调用变更

### 1. Contract 构造函数
| v5 | v6 |
|----|----|
| `new ethers.Contract(address, abi, signerOrProvider)` | `new Contract(address, abi, signerOrProvider)` |

✅ 用法一致，但导入方式不同。

### 2. 读取状态（Call）
#### v5
```js
const balance = await contract.balanceOf(address);
```

#### v6
```js
const balance = await contract.balanceOf(address);
```
✅ 读取方法调用方式**保持不变**。

### 3. 写入状态（Send / Transaction）
#### v5
```js
const tx = await contract.transfer(to, value);
await tx.wait(); // 等待确认
```

#### v6
```js
const tx = await contract.transfer(to, value);
await tx.wait(); // ✅ 仍然支持
```
✅ 交易发送方式基本不变。

---

## 💸 六、Gas 与交易选项（Transaction Request）变更

### 1. `gasPrice` vs `maxFeePerGas` / `maxPriorityFeePerGas`

#### v5（传统交易）
```js
await contract.transfer(to, value, {
  gasPrice: ethers.utils.parseUnits('50', 'gwei')
});
```

#### v6（EIP-1559 交易）
```js
await contract.transfer(to, value, {
  maxFeePerGas: parseUnits('50', 'gwei'),
  maxPriorityFeePerGas: parseUnits('2', 'gwei')
});
```

> ✅ v6 **默认使用 EIP-1559 交易格式**，推荐使用 `maxFeePerGas` 和 `maxPriorityFeePerGas`。

> ⚠️ v6 中 `gasPrice` 仍可用，但仅用于 Legacy Transactions（非推荐）。

---

## 🧮 七、单位转换工具变更

### v5
```js
import { ethers } from 'ethers';

ethers.utils.parseEther('1.0');        // → BigNumber
ethers.utils.formatEther('1000000000000000000');
ethers.utils.parseUnits('1', 'gwei');
```

### v6
```js
import { parseEther, formatEther, parseUnits, formatUnits } from 'ethers';

parseEther('1.0');        // → bigint（默认）或 BigNumber（可选）
formatEther('1000000000000000000');
parseUnits('1', 'gwei');
```

### 主要变化
| v5 | v6 |
|----|----|
| `ethers.utils.parseEther` | `parseEther`（顶层导出） |
| 返回 `BigNumber` | 默认返回 `bigint`（原生 JS 类型） |
| `BigNumber` 是对象 | `bigint` 是 JS 原生类型 |

> ✅ v6 推荐使用 `bigint`，性能更好，但需注意：
> - `bigint` 不能与 `number` 混合运算
> - 若需 `BigNumber`，可启用 `allowBigInt: false`（不推荐）

---

## 🧩 八、ABI 编码与解码工具

### v5
```js
ethers.utils.defaultAbiCoder.encode(types, values);
ethers.utils.defaultAbiCoder.decode(types, data);
```

### v6
```js
import { AbiCoder } from 'ethers';

const coder = AbiCoder.defaultAbiCoder();
coder.encode(types, values);
coder.decode(types, data);
```

> ✅ 更明确的类封装，类型更清晰。

---

## 🔗 九、地址与校验

### v5
```js
ethers.utils.getAddress(address); // 校验并返回 checksum 地址
ethers.utils.isAddress(address);  // 判断是否为有效地址
```

### v6
```js
import { getAddress, isAddress } from 'ethers';

getAddress(address); // ✅ 同上
isAddress(address);  // ✅ 同上
```

✅ 功能一致，导入方式变更。

---

## 🧯 十、常见错误与迁移陷阱（v5 → v6）

### 错误 1：未 `await provider.getSigner()`
```js
// ❌ v6 错误
const signer = provider.getSigner(); // 返回 Promise，未 await

// ✅ 正确
const signer = await provider.getSigner();
```

---

### 错误 2：误用 `ethers.utils`
```js
// ❌ v6 不存在
ethers.utils.parseEther('1.0');

// ✅ 正确
parseEther('1.0');
```

---

### 错误 3：`bigint` 与 `number` 混用
```js
const amount = parseEther('1.0'); // → bigint
const fee = 10000; // → number

// ❌ 错误：Cannot mix BigInt and other types
const total = amount + fee;

// ✅ 正确：统一类型
const total = amount + BigInt(fee);
```

---

### 错误 4：未处理 EIP-1559 默认行为
```js
// ❌ v6 可能失败：未设置 maxFeePerGas
await contract.transfer(to, value);

// ✅ 正确：显式设置
await contract.transfer(to, value, {
  maxFeePerGas: parseUnits('50', 'gwei'),
  maxPriorityFeePerGas: parseUnits('2', 'gwei')
});
```

---

### 错误 5：ABI 编码未导入 `AbiCoder`
```js
// ❌ v6 不存在
ethers.utils.defaultAbiCoder.encode(...);

// ✅ 正确
import { AbiCoder } from 'ethers';
const coder = AbiCoder.defaultAbiCoder();
coder.encode(...);
```

---

## 🔄 十一、兼容性与共存策略

### 是否兼容？
- **不兼容**：v5 和 v6 **不能直接共存于同一项目**（类型冲突、API 冲突）。
- **不能混用**：如 `v5.Wallet` 与 `v6.Provider` 可能出错。

### 共存方案（高级）
若必须共存（如微前端、插件系统），可通过以下方式隔离：
- 使用 **Webpack Module Federation** 或 **Vite 动态导入**
- 将不同版本封装在独立 bundle 中
- 通过消息通信传递数据（避免对象共享）

> ⚠️ 不推荐，复杂且易错。

---

## 🧩 十二、你可能还没问但需要知道的问题

### 1. v6 中 `BigNumber` 还支持吗？
- ✅ 支持，但**默认禁用**。
- 可通过 `ethers.BigNumber.from()` 创建，但官方推荐使用 `bigint`。

### 2. `ethers.constants` 去哪了？
- v5：`ethers.constants.AddressZero`
- v6：`import { ZeroAddress } from 'ethers'`
- 常量已重命名并导出：
  - `ZeroAddress` → `'0x000...000'`
  - `EtherSymbol` → `'Ξ'`
  - `MaxUint256` → `parseEther('115792089237316195423570985008687907853269984665640564039457...')`

### 3. `ethers.errors` 变更？
- v5 错误是对象
- v6 错误是**类继承结构**，更易捕获：
  ```ts
  try {
    // ...
  } catch (error) {
    if (error instanceof ContractTransactionRevertedError) {
      // 处理合约回滚
    }
  }
  ```

### 4. 性能提升体现在哪？
- 更小的包体积
- 更少的依赖
- `bigint` 替代 `BigNumber`，减少对象创建开销
- 更高效的 ABI 编码器

### 5. TypeScript 支持更好了吗？
✅ **显著提升**：
- 所有类型重新设计
- 更精确的函数签名
- 更好的泛型支持
- 减少 `any` 使用

---

## ✅ 十三、迁移 checklist（v5 → v6）

| 任务 | 状态 |
|------|------|
| 1. 更新 `package.json`：`"ethers": "^6.0.0"` | ☐ |
| 2. 修改所有导入语句（移除 `ethers.` 前缀） | ☐ |
| 3. 将 `provider.getSigner()` 改为 `await provider.getSigner()` | ☐ |
| 4. 替换 `ethers.utils.xxx` 为顶层函数（如 `parseEther`） | ☐ |
| 5. 处理 `bigint` 与 `number` 类型混合问题 | ☐ |
| 6. 更新交易选项为 EIP-1559 格式（`maxFeePerGas`） | ☐ |
| 7. 替换常量（如 `AddressZero` → `ZeroAddress`） | ☐ |
| 8. 修复 ABI 编码/解码导入方式 | ☐ |
| 9. 更新错误处理逻辑（使用 `instanceof`） | ☐ |
| 10. 测试所有交易与查询功能 | ☐ |

---

## 🏁 十四、总结：v5 vs v6 决策建议

| 场景 | 推荐版本 |
|------|----------|
| 新项目开发 | ✅ **ethers v6**（更现代、更高效） |
| 老项目维护 | ⚠️ 评估迁移成本，优先修复安全问题 |
| 需要 IE11 支持 | ✅ **ethers v5**（v6 不支持） |
| 追求极致类型安全 | ✅ **ethers v6** |
| 依赖大量 v5 第三方库 | ✅ 暂留 v5，逐步解耦 |

> 📢 **最终建议**：  
> **拥抱 v6**。它是 ethers.js 的未来，API 更简洁、性能更强、类型更安全。  
> 虽然迁移有成本，但长期收益远大于短期阵痛。

---

## 🔗 参考资料
- [ethers.js v6 官方文档](https://docs.ethers.org/v6/)
- [v5 → v6 迁移指南](https://docs.ethers.org/v6/migrating/)
- [GitHub 仓库](https://github.com/ethers-io/ethers.js)
