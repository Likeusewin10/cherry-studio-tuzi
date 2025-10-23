# 构建和更新指南

本指南介绍如何将二次开发的 Cherry Studio 打包为可执行文件，以及如何同步上游更新。

## 一、打包为可执行文件

### 前置条件

1. **Node.js 版本要求**: >= 22.0.0
2. **包管理器**: Yarn 4.9.1+
3. **系统要求**:
   - **Windows**: Windows 10/11 (推荐)
   - **macOS**: macOS 10.13+ (High Sierra 或更高版本)

### 安装依赖

```bash
# 使用 yarn 安装所有依赖
yarn install
```

### 打包命令

项目已配置好打包脚本，根据目标平台选择相应命令：

#### 1. Windows 平台

```bash
# 打包 Windows 版本 (x64 + ARM64)
yarn build:win

# 仅打包 x64 版本
yarn build:win:x64

# 仅打包 ARM64 版本
yarn build:win:arm64
```

**输出位置**: `dist/` 目录下会生成：
- `.exe` 安装程序
- `-win.zip` 便携版压缩包

#### 2. macOS 平台

```bash
# 打包 macOS 版本 (Apple Silicon + Intel)
yarn build:mac

# 仅打包 Apple Silicon (M1/M2/M3)
yarn build:mac:arm64

# 仅打包 Intel 芯片
yarn build:mac:x64
```

**输出位置**: `dist/` 目录下会生成：
- `.dmg` 磁盘映像文件
- `-mac.zip` 压缩包

**注意**: 在 Windows 上打包 macOS 应用可能会遇到问题，建议在 macOS 系统上打包。

#### 3. Linux 平台

```bash
# 打包 Linux 版本 (x64 + ARM64)
yarn build:linux

# 仅打包 x64 版本
yarn build:linux:x64

# 仅打包 ARM64 版本
yarn build:linux:arm64
```

**输出位置**: `dist/` 目录下会生成：
- `.AppImage` 应用镜像
- `.deb` Debian/Ubuntu 安装包
- `.rpm` RedHat/Fedora 安装包

### 打包前的检查

在正式打包发布版本前，建议先运行质量检查：

```bash
# 运行代码检查和测试
yarn build:check

# 该命令会执行：
# - yarn lint (代码格式和规范检查)
# - yarn test (单元测试)
```

### 自定义打包配置

打包配置位于 `electron-builder.yml` 文件中，你可以修改：

- 应用名称
- 应用图标
- 安装程序设置
- 代码签名配置
- 自动更新服务器地址

查找该文件：
```bash
# 查找 electron-builder 配置文件
find . -name "electron-builder.yml" -o -name "electron-builder.json"
```

### 代码签名（可选）

对于生产环境发布，建议对应用进行代码签名：

#### Windows 代码签名

Windows 应用的代码签名可以防止用户下载时出现 "Windows 已保护你的电脑" 或 "未知发布者" 的警告，提升用户信任度。

##### 1. 前置准备

**必需条件**：
- Windows 代码签名证书（EV 或 OV 证书）
- 可以在 Windows、macOS 或 Linux 上进行签名
- 签名工具会由 electron-builder 自动处理

##### 2. 购买代码签名证书

**证书类型选择**：

| 证书类型 | 价格 | 验证方式 | 即时声誉 | 推荐度 |
|---------|------|---------|---------|--------|
| **EV 代码签名证书** | $300-500/年 | 企业验证 + 硬件令牌 | ✅ 立即获得 SmartScreen 信任 | ⭐⭐⭐⭐⭐ |
| **OV 代码签名证书** | $100-300/年 | 企业/个人验证 | ❌ 需要积累信誉（数周到数月） | ⭐⭐⭐ |

**推荐证书提供商**：
1. **DigiCert**（最权威）
   - 网址：https://www.digicert.com/
   - EV 证书：~$474/年
   - OV 证书：~$474/年（Code Signing Certificate）

2. **Sectigo（原 Comodo）**（性价比高）
   - 网址：https://sectigo.com/
   - EV 证书：~$359/年
   - OV 证书：~$179/年

3. **GlobalSign**
   - 网址：https://www.globalsign.com/
   - EV 证书：~$599/年
   - OV 证书：~$249/年

