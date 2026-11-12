<p align="center">
  <img src="https://raw.githubusercontent.com/mzh2077/SNNUAutoLogin-GUI/main/Assets/app_256.png" width="96" height="96" alt="SNNUAutoLogin-GUI 应用图标">
</p>

# SNNUAutoLogin-GUI

陕师大校园网自动登录客户端。使用 WinUI 3 构建，可在 Windows 托盘后台运行，支持开机登录、断线重连和按网络场景切换账号。

<p align="center">
  <img src="https://img.shields.io/badge/Windows-10%202004%2B%20%7C%2011-0078D4" alt="Windows 10 2004 及以上或 Windows 11">
  <img src="https://img.shields.io/badge/.NET-8.0-512BD4" alt=".NET 8.0">
  <img src="https://img.shields.io/badge/WinUI-3%20%2F%20Windows%20App%20SDK%201.8-0067C0" alt="WinUI 3 与 Windows App SDK 1.8">
  <img src="https://img.shields.io/badge/self--contained-%E6%97%A0%E9%9C%80%E8%BF%90%E8%A1%8C%E5%BA%93-2E7D32" alt="发布包自带运行时">
  <a href="https://github.com/mzh2077/SNNUAutoLogin-GUI/releases/latest"><img src="https://img.shields.io/github/v/release/mzh2077/SNNUAutoLogin-GUI?label=version&amp;color=E05D29" alt="最新正式版本"></a>
</p>

