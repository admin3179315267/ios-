# 机标查询 · iOS 打包说明

这个目录是把「查询网页」打包成 iPhone 应用（IPA）的完整工程。它用一个 `WKWebView` 加载本地 `index.html`，所有查询逻辑都在手机本地运行，全程离线、无需把图片上传到任何服务器。

> 原理说明：iOS 应用必须在 macOS + Xcode 下编译。这个工程用 **GitHub Actions 的 macOS 云机器**自动完成编译，你只需要把文件推到 GitHub，再下载编译好的 `.ipa` 即可，全程不用买 Mac。

---

## 一、准备一个 GitHub 账号

1. 打开 https://github.com ，点右上角 **Sign up** 注册（免费）。
2. 注册完成后登录。

---

## 二、把本目录上传到 GitHub

任选下面 A / B 一种方式。

### 方式 A：用 git 命令（推荐）

在你的电脑上安装 git（https://git-scm.com/ ），然后打开「PowerShell」，依次执行：

```powershell
cd "d:\新版min出图机\min出图机\查询网页\ios-app"
git init
git add .
git commit -m "init"
```

接着到 GitHub 网页创建一个 **空仓库**（New repository，仓库名随意，比如 `chaxun-ios`，选 **Private 私有** 更安全）。

创建后，回到 PowerShell 继续执行（把下面网址换成你仓库的真实地址）：

```powershell
git branch -M main
git remote add origin https://github.com/你的用户名/chaxun-ios.git
git push -u origin main
```

### 方式 B：用 GitHub 网页直接上传（不用装 git）

1. 在 GitHub 上新建仓库并进入仓库页。
2. 点 **Add file → Upload files**。
3. 把本目录里的这几个东西**拖进去**（保持目录结构）：
   - `project.yml`
   - `Sources/`（整个文件夹）
   - `Resources/`（整个文件夹）
   - `.github/`（整个文件夹）
4. 提交（Commit changes）。

> 提示：`.github` 是隐藏文件夹。在 Windows 资源管理器里，先在「查看」里勾选「隐藏的项目」就能看到它。

---

## 三、触发云编译并下载 IPA

1. 在仓库页面点顶部 **Actions** 标签。
2. 左侧选 **Build iOS IPA**，点 **Run workflow** → **Run workflow**（绿色按钮）。
3. 等几分钟（首次约 5–10 分钟，会安装 XcodeGen 并编译）。
4. 编译完成后，点进这次运行记录，最下方 **Artifacts** 里会出现 `QueryTool-ipa`，点击下载。
5. 解压下载的 zip，得到 `QueryTool.ipa`。

> 以后每次想重新编译（比如改了 `index.html`），先把新文件推到仓库，再照第三步点一次 Run workflow 即可。

---

## 四、把 IPA 装到 iPhone（关键：签名）

从 GitHub 下载的 IPA 是**未签名**的，必须签名后才能装到手机。三种方式：

### 方式 1：AltStore / SideStore（免费，最常用）
- 在电脑装 [AltStore](https://altstore.io)（Windows 需要同时装 iTunes 和 iCloud），或 iPhone 上装 SideStore。
- 用它安装 `QueryTool.ipa`，会用你的免费 Apple ID 自动签名。
- 免费签名有效期 **7 天**，到期需在同一 Wi-Fi 下用 AltStore 刷新重签。

### 方式 2：TrollStore（永久，免签）
- 若你的 iPhone 是兼容机型/系统（越狱或特定 iOS 版本），用 TrollStore 安装后**永久有效、无需续签**。

### 方式 3：付费 Apple 开发者账号（$99/年）
- 一次性签名，有效期一年，可正常分发。适合长期正式使用。

> 三种方式安装的 App 都属于「侧载」：免费账号 7 天重签、付费账号一年、TrollStore 永久，自行按实际情况选择。

---

## 五、想改的地方

| 想改什么 | 改哪里 |
|---|---|
| App 名称（现为「机标查询」） | `project.yml` 里的 `CFBundleDisplayName` |
| 包名（现为 `com.minchutu.querytool`） | `project.yml` 里的 `PRODUCT_BUNDLE_IDENTIFIER` |
| 应用图标 | 替换 `Resources/Assets.xcassets/AppIcon.appiconset/AppIcon.png`（1024×1024 PNG） |
| 页面内容 | 替换 `Resources/www/index.html` |
| iOS 最低版本 | `project.yml` 里的 `deploymentTarget` |

改完后重新推送到 GitHub，再跑一次 Actions 即可。

---

## 六、目录结构

```
ios-app/
├── project.yml              # XcodeGen 工程配置
├── Sources/                 # Swift 原生壳（WKWebView）
│   ├── AppDelegate.swift
│   └── ViewController.swift
├── Resources/
│   ├── www/index.html       # 你的查询网页（打包的核心内容）
│   └── Assets.xcassets/     # 应用图标
├── .github/workflows/       # 云端编译 IPA 的自动化脚本
└── README.md
```