4. **SSL.com**（价格实惠）
   - 网址：https://www.ssl.com/
   - EV 证书：~$299/年
   - OV 证书：~$199/年

**证书申请时间**：
- **EV 证书**：3-7 个工作日（需要企业验证 + 邮寄硬件令牌）
- **OV 证书**：1-3 个工作日（需要组织/个人验证）

##### 3. 证书申请流程

**步骤一：选择证书并下单**
1. 访问证书提供商网站
2. 选择 "Code Signing Certificate"（OV）或 "EV Code Signing Certificate"（EV）
3. 填写订单信息
4. 支付费用

**步骤二：完成身份验证**

**对于 OV 证书（组织验证）**：
1. 提交公司注册文件（营业执照等）
2. 接听验证电话或回复验证邮件
3. 等待审核通过（1-3 天）

**对于 EV 证书（扩展验证）**：
1. 提交更详细的公司文件
2. 完成更严格的身份验证
3. 接收物理硬件令牌（USB Token）
4. 等待审核和邮寄（3-7 天）

**步骤三：获取证书**

**OV 证书**：
- 通过邮件接收证书文件（.pfx 或 .p12 格式）
- 或在证书提供商后台下载

**EV 证书**：
- 接收硬件 USB Token（证书已预装在里面）
- 安装驱动程序（通常为 SafeNet 或 Gemalto）

##### 4. 准备证书文件

**对于 OV 证书（.pfx/.p12 文件）**：

证书通常已经是 .pfx 或 .p12 格式，可以直接使用。如果收到的是其他格式，需要转换：

```bash
# 如果有 .cer 和 .key 文件，转换为 .pfx
openssl pkcs12 -export \
  -out certificate.pfx \
  -inkey private-key.key \
  -in certificate.cer \
  -certfile ca-bundle.cer
```

**对于 EV 证书（USB Token）**：

1. 插入 USB Token
2. 安装硬件驱动（SafeNet Authentication Client）
3. 确认证书已安装在设备中

##### 5. 配置环境变量

**方案一：使用 .pfx/.p12 文件（OV 证书）**

在 Windows 上：
```cmd
# 设置证书路径
set CSC_LINK=C:\path\to\certificate.pfx

# 设置证书密码
set CSC_KEY_PASSWORD=your_certificate_password
```

在 macOS/Linux 上：
```bash
export CSC_LINK=/path/to/certificate.pfx
export CSC_KEY_PASSWORD=your_certificate_password
```

**方案二：使用 USB Token（EV 证书）**

```cmd
# Windows 上，electron-builder 会自动检测 USB Token
# 不需要设置 CSC_LINK，但需要设置证书名称
set WIN_CSC_LINK=<证书指纹或名称>

# 或者通过 Windows 证书存储
set CSC_LINK=CurrentUser\My\<certificate-thumbprint>
```

查找证书指纹：
```cmd
# 打开证书管理器
certmgr.msc

# 或使用 PowerShell 查看
Get-ChildItem -Path Cert:\CurrentUser\My | Format-List Subject, Thumbprint
```

##### 6. 更新 electron-builder 配置

在 `electron-builder.yml` 中添加 Windows 签名配置：

```yaml
win:
  target:
    - nsis
    - zip
  certificateSubjectName: "Your Company Name"  # EV 证书使用
  # 或
  # certificateSha1: "THUMBPRINT"  # 证书指纹
  signingHashAlgorithms:
    - sha256
  signDlls: true  # 同时签名 DLL 文件
  rfc3161TimeStampServer: http://timestamp.digicert.com
  timeStampServer: http://timestamp.digicert.com

nsis:
  oneClick: false
  allowToChangeInstallationDirectory: true
  createDesktopShortcut: true
  createStartMenuShortcut: true
  shortcutName: "Cherry Studio"
```

**时间戳服务器列表**（推荐使用，证书过期后签名仍然有效）：
- DigiCert: `http://timestamp.digicert.com`
- Sectigo: `http://timestamp.sectigo.com`
- GlobalSign: `http://timestamp.globalsign.com`

##### 7. 执行打包（自动签名）

```bash
# Windows 上
yarn build:win

# macOS/Linux 上（使用 .pfx 文件）
yarn build:win

# electron-builder 会自动：
# 1. 检测证书（文件或 USB Token）
# 2. 对 .exe 和 .dll 文件进行签名
# 3. 添加时间戳（防止证书过期后失效）
# 4. 生成安装包
```

