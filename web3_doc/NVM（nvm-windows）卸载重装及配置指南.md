# NVM（nvm-windows）卸载重装及配置指南

**更新日期：2025年10月3日**

## 一、完全卸载现有 NVM 环境

### 1. 卸载 nvm-windows 程序
- 打开「控制面板」→「程序和功能」
- 找到「nvm-windows」相关程序，右键选择「卸载」
- 若无法找到卸载程序，手动删除安装目录：
  ```powershell
  # 以管理员身份打开 PowerShell
  Remove-Item -Path "C:\DevTools\nvm" -Recurse -Force
  ```

### 2. 清理残留文件
```powershell
# 删除符号链接目录
Remove-Item -Path "C:\DevTools\nodejs-link" -Recurse -Force

# 清理 npm 缓存和全局包目录（可选）
Remove-Item -Path "C:\DevTools\npm-cache" -Recurse -Force
Remove-Item -Path "C:\DevTools\npm-global" -Recurse -Force
```

### 3. 删除环境变量
- 打开「环境变量」设置（`Win + R` → `sysdm.cpl` → 高级 → 环境变量）
- 在「系统变量」中：
  - 删除 `NVM_HOME` 变量
  - 编辑 `Path` 变量，删除所有与 NVM/Node 相关的条目

## 二、重新安装 NVM

