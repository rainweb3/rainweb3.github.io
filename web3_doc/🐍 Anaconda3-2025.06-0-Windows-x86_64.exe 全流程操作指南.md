# 🐍 Anaconda3-2025.06-0-Windows-x86_64.exe 全流程操作指南  
## **下载 → 验证 → 安装 → 配置 → 验证**（2025年最新版，确保安装成功）

> **版本**：Anaconda3-2025.06-0  
> **系统**：Windows 10/11 (64位)  
> **目标**：零错误、安全、可验证的完整安装  
> **更新时间**：2025年10月3日

---

## 特别注意事项：禁止路径含空格、中文、横杠（-）、下划线（_）以外的特殊字符（如 Technical-Software 中的 “-” 理论支持，但建议避免）；
否则会安装失败。
---

## 📌 目录

1. [✅ 第一步：下载安装包](#1-第一步下载安装包)  
2. [✅ 第二步：验证安装包完整性（SHA256 + 数字签名）](#2-第二步验证安装包完整性sha256--数字签名)  
3. [✅ 第三步：安装 Anaconda（图形化）](#3-第三步安装-anaconda图形化)  
4. [✅ 第四步：配置环境与常用设置](#4-第四步配置环境与常用设置)  
5. [✅ 第五步：最终验证安装是否成功](#5-第五步最终验证安装是否成功)  
6. [✅ 第六步：常见问题与解决方案](#6-第六步常见问题与解决方案)  
7. [✅ 总结：关键成功要素](#7-总结关键成功要素)

---

## 1. 第一步：下载安装包

### 🔗 官方下载地址：
👉 [https://repo.anaconda.com/archive/Anaconda3-2025.06-0-Windows-x86_64.exe](https://repo.anaconda.com/archive/Anaconda3-2025.06-0-Windows-x86_64.exe)

> 💡 **说明**：
> - `repo.anaconda.com` 是 Anaconda 官方归档服务器，**最权威的下载源**
> - `2025.06`：2025年6月发布，包含 Python 3.12.x、conda 24.5+、Jupyter、Spyder 等
> - `x86_64`：64位 Windows 系统专用

### 📥 下载步骤：
1. 打开浏览器，访问上述链接
2. 自动开始下载（文件大小约 500MB~700MB）
3. 建议将文件保存到：
   ```
   C:\Users\你的用户名\Downloads\Anaconda3-2025.06-0-Windows-x86_64.exe
   ```

> ⚠️ **不要使用第三方镜像或百度网盘等非官方渠道！**

---

## 2. 第二步：验证安装包完整性（SHA256 + 数字签名）

### ✅ 2.1 获取官方 SHA256 校验码

Anaconda 官方将校验码发布在文档站：

👉 [https://docs.anaconda.com/anaconda/install/hashes/](https://docs.anaconda.com/anaconda/install/hashes/)

#### 操作：
1. 打开链接
2. 找到 `Anaconda3-2025.06-0-Windows-x86_64.exe` 对应的 SHA256 值

> 🔎 示例（非真实值，请以官网为准）：
> ```
> # Anaconda3-2025.06-0-Windows-x86_64.exe
> 7e3c8a9b2f1d4e5c8a9b2f1d4e5c8a9b2f1d4e5c8a9b2f1d4e5c8a9b2f1d4e5c
> ```

---

### ✅ 2.2 计算本地文件的 SHA256 值

#### 方法一：使用 PowerShell（推荐）

```powershell
Get-FileHash -Algorithm SHA256 "C:\Users\你的用户名\Downloads\Anaconda3-2025.06-0-Windows-x86_64.exe"
```

📌 **替换路径为你的实际路径**

✅ 正常输出示例：

```text
Algorithm       Hash                                                                   Path
---------       ----                                                                   ----
SHA256          7E3C8A9B2F1D4E5C8A9B2F1D4E5C8A9B2F1D4E5C8A9B2F1D4E5C8A9B2F1D4E5C       C:\...\Anaconda3-2025.06-0-Windows-x86_64.exe
```

#### 方法二：使用 HashTab（图形化工具）

1. 下载 HashTab：[https://implbits.com/products/hashtab/](https://implbits.com/products/hashtab/)
2. 右键 `.exe` 文件 → **属性** → **Hashes** 选项卡
3. 查看 SHA256 值

---

### ✅ 2.3 对比 SHA256 值

| 项目 | 值 |
|------|----|
| **官方值** | `7e3c8a9b...`（来自 docs.anaconda.com） |
| **本地值** | `7E3C8A9B...`（来自 PowerShell） |

✅ **必须完全一致**（忽略大小写，但建议转为小写对比）

❌ 如果不一致 → **文件损坏或来源可疑 → 立即删除并重新下载**

---

### ✅ 2.4 验证数字签名（确保来源可信）

1. 右键 `.exe` 文件 → **属性**
2. 切换到 **“数字签名”** 选项卡
3. 查看签名信息：

✅ 正确签名应包含：
- **签名者名称**：`Anaconda, Inc.`
- **颁发者**：`DigiCert SHA2 Assured ID Code Signing CA`
- **状态**：此数字签名正常

> ✅ 表示该文件由 Anaconda 官方签署，未被篡改。

---

## 3. 第三步：安装 Anaconda（图形化）

### ✅ 3.1 安装前准备
- 关闭杀毒软件（如 360、腾讯电脑管家）
- 确保 C 盘或目标盘有 **10GB 可用空间**
- 以管理员身份运行安装程序

---

### ✅ 3.2 安装步骤（图解流程）

| 步骤 | 操作 |
|------|------|
| 1 | 右键 `Anaconda3-2025.06-0-Windows-x86_64.exe` → **以管理员身份运行** |
| 2 | 欢迎界面 → 点击 **Next** |
| 3 | 同意许可协议 → 勾选 **I agree** → **Next** |
| 4 | 选择安装类型 → 推荐 **Just Me** → **Next** |
| 5 | 选择安装路径 → **必须是纯英文路径**<br>✅ 推荐：`C:\anaconda3` 或 `D:\anaconda3`<br>❌ 避免：`C:\Users\张三\anaconda3` |
| 6 | 高级选项 → **勾选以下两项**：<br>✅ **Add Anaconda3 to my PATH environment variable**<br>✅ **Register Anaconda3 as my default Python 3.12** |
| 7 | 点击 **Install** → 等待 5-10 分钟 |
| 8 | 安装完成 → **取消勾选 “Launch Anaconda Navigator”** → 点击 **Finish** |

> 💡 **2025 年新建议**：勾选 `Add to PATH` 是安全的，官方已优化兼容性。

---

## 4. 第四步：配置环境与常用设置

### ✅ 4.1 设置国内镜像源（加速 pip/conda）

```bash
# 添加清华镜像源
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --set show_channel_urls yes
```

> 可选中科大源：`https://mirrors.ustc.edu.cn/anaconda/`

---

### ✅ 4.2 更新 conda（推荐）

```bash
conda update conda
conda update anaconda
```

---

### ✅ 4.3 创建虚拟环境（最佳实践）

```bash
# 创建名为 myenv 的环境，Python 3.12
conda create -n myenv python=3.12

# 激活环境
conda activate myenv

# 安装常用包
conda install numpy pandas matplotlib jupyter scikit-learn
```

---

## 5. 第五步：最终验证安装是否成功

### ✅ 5.1 打开 Anaconda Prompt
- 按 `Win + S` 搜索 **Anaconda Prompt**
- 右键以管理员身份运行

---

### ✅ 5.2 验证命令

```bash
# 1. 检查 conda 版本
conda --version
# 输出示例：conda 24.5.0

# 2. 检查 Python 版本
python --version
# 输出示例：Python 3.12.4

# 3. 检查环境列表
conda info --envs

# 4. 启动 Jupyter Notebook
jupyter notebook
# 成功则自动打开浏览器
```

---

### ✅ 5.3 启动 Anaconda Navigator（图形化）

```bash
anaconda-navigator
```

✅ 成功则打开主界面，可启动：
- JupyterLab
- Spyder
- VS Code
- Orange3

---

## 6. 第六步：常见问题与解决方案

| 问题 | 解决方案 |
|------|----------|
| ❌ `Failed to extract packages` | 1. 以管理员身份运行<br>2. 安装路径改为 `C:\anaconda3`<br>3. 关闭杀毒软件<br>4. 确保磁盘空间 ≥10GB |
| ❌ `conda is not recognized` | 1. 检查是否勾选 "Add to PATH"<br>2. 手动添加路径到系统环境变量：<br>   `C:\anaconda3`<br>   `C:\anaconda3\Scripts`<br>   `C:\anaconda3\Library\bin` |
| ❌ Jupyter 无法启动 | `jupyter notebook --port=8889` 换端口 |
| ❌ 安装后 Python 仍指向旧版本 | `where python` 查看路径，手动调整 PATH 顺序 |

---

## 7. 总结：关键成功要素

| 要素 | 说明 |
|------|------|
| ✅ **从官方下载** | `repo.anaconda.com` 是唯一可信源 |
| ✅ **验证 SHA256** | 对比 `docs.anaconda.com/install/hashes/` |
| ✅ **验证数字签名** | 签名者必须是 `Anaconda, Inc.` |
| ✅ **纯英文路径安装** | 推荐 `C:\anaconda3` |
| ✅ **以管理员身份运行** | 避免权限问题 |
| ✅ **勾选 Add to PATH** | 2025 年推荐做法 |
| ✅ **配置国内镜像** | 加速包下载 |

---

🎯 **按照本指南操作，可 100% 确保 Anaconda3-2025.06 安装成功！**

🐍 现在，你可以开始使用 Jupyter、Spyder、conda 等强大工具进行数据科学、AI 或 Python 开发了！

> 📞 如遇问题，请提供：
> - Windows 版本
> - 安装路径
> - 错误截图
> - `Get-FileHash` 输出

祝你开发顺利！🚀
