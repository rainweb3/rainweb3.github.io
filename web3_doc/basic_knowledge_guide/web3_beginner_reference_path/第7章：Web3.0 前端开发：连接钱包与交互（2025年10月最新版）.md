### **第7章：Web3.0 前端开发：连接钱包与交互（2025年10月最新版）**

⚠️ 免责声明：本文档旨在提供教育性、参考性的技术指导，基于当前（2025年）社区广泛认可的最佳实践。它不构成任何形式的投资、法律或专业建议。智能合约开发风险极高，任何部署前都应进行严格的自我审查、自动化扫描和第三方审计。

本章系统讲解**现代Web3.0前端开发的核心技术栈**，涵盖钱包连接、交易发送、事件监听等关键操作。所有内容基于当前（2025年10月）行业最佳实践，采用**生产级、无过时信息、可直接部署**的技术方案，确保开发者掌握真实可用的技能。

---

#### **1. Web3.js vs Ethers.js：推荐 Ethers.js**

| 对比项 | **Ethers.js（推荐）** | **Web3.js** |
|--------|------------------------|-------------|
| **体积与性能** | 更轻量（~80KB），Tree-shaking 友好 | 较大（~400KB），包体积大 |
| **API 设计** | 现代、简洁、TypeScript 优先 | 旧式 API，回调风格遗留 |
| **维护状态** | 活跃维护，v6 是当前稳定版（2023–2025） | 维护减缓，社区转向 Ethers |
| **安全性** | 内置地址校验、ABI 解析更安全 | 需额外处理边界情况 |
| **多链支持** | 原生支持以太坊及所有 EVM 链（Polygon、Arbitrum 等） | 支持但配置复杂 |

> ✅ **结论（2025年）**：**Ethers.js v6** 是当前和未来 Web3 前端开发的**事实标准**，强烈推荐用于新项目。

---

#### **2. 钱包连接：使用 `window.ethereum` 检测 MetaMask**

现代 DApp 通过浏览器注入的 `window.ethereum` 对象与钱包通信（遵循 **EIP-1193** 标准）。

##### **（1）检测钱包存在**
```ts
function detectWallet() {
  if (typeof window.ethereum !== 'undefined') {
    console.log('MetaMask is installed!');
    return true;
  } else {
    alert('Please install MetaMask to use this DApp.');
    return false;
  }
}
```

##### **（2）请求用户授权（`eth_requestAccounts`）**
```ts
async function connectWallet() {
  if (!detectWallet()) return;

  try {
    // 请求用户授权连接
    const accounts = await window.ethereum.request({
      method: 'eth_requestAccounts',
    });

    const account = accounts[0];
    console.log('Connected account:', account);
    return account;
  } catch (error) {
    console.error('User rejected request or error:', error);
    throw error;
  }
}
```

> 🔐 **安全提示**：
> - `eth_requestAccounts` 是标准方法，不会自动连接。
> - 用户必须主动点击“连接”按钮才能触发。

---

#### **3. 获取用户地址、余额、网络**

##### **（1）获取用户地址**
```ts
const account = await provider.getSigner().getAddress();
```

##### **（2）获取余额**
```ts
async function getBalance(account: string, provider: ethers.Provider) {
  const balanceWei = await provider.getBalance(account);
  const balanceEth = ethers.formatEther(balanceWei); // 转换为 ETH
  console.log(`Balance: ${balanceEth} ETH`);
  return balanceEth;
}
```

##### **（3）获取当前网络**
```ts
async function getNetwork(provider: ethers.Provider) {
  const network = await provider.getNetwork();
  console.log(`Chain ID: ${network.chainId}, Name: ${network.name}`);
  return network;
}
```

> 🌐 **常见 Chain ID（2025年）**：
> - Ethereum Mainnet: `1`
> - Polygon PoS: `137`
> - Arbitrum One: `42161`
> - Optimism: `10`
> - Base: `8453`

---

#### **4. 发送交易：调用合约方法**

以调用 NFT 合约的 `mint` 方法为例。

##### **（1）初始化合约实例**
```ts
const contractAddress = "0xYourContractAddress";
const contractABI = [ /* 从 artifacts 复制 ABI */ ];

// 使用 signer（有权限发送交易）
const signer = await provider.getSigner();
const contract = new ethers.Contract(contractAddress, contractABI, signer);
```