[下载最新版](https://github.com/mzh2077/SNNUAutoLogin-GUI/releases/latest) · [查看更新记录](https://github.com/mzh2077/SNNUAutoLogin-GUI/releases) · [反馈问题](https://github.com/mzh2077/SNNUAutoLogin-GUI/issues)

![SNNUAutoLogin-GUI 主界面](https://raw.githubusercontent.com/mzh2077/SNNUAutoLogin-GUI/main/docs/ui-preview.png)

> 这是面向陕西师范大学校园网的非官方项目。请在自己的设备上使用，并遵守学校的网络使用规定。

## 安装和更新

发布包适用于 **Windows 10 2004（19041）及以上或 Windows 11，x64**。包内已包含 .NET 和 Windows App SDK 运行时。

| 下载文件 | 使用方法 |
| --- | --- |
| `SNNUAutoLogin-Setup-<版本>.exe` | 双击安装。默认安装到当前用户目录 `%LOCALAPPDATA%\Programs\SNNUAutoLogin`。 |
| `SNNUAutoLogin-Portable-<版本>-win-x64.zip` | 将整个压缩包解压到固定目录，再运行其中的 `SNNUAutoLogin.exe`。 |
| `SHA256SUMS.txt` | 用于核对安装包和便携包的 SHA-256。 |

安装版可以直接覆盖升级，保留已保存的账号、密码和设置。移动便携版目录后，手动启动一次以修正已启用的开机自启路径。卸载安装版会删除当前用户的本地配置和凭据。

1.6.0 发布包早于 #5 合并。守卫并发、换网状态和退出流程的后续修复可从 `main` 构建。 [1.6.0 发布记录](https://github.com/mzh2077/SNNUAutoLogin-GUI/releases/tag/1.6.0) · [PR #5](https://github.com/mzh2077/SNNUAutoLogin-GUI/pull/5)

## 第一次使用

1. 先连接陕师大校园 Wi-Fi 或有线网络，再启动客户端。
2. 在「默认账号与出口」填入学号和校园网密码，按实际接入方式选择校园网直连、移动、联通或电信。
3. 点击「立即登录」。如果门户要求图形验证码，在客户端输入后继续。
4. 按需设置「开机自动登录」和「断线自动重连」。前者在 Windows 用户登录、网络就绪后完成一次认证。后者负责运行期间掉线后的重新认证，巡检间隔可设为 4–120 秒。

启用「关闭时驻留托盘」后，关窗仍会继续巡检。要结束程序，在托盘图标的右键菜单点「退出」。

## 连接规则和设备管理

- **连接规则**可以为有线网络、任意 Wi-Fi 或指定 Wi-Fi（SSID）分别配置账号、密码和运营商出口。无线规则优先匹配指定 SSID，其次匹配任意 Wi-Fi。未匹配时使用「默认账号与出口」。门户接入端口由客户端自动发现。
- **在线设备**可以查看当前账号的在线设备和下线其他会话。底部「断开连接」用于断开本机。自动重连开启时，后续巡检仍可能重新认证。
- **设备数守卫**默认关闭。开启后，在线设备数超过阈值（默认 2 台，含本机）时才检测网络连通性。检测失败才尝试断开本机并重新认证。默认探测目标是 `www.snnu.edu.cn`，超时为 3 秒。「立即检测一次」只执行探测。自动处置后有 10 分钟冷却，连续 3 次断开失败会停止自动处置。
- **锁屏保持连接**允许程序在锁屏期间继续巡检，并请求系统避免因空闲进入睡眠。手动睡眠、休眠和合盖仍按 Windows 电源设置处理。

## 凭据与网络请求

设置保存在 `%LOCALAPPDATA%\SNNUAutoLogin\settings.json`。默认账号和各条规则的密码由 Windows DPAPI 按当前用户加密，保存在 `secrets.json`。从旧版迁移时会保留 `secret.bin`，供旧版读取。

账号和密码用于学校认证门户。设备数守卫的连通性检查会向配置的目标发起 ICMP 或 HTTP 请求。程序没有遥测或自动更新检查。

## 常见问题

| 情况 | 怎么处理 |
| --- | --- |
| 无法登录或门户不可达 | 确认已连接校园网，核对账号、密码和运营商出口。可运行 `.\SNNUAutoLogin.exe --diag`，在 `%TEMP%\snnu_diag.txt` 查看门户可达性和认证状态。 |
| 断开后马上又上线 | 手动断开本机后，已启用的「断线自动重连」可能会再次登录。要保持离线，先关闭该开关。 |
| 开机后没有启动 | 检查「开机自动登录」和 Windows「启动应用」是否都已启用。移动便携版后先手动打开一次。 |
| 找不到主窗口 | 在通知区域或折叠区双击托盘图标。 |
| 从 1.3.x 升级后出现「还原解析」 | 这是旧版加速功能留下的 `hosts` 条目。可通过提示里的「还原解析」移除旧条目。 |

## 从源码构建

在 Windows x64 上安装 [.NET 8 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)。Windows App SDK 依赖由 NuGet 还原。

```powershell
git clone https://github.com/mzh2077/SNNUAutoLogin-GUI.git
Set-Location SNNUAutoLogin-GUI
dotnet build SNNUAutoLogin.csproj -c Release -p:Platform=x64
```

在仓库根目录运行 `./tools/build_portable.ps1` 生成便携版。安装 [Inno Setup 6](https://jrsoftware.org/isinfo.php) 后，也可运行 `./tools/build_release.ps1`，一次生成安装版、便携版和校验文件。默认输出目录为 `artifacts/`。

更新版本时同步修改 `SNNUAutoLogin.csproj` 和 `installer/SNNUAutoLogin.iss` 的版本号。保留安装器的 `AppId` 以支持覆盖升级。

## 测试和贡献

从 `main` 创建分支，通过 PR 提交，并查看 Windows release build 的 `portable` 检查。以下测试可在本地运行。

```powershell
dotnet run --project tests/StartupTests -c Release
dotnet run --project tests/CredentialTests -c Release
dotnet run --project tests/DeviceListTests -c Release
dotnet run --project tests/GuardTests -c Release
```

Windows CI 为开机自启测试传入 `--registry-integration`，验证临时注册表键的创建和清理，并检查安装、启动及卸载。涉及换网或门户行为的改动，可在真实校园网运行 `dotnet run --project tests/LiveCheck -c Release` 补做验证。 [Windows 构建工作流](https://github.com/mzh2077/SNNUAutoLogin-GUI/blob/main/.github/workflows/portable.yml)

反馈问题时，请附上 Windows 版本、客户端版本、接入方式和复现步骤。分享诊断日志前，请遮盖学号、IP、MAC 等信息。

感谢 [@mzh2077](https://github.com/mzh2077) 维护项目，以及 [@marike86](https://github.com/marike86) 对开机自启、凭据保存和回归测试的贡献。 [贡献者](https://github.com/mzh2077/SNNUAutoLogin-GUI/graphs/contributors)