打包过程中会看到：
```
• signing         file=dist\win-unpacked\Cherry Studio.exe ...
• signing         file=dist\Cherry-Studio-Setup-1.0.0.exe ...
```

##### 8. 验证签名

**方法一：使用 Windows 资源管理器**
1. 右键点击 .exe 文件
2. 选择 "属性"
3. 切换到 "数字签名" 选项卡
4. 查看签名详细信息

**方法二：使用 signtool（命令行）**

```cmd
# 验证签名
signtool verify /pa /v "dist\Cherry-Studio-Setup-1.0.0.exe"

# 查看签名详细信息
signtool verify /pa /v /d "dist\Cherry-Studio-Setup-1.0.0.exe"

# 应该看到：
# Successfully verified: dist\Cherry-Studio-Setup-1.0.0.exe
```

**方法三：使用 PowerShell**
```powershell
Get-AuthenticodeSignature "dist\Cherry-Studio-Setup-1.0.0.exe" | Format-List *
```

##### 9. SmartScreen 信誉累积

**对于 EV 证书**：
- ✅ **即时信任**：应用会立即被 Windows SmartScreen 信任
- ✅ 用户下载时不会出现警告

**对于 OV 证书**：
- ❌ **需要累积信誉**：初期仍会显示 "未知发布者" 警告
- 📈 **信誉累积**：需要数周到数月时间，取决于：
  - 下载量（越多越快）
  - 用户反馈（无恶意报告）
  - 证书使用时长

**加速信誉累积的方法**：
1. 尽可能多的用户下载和运行
2. 避免被报告为恶意软件
3. 保持证书持续使用
4. 考虑向 Microsoft 提交应用以加速审核

##### 10. 手动签名（高级）

如果自动签名失败，可以手动签名：

```cmd
# 安装 Windows SDK 获取 signtool
# 下载地址：https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/

# 使用 .pfx 文件签名
signtool sign ^
  /f "C:\path\to\certificate.pfx" ^
  /p "password" ^
  /tr http://timestamp.digicert.com ^
  /td sha256 ^
  /fd sha256 ^
  "dist\Cherry-Studio-Setup-1.0.0.exe"

# 使用证书存储中的证书签名
signtool sign ^
  /n "Your Company Name" ^
  /tr http://timestamp.digicert.com ^
  /td sha256 ^
  /fd sha256 ^
  "dist\Cherry-Studio-Setup-1.0.0.exe"

# 使用 USB Token 签名
signtool sign ^
  /sha1 CERTIFICATE_THUMBPRINT ^
  /tr http://timestamp.digicert.com ^
  /td sha256 ^
  /fd sha256 ^
  "dist\Cherry-Studio-Setup-1.0.0.exe"
```

##### 11. 常见问题

**Q: 签名失败，提示 "Certificate not found"**
- 确认 CSC_LINK 路径正确
- 检查证书密码是否正确
- 对于 EV 证书，确保 USB Token 已插入

**Q: 签名成功，但用户仍然看到警告**
- OV 证书需要时间累积信誉
- 考虑升级到 EV 证书
- 确保时间戳服务器正常工作

**Q: 时间戳添加失败**
- 更换其他时间戳服务器
- 检查网络连接
- 可以暂时移除时间戳配置（不推荐）

**Q: 需要同时签名多个文件？**
```bash
# 签名整个目录的所有 .exe 和 .dll
for /r "dist\win-unpacked" %f in (*.exe *.dll) do signtool sign /f cert.pfx /p password "%f"
```

##### 12. 成本和时间对比

| 项目 | EV 证书 | OV 证书 |
|-----|---------|---------|
| **价格** | $299-599/年 | $100-300/年 |
| **申请时间** | 3-7 天 | 1-3 天 |
| **即时信任** | ✅ 是 | ❌ 否 |
| **硬件要求** | ✅ USB Token | ❌ 无 |
| **推荐场景** | 商业发布 | 个人项目/测试 |

##### 13. 无代码签名证书的临时方案

如果暂时没有证书，用户下载后会看到警告，需要：

1. 点击 "更多信息"
2. 点击 "仍要运行"
3. 或在 "Windows 安全中心" → "应用和浏览器控制" 中调整设置