##### **（2）发送 Mint 交易**
```ts
async function mintNFT(amount: number) {
  if (!contract) throw new Error("Contract not initialized");

  try {
    const tx = await contract.publicMint(amount, {
      value: ethers.parseEther("0.01"), // 发送 0.01 ETH
      gasLimit: 300000, // 可选：设置 Gas 上限
    });

    console.log("Transaction sent:", tx.hash);

    // 等待交易确认
    const receipt = await tx.wait();
    console.log("Transaction confirmed in block:", receipt?.blockNumber);
    return receipt;
  } catch (error: any) {
    console.error("Transaction failed:", error.message);
    throw error;
  }
}
```

> 💡 **关键参数说明**：
> - `value`: 发送 ETH（单位为 Wei，使用 `ethers.parseEther()` 转换）。
> - `gasLimit`: 手动设置 Gas 上限，避免意外消耗过多。
> - `tx.wait()`: 返回交易回执（Receipt），包含状态、日志、区块号等。

---

#### **5. 事件监听：监听合约事件**

监听 `Transfer` 事件，实时更新前端 UI。

##### **（1）监听单个事件**
```ts
contract.on("Minted", (minter, tokenId) => {
  console.log(`New NFT minted! Owner: ${minter}, Token ID: ${tokenId.toString()}`);
  // 更新前端状态，如刷新 NFT 列表
});
```

##### **（2）监听所有事件（调试用）**
```ts
contract.on("Transfer", (from, to, tokenId) => {
  console.log(`NFT #${tokenId} transferred from ${from} to ${to}`);
});
```

##### **（3）移除监听器**
```ts
// 避免内存泄漏
contract.off("Minted", listenerFunction);
```

> ⚠️ **生产建议**：
> - 在 React 中使用 `useEffect` 清理监听器。
> - 对于高频事件，考虑使用 The Graph 或后端索引服务。

---

#### **6. 前端框架集成：React + Ethers.js（推荐模式）**

以 **React + TypeScript + Vite** 为例，展示最佳实践结构。

##### **（1）安装依赖**
```bash
npm create vite@latest my-dapp -- --template react-ts
cd my-dapp
npm install ethers@6
```

##### **（2）创建 Web3 上下文（`Web3Context.tsx`）**
```tsx
import { createContext, useContext, useState, useEffect, ReactNode } from 'react';
import { ethers } from 'ethers';

interface Web3ContextType {
  account: string | null;
  balance: string;
  chainId: number | null;
  provider: ethers.Provider | null;
  connectWallet: () => Promise<void>;
}

const Web3Context = createContext<Web3ContextType | undefined>(undefined);

export function Web3Provider({ children }: { children: ReactNode }) {
  const [account, setAccount] = useState<string | null>(null);
  const [balance, setBalance] = useState("0");
  const [chainId, setChainId] = useState<number | null>(null);
  const [provider, setProvider] = useState<ethers.Provider | null>(null);

  useEffect(() => {
    if (typeof window.ethereum !== 'undefined') {
      const prov = new ethers.BrowserProvider(window.ethereum);
      setProvider(prov);
    }
  }, []);

  const connectWallet = async () => {
    if (!provider) return;

    try {
      const accounts = await provider.send("eth_requestAccounts", []);
      const acc = accounts[0];
      setAccount(acc);

      const bal = await provider.getBalance(acc);
      setBalance(ethers.formatEther(bal));

      const network = await provider.getNetwork();
      setChainId(network.chainId);
    } catch (err) {
      console.error("Failed to connect wallet", err);
    }
  };

  return (
    <Web3Context.Provider value={{ account, balance, chainId, provider, connectWallet }}>
      {children}
    </Web3Context.Provider>
  );
}

export const useWeb3 = () => {
  const context = useContext(Web3Context);
  if (!context) throw new Error("useWeb3 must be used within Web3Provider");
  return context;
};
```

##### **（3）在组件中使用**
```tsx
import { useWeb3 } from './Web3Context';

function App() {
  const { account, balance, connectWallet } = useWeb3();

  return (
    <div>
      <h1>My DApp</h1>
      {!account ? (
        <button onClick={connectWallet}>Connect Wallet</button>
      ) : (
        <div>
          <p>Account: {account}</p>
          <p>Balance: {balance} ETH</p>
        </div>
      )}
    </div>
  );
}
```

---

### **本章小结**

截至2025年10月，Web3.0前端开发已形成清晰的技术路径：
- **首选 Ethers.js v6**，因其轻量、安全、现代化。
- **钱包连接**基于 `window.ethereum` 和 `eth_requestAccounts`。
- **交易发送**需正确处理 `signer`、`gas`、`value` 和交易回执。
- **事件监听**实现响应式UI更新。
- **React + Context API** 是管理 Web3 状态的推荐模式。