### 1. 下载安装包
- 访问 [nvm-windows  Releases](https://github.com/coreybutler/nvm-windows/releases)
- 下载最新版 `nvm-setup.exe`

### 2. 安装步骤
- 运行安装程序，设置以下路径：
  - **NVM 安装路径**：`C:\DevTools\nvm`
  - **Node.js 符号链接路径**：`C:\DevTools\nodejs-link`
- 勾选「Add to PATH」选项，完成安装

## 三、配置环境变量

1. 配置 `NVM_HOME` 变量：
   - 变量名：`NVM_HOME`
   - 变量值：`C:\DevTools\nvm`

2. 编辑 `Path` 变量，添加以下条目：
   - `%NVM_HOME%`
   - `C:\DevTools\nodejs-link`
   - `C:\DevTools\npm-global`
   - `C:\DevTools\npm-cache`

## 四、创建 settings.txt 配置文件

1. 进入 NVM 安装目录：`C:\DevTools\nvm`
2. 创建 `settings.txt` 文件，内容如下：
   ```txt
   root: C:\DevTools\nvm
   path: C:\DevTools\nodejs-link
   arch: 64
   proxy: none
   node_mirror: https://npmmirror.com/mirrors/node/
   npm_mirror: https://npmmirror.com/mirrors/npm/
   ```

## 五、验证安装并安装 Node.js

1. 打开管理员 PowerShell，验证 NVM 安装：
   ```powershell
   nvm version  # 应显示版本号
   ```

2. 安装 Node.js 版本：
   ```powershell
   nvm install 20.17.0  # 安装LTS版本
   nvm install 22.8.0   # 安装最新版本
   ```

3. 切换 Node.js 版本：
   ```powershell
   # 清理可能的链接残留
   Remove-Item -Path "C:\DevTools\nodejs-link" -Recurse -Force
   
   # 切换到20.17.0版本
   nvm use 20.17.0
   ```

4. 验证 Node.js 安装：
   ```powershell
   node -v  # 应显示 v20.17.0
   npm -v   # 应显示对应版本号
   ```

## 六、配置 npm 全局路径

```powershell
# 创建目录
mkdir "C:\DevTools\npm-global"
mkdir "C:\DevTools\npm-cache"

# 配置 npm
npm config set prefix "C:\DevTools\npm-global"
npm config set cache "C:\DevTools\npm-cache"
```

## 七、常见问题处理

1. 若出现 `NVM_SYMLINK` 错误：
   ```powershell
   # 手动创建符号链接
   Remove-Item -Path "C:\DevTools\nodejs-link" -Recurse -Force
   mklink /D "C:\DevTools\nodejs-link" "C:\DevTools\nvm\v20.17.0"
   
   # 写入当前版本记录
   echo v20.17.0 > "C:\DevTools\nvm\current"
   ```

2. 确保所有操作都在管理员权限终端中执行

3. 若命令仍无法识别，关闭所有终端后重新打开

通过以上步骤，可建立一个干净的 NVM 环境，实现 Node.js 版本的灵活管理。

----

# NVM 多镜像源自动切换方案（手动/脚本两种实现）

**更新日期：2025年10月3日**

NVM 原生不支持「镜像源自动优先级切换」，但可通过 **手动指定镜像** 或 **脚本自动重试** 两种方案实现类似需求。以下是完整实现指南：

## 一、核心需求说明
目标：安装 Node.js 时优先尝试国外官方源，失败后自动切换国内镜像（阿里 → 腾讯 → 华为等），无需手动修改配置文件。

## 二、方案一：手动指定镜像（简单易用）
保留 `settings.txt` 中的默认国外源，安装时通过命令行参数手动切换国内镜像，适合偶尔安装的场景。

### 1. 配置 `settings.txt`（默认国外源）
```txt
root: C:\DevTools\nvm          # 你的NVM安装目录
path: C:\DevTools\nodejs-link  # 符号链接路径
arch: 64                       # 系统架构（32位填32）
proxy: none
# 默认国外官方源（优先尝试）
node_mirror: https://nodejs.org/dist/
npm_mirror: https://registry.npmjs.org/
```

### 2. 按优先级手动切换镜像
安装时先尝试默认源，失败后依次指定国内镜像：
```bash
# 1. 优先尝试国外官方源
nvm install 20.10.0  # 安装具体版本
# 或安装稳定版：nvm install stable

# 2. 国外源失败 → 阿里镜像（国内首选）
nvm install 20.10.0 --mirror https://npmmirror.com/mirrors/node/

# 3. 阿里镜像失败 → 腾讯云镜像
nvm install 20.10.0 --mirror https://mirrors.tencent.com/npm/mirrors/node/

# 4. 腾讯云失败 → 华为云镜像
nvm install 20.10.0 --mirror https://mirrors.huaweicloud.com/nodejs/

# 5. 华为云失败 → 清华大学镜像
nvm install 20.10.0 --mirror https://mirrors.tuna.tsinghua.edu.cn/nodejs-release/
```

## 三、方案二：脚本自动重试（高效智能）
通过 PowerShell 脚本实现「失败自动切换镜像」，支持版本号、`stable`/`lts` 关键字，适合频繁安装的场景。

### 1. 创建自动安装脚本 `install-node.ps1`
```powershell
<#
.SYNOPSIS
Node.js多镜像源自动安装脚本，支持版本号和stable/lts关键字

.DESCRIPTION
按优先级尝试镜像源（国外官方 → 国内多源），失败自动切换，成功立即退出
支持参数：具体版本号（如20.10.0）、stable（最新稳定版）、lts（最新LTS版）

.EXAMPLE
.\install-node.ps1 20.10.0   # 安装指定版本
.\install-node.ps1 stable    # 安装最新稳定版
.\install-node.ps1 lts       # 安装最新LTS版
#>

# 1. 检查是否传入参数
if ($args.Count -eq 0) {
    Write-Error "请指定安装目标！"
    Write-Error "示例：.\\install-node.ps1 20.10.0 或 .\\install-node.ps1 stable"
    exit 1
}

# 2. 定义安装目标（版本号或关键字）
$target = $args[0]
Write-Host "`n===== 开始安装 Node.js：$target =====" -ForegroundColor Cyan

# 3. 镜像源列表（按优先级排序：国外 → 国内多源）
$mirrors = @(
    # 国外源（优先尝试）
    "https://nodejs.org/dist/",
    # 国内源（按稳定性排序）
    "https://npmmirror.com/mirrors/node/",       # 阿里云（国内首选）
    "https://mirrors.tencent.com/npm/mirrors/node/", # 腾讯云
    "https://mirrors.huaweicloud.com/nodejs/",   # 华为云
    "https://mirrors.tuna.tsinghua.edu.cn/nodejs-release/", # 清华大学
    "https://mirrors.ustc.edu.cn/nodejs/",       # 中国科学技术大学
    "https://mirrors.163.com/nodejs/",           # 网易
    "https://mirrors.sohu.com/nodejs/",          # 搜狐
    "https://mirrors.bfsu.edu.cn/nodejs-release/", # 北京外国语大学
    "https://mirror.sjtu.edu.cn/nodejs/"         # 上海交通大学
)

# 4. 循环尝试镜像源安装
foreach ($mirror in $mirrors) {
    Write-Host "`n尝试镜像源：$mirror" -ForegroundColor Yellow
    
    # 执行安装命令（兼容版本号和关键字）
    nvm install $target --mirror $mirror
    
    # 检查安装是否成功（0 = 成功）
    if ($LASTEXITCODE -eq 0) {
        Write-Host "`n✅ 安装成功！" -ForegroundColor Green
        Write-Host "✅ 目标：$target"
        Write-Host "✅ 镜像源：$mirror`n"
        exit 0
    }
    
    # 安装失败，继续下一个源
    Write-Host "❌ 该镜像源安装失败，尝试下一个..." -ForegroundColor Red
}

# 5. 所有源尝试失败
Write-Error "`n❌ 错误：所有镜像源均无法安装 Node.js $target"
Write-Error "可能原因：版本无效、网络故障或镜像源暂时不可用`n"
exit 1
```

### 2. 脚本使用指南
#### （1）保存脚本
将上述代码复制到记事本，保存为 `install-node.ps1`（如保存到 `C:\Scripts\` 目录）。

#### （2）执行脚本（必须管理员权限）
1. 按下 `Win + R` → 输入 `powershell` → 按 `Ctrl + Shift + Enter` 以管理员身份运行。
2. 导航到脚本目录：`cd C:\Scripts\`。
3. 执行安装命令：
   ```powershell
   # 示例1：安装具体版本（20.10.0）
   .\install-node.ps1 20.10.0
   
   # 示例2：安装最新稳定版
   .\install-node.ps1 stable
   
   # 示例3：安装最新LTS版
   .\install-node.ps1 lts
   ```

#### （3）执行效果示例
```
===== 开始安装 Node.js：20.10.0 =====

尝试镜像源：https://nodejs.org/dist/
❌ 该镜像源安装失败，尝试下一个...

尝试镜像源：https://npmmirror.com/mirrors/node/
Downloading node.js version 20.10.0 (64-bit)...
Complete
Creating C:\DevTools\nvm\temp

Downloading npm version 10.2.3... Complete
Installing npm v10.2.3...

Installation complete. If you want to use this version, type

nvm use 20.10.0

✅ 安装成功！
✅ 目标：20.10.0
✅ 镜像源：https://npmmirror.com/mirrors/node/
```

### 3. 脚本核心特点
- **兼容性强**：支持 `nvm install` 的所有参数（版本号、`stable`、`lts`）。
- **自动重试**：按优先级依次尝试10个镜像源，无需手动干预。
- **日志清晰**：彩色输出安装状态，成功/失败一目了然。
- **灵活扩展**：可在 `$mirrors` 数组中添加/删除镜像源。

## 四、注意事项
1. **管理员权限**：无论手动还是脚本安装，都需以管理员身份运行终端（否则可能无法创建符号链接）。
2. **版本有效性**：安装前建议通过 [Node.js 官网](https://nodejs.org/en/download/releases/) 确认版本是否已发布。
3. **镜像源更新**：国内镜像源通常滞后官方源几分钟到几小时，最新版本建议优先尝试官方源。
4. **脚本安全**：从非官方渠道获取的脚本需先检查代码，避免恶意内容。

通过以上两种方案，可完美解决国内网络环境下 NVM 安装 Node.js 时的镜像源切换问题，兼顾灵活性和高效性。