**注意**：无签名的应用会严重影响用户信任度和下载转化率，强烈建议购买证书。

##### 14. 推荐方案

**小团队/个人开发者**：
- 先使用 **OV 证书**（$100-200/年）
- 累积一定用户量后升级到 EV 证书

**商业发布/企业**：
- 直接购买 **EV 证书**（$300-500/年）
- 避免 SmartScreen 警告，提升用户体验

**测试阶段**：
- 可以暂时不签名
- 告知测试用户忽略警告
- 正式发布前务必购买证书

#### macOS 代码签名和公证

macOS 应用的发布需要经过**代码签名**和**公证（Notarization）**两个步骤，否则用户下载后会提示"已损坏"或"无法验证开发者"。

##### 1. 前置准备

**必需条件**：
- Apple Developer 账号（个人或企业，每年 $99 USD）
- 在 macOS 系统上进行打包（不能在 Windows/Linux 上完成）
- Xcode 命令行工具：`xcode-select --install`

##### 2. 获取代码签名证书

**步骤一：登录 Apple Developer**
1. 访问 https://developer.apple.com/account/
2. 登录你的 Apple Developer 账号

**步骤二：创建证书**
1. 进入 "Certificates, Identifiers & Profiles"
2. 点击 "Certificates" → "+" 创建新证书
3. 选择 "Developer ID Application"（用于在 App Store 外分发）
4. 按照提示在 Mac 上使用 "钥匙串访问" 创建证书请求（CSR）
5. 上传 CSR 文件，下载生成的证书（.cer 文件）
6. 双击 .cer 文件导入到钥匙串

**步骤三：导出证书为 .p12 格式**
1. 打开 "钥匙串访问" 应用
2. 在 "我的证书" 中找到刚导入的证书
3. 右键 → "导出"，选择 .p12 格式
4. 设置密码保护（记住这个密码）

##### 3. 创建 App 专用密码

为了进行公证，需要创建 App 专用密码：
1. 访问 https://appleid.apple.com/
2. 登录你的 Apple ID
3. 在 "安全" 部分，找到 "App 专用密码"
4. 点击 "生成密码"，输入标签（如 "Cherry Studio Notarization"）
5. **保存生成的密码**（格式：xxxx-xxxx-xxxx-xxxx）

##### 4. 配置环境变量

在打包前设置以下环境变量：

```bash
# Apple ID（用于公证）
export APPLE_ID=your@email.com

# App 专用密码（第3步生成的）
export APPLE_ID_PASSWORD=xxxx-xxxx-xxxx-xxxx

# 或者使用 Apple Team ID（推荐）
export APPLE_TEAM_ID=XXXXXXXXXX  # 在 Apple Developer 账号页面查看

# 证书文件路径和密码
export CSC_LINK=/path/to/your/certificate.p12
export CSC_KEY_PASSWORD=your_certificate_password

# 公证时使用的 Bundle ID（可选，默认使用 package.json 中的 appId）
export APPLE_APP_SPECIFIC_PASSWORD=$APPLE_ID_PASSWORD
```

##### 5. 更新 electron-builder 配置

在 `electron-builder.yml` 中添加 macOS 签名配置：

```yaml
mac:
  target:
    - dmg
    - zip
  category: public.app-category.productivity
  hardenedRuntime: true
  gatekeeperAssess: false
  entitlements: build/entitlements.mac.plist
  entitlementsInherit: build/entitlements.mac.plist
  identity: "Developer ID Application: Your Name (TEAM_ID)"

dmg:
  sign: true

afterSign: build/notarize.js  # 公证脚本（可选）
```

##### 6. 创建 Entitlements 文件

创建 `build/entitlements.mac.plist`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>com.apple.security.cs.allow-jit</key>
  <true/>
  <key>com.apple.security.cs.allow-unsigned-executable-memory</key>
  <true/>
  <key>com.apple.security.cs.allow-dyld-environment-variables</key>
  <true/>
  <key>com.apple.security.network.client</key>
  <true/>
  <key>com.apple.security.network.server</key>
  <true/>
</dict>
</plist>
```

##### 7. 执行打包（自动签名和公证）

```bash
# 设置环境变量后执行打包
yarn build:mac

# electron-builder 会自动：
# 1. 使用证书对应用进行签名
# 2. 将应用上传到 Apple 进行公证
# 3. 等待公证完成（通常 5-15 分钟）
# 4. 将公证票据附加到应用
```

##### 8. 验证签名和公证

打包完成后，验证应用是否正确签名和公证：

```bash
# 验证代码签名
codesign -dv --verbose=4 dist/mac/YourApp.app

# 验证公证状态
spctl -a -vv -t install dist/mac/YourApp.app

# 应该看到：
# dist/mac/YourApp.app: accepted
# source=Notarized Developer ID
```

##### 9. 手动公证（如果自动公证失败）

如果自动公证失败，可以手动提交：

```bash
# 压缩应用
ditto -c -k --keepParent dist/mac/YourApp.app YourApp.zip

# 上传到 Apple 公证
xcrun notarytool submit YourApp.zip \
  --apple-id "your@email.com" \
  --password "xxxx-xxxx-xxxx-xxxx" \
  --team-id "TEAM_ID" \
  --wait

# 查看公证日志（如果失败）
xcrun notarytool log <submission-id> \
  --apple-id "your@email.com" \
  --password "xxxx-xxxx-xxxx-xxxx" \
  --team-id "TEAM_ID"

# 公证成功后，附加票据
xcrun stapler staple dist/mac/YourApp.app
```

##### 10. 无开发者账号的临时方案

如果暂时没有 Apple Developer 账号，用户下载后可以执行：

```bash
# 移除隔离属性（用户需要手动执行）
sudo xattr -cr /Applications/YourApp.app

# 或者在"系统偏好设置" → "安全性与隐私"中选择"仍要打开"
```

**注意**：这种方式不适合正式发布，会影响用户体验和信任度。

##### 11. 常见问题

**Q: 公证失败，提示 "Invalid Hardened Runtime"**
- 确保 `hardenedRuntime: true` 已设置
- 检查 entitlements.mac.plist 文件是否正确

**Q: 公证失败，提示 "Invalid Binary"**
- 确保在 macOS 系统上打包
- 检查是否所有依赖都正确签名

**Q: 签名后应用无法启动**
- 检查 entitlements 权限是否过于严格
- 尝试添加必要的权限（如 JIT、网络访问）

##### 12. 成本和时间

- **Apple Developer 账号**：$99/年（必需）
- **证书申请**：即时（几分钟）
- **首次配置**：1-2 小时
- **每次公证**：5-15 分钟
- **证书有效期**：1 年（需每年更新）

---

## 二、同步上游更新

由于你们对原项目进行了二次开发，需要定期同步上游的更新。以下是推荐的 Git 工作流程。

### 初始设置（仅首次需要）

```bash
# 1. 查看当前远程仓库
git remote -v

# 2. 添加上游仓库（原项目）
git remote add upstream https://github.com/CherryHQ/cherry-studio.git

# 3. 验证远程仓库
git remote -v
# 应该看到：
# origin    (你们的仓库)
# upstream  (原项目仓库)
```

### 同步更新流程

#### 方案一：合并更新（推荐）

```bash
# 1. 确保当前分支已提交所有更改
git status
git add .
git commit -m "保存当前工作"

# 2. 获取上游最新代码
git fetch upstream

# 3. 查看上游更新内容
git log HEAD..upstream/main --oneline

# 4. 合并上游更新到当前分支
git merge upstream/main

# 5. 如果有冲突，解决冲突后提交
# 打开冲突文件，手动解决标记的冲突
git add .
git commit -m "合并上游更新"

# 6. 推送到你们的远程仓库
git push origin main
```

#### 方案二：变基更新（保持提交历史整洁）

```bash
# 1. 确保当前分支已提交所有更改
git status
git add .
git commit -m "保存当前工作"

# 2. 获取上游最新代码
git fetch upstream

# 3. 使用 rebase 应用上游更新
git rebase upstream/main

# 4. 如果有冲突，解决后继续
git add .
git rebase --continue

# 5. 强制推送到远程（谨慎使用）
git push origin main --force-with-lease
```

### 处理冲突的技巧

当同步更新时出现冲突，通常是因为你们修改的文件也被上游修改了。

#### 冲突文件示例：
```typescript
<<<<<<< HEAD
// 你们的修改
const API_URL = 'https://api.tu-zi.com'
=======
// 上游的修改
const API_URL = 'https://api.cherry-ai.com'
>>>>>>> upstream/main
```

#### 解决策略：

1. **保留你们的修改**:
```bash
git checkout --ours path/to/file
git add path/to/file
```

2. **使用上游的修改**:
```bash
git checkout --theirs path/to/file
git add path/to/file
```

3. **手动合并**: 打开文件，保留需要的部分，删除冲突标记

### 选择性更新

如果只想同步特定的功能或修复：

```bash
# 1. 查看上游的提交历史
git log upstream/main --oneline

# 2. 挑选特定的提交
git cherry-pick <commit-hash>

# 3. 如果有冲突，解决后继续
git add .
git cherry-pick --continue
```

### 创建更新分支（推荐策略）

为了安全起见，建议在单独的分支上进行更新测试：

```bash
# 1. 创建更新分支
git checkout -b update-from-upstream

# 2. 合并上游更新
git fetch upstream
git merge upstream/main

# 3. 解决冲突并测试

# 4. 测试通过后合并到主分支
git checkout main
git merge update-from-upstream

# 5. 删除更新分支
git branch -d update-from-upstream
```

### 保持定制化的配置文件

对于你们的定制配置，建议：

1. **使用配置文件分离**: 将定制化配置单独放在文件中
2. **使用环境变量**: 通过 `.env` 文件管理定制配置
3. **文档化修改**: 记录所有定制化修改的位置和原因

创建一个 `CUSTOMIZATIONS.md` 文件记录你们的修改：

```markdown
# 定制化修改清单

## 修改的文件列表
1. `src/renderer/src/config/providers.ts`
   - 添加了 tuzi-default 和 tuzi-original 提供商
   - 修改了 API 链接为 https://api.tu-zi.com

2. `src/renderer/src/config/models/default.ts`
   - 添加了自定义模型配置

## 注意事项
- 更新时需要保留这些文件的修改
- 冲突时优先使用我们的配置
```

---

## 三、自动更新配置

如果希望应用支持自动更新，需要：

1. **设置更新服务器**: 托管打包后的文件
2. **配置 electron-updater**: 在 `electron-builder.yml` 中配置
3. **发布新版本**: 上传到服务器并更新版本信息

示例配置：
```yaml
publish:
  - provider: generic
    url: https://download.tu-zi.com/cherry-studio/
  - provider: github
    owner: your-org
    repo: cherry-studio-tuzi
```

---

## 四、常见问题

### Q1: 打包失败，提示缺少依赖
**解决方案**:
```bash
# 清理并重新安装依赖
rm -rf node_modules .yarn/cache
yarn install
```

### Q2: macOS 打包后无法打开，提示"已损坏"
**解决方案**: 需要对应用进行代码签名或在目标机器上执行：
```bash
xattr -cr /Applications/YourApp.app
```

### Q3: 如何回退到之前的版本
**解决方案**:
```bash
# 查看提交历史
git log --oneline

# 回退到指定提交
git reset --hard <commit-hash>

# 强制推送（谨慎！）
git push origin main --force
```

### Q4: 更新后测试发现问题，如何快速回滚
**解决方案**:
```bash
# 如果还未推送
git reset --hard ORIG_HEAD

# 如果已推送，创建回滚提交
git revert HEAD
```

---

## 五、推荐的开发流程

1. **开发分支**: 在 `develop` 分支上进行开发
2. **功能分支**: 新功能使用 `feature/xxx` 分支
3. **发布分支**: 稳定版本打 tag 并从 `main` 分支发布
4. **定期同步**: 每月或每周同步一次上游更新

```bash
# 分支管理示例
git checkout -b develop                    # 创建开发分支
git checkout -b feature/new-provider      # 创建功能分支
# ... 开发完成
git checkout develop
git merge feature/new-provider            # 合并功能
# ... 测试通过
git checkout main
git merge develop                         # 合并到主分支
git tag v1.0.0                           # 打版本标签
yarn build:win                           # 打包发布
```

---

## 六、联系和支持

- **上游项目**: https://github.com/CherryHQ/cherry-studio
- **上游文档**: https://docs.cherry-ai.com
- **问题反馈**: 在你们的仓库创建 Issue

---

**最后更新**: 2025年10月
