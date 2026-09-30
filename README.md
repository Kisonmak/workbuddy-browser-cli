# workbuddy-browser-cli

WorkBuddy 适配版的浏览器控制技能。把 [sleepinginsummer/agent-browser-cli](https://github.com/sleepinginsummer/agent-browser-cli)（MIT 许可）的 CLI + Chrome 扩展桥，打包成可直接放进 WorkBuddy 的技能。

- 复用你真实 Chrome 的登录态与 Cookie
- 标签页扫描 / 切换、页面 JS 执行、Cookie 读取、CDP 控制、截图、PDF、文件上传、下拉框点击
- 不是 Selenium / Playwright

> 💡 **一句话让 WorkBuddy 帮你装**：在 WorkBuddy 对话框里发这一句 ——
> 「帮我把 GitHub 仓库 Kisonmak/workbuddy-browser-cli 里的技能安装到 WorkBuddy，并指导我加载 Chrome 扩展」
> —— 它会自动完成「下载仓库、放入技能目录、安装 CLI、补上系统软链、给出加载扩展的步骤」，你只需要在 Chrome 里点一下「加载已解压的扩展程序」。**不想碰命令行，就走这条路。**

## 本仓库包含什么

- `SKILL.md` — WorkBuddy 技能定义（含 WorkBuddy 专属安装说明与完整操作 SOP）
- `references/operations.md` — 运维排障手册
- `chrome-extensions.zip` — 已内置的 Chrome 扩展（`tmwd_cdp_bridge`），离线可装
- `LICENSE` — MIT（含上游出处）

> 本仓库**不**分发 CLI 原生二进制。CLI 通过上游 npm 安装：
> `npm install -g @sleepinsummer/agent-browser-cli`

## 先决条件

- 已安装 **Node.js**（自带 `npm`）：https://nodejs.org —— 装 LTS 即可
- 已安装 **Google Chrome** 桌面版（不是网页版、不是 Chrome for Testing）
- 已安装 **WorkBuddy**

---

## 平台适配：macOS / Windows

CLI 是跨平台的（npm 包自带 macOS / Windows / Linux 预编译二进制），**两平台命令完全一致**；差异只在技能目录的路径、软链方式和首次联网的防火墙弹窗。

### macOS

| 项 | 说明 |
| --- | --- |
| 技能目录 | `~/.workbuddy/skills/workbuddy-browser-cli/`<br>（`~` = `/Users/你的用户名`） |
| 上游软链 | `ln -s ~/.agents/skills/agent-browser-cli ~/.workbuddy/skills/agent-browser-cli`（自带，无需管理员） |
| 防火墙 | 一般不会弹窗；如弹窗「允许」即可 |
| 终端 | 用「终端」App，命令同上 |

### Windows

| 项 | 说明 |
| --- | --- |
| 技能目录 | `%USERPROFILE%\.workbuddy\skills\workbuddy-browser-cli\`<br>（即 `C:\Users\你的用户名\.workbuddy\skills\...`） |
| 上游软链 | 推荐**直接复制文件夹**最省事；若要软链，需「开发者模式」或在**管理员 PowerShell** 跑：<br>`mklink /D "%USERPROFILE%\.workbuddy\skills\agent-browser-cli" "%USERPROFILE%\.agents\skills\agent-browser-cli"` |
| 防火墙 | 首次 daemon 监听 `18765` 端口，Windows 可能弹「Windows 安全警报」——**勾选「专用网络」并允许** |
| 终端 | 用 PowerShell（开始菜单搜 PowerShell） |

> 偷懒建议（Windows / macOS 通用）：方式 A 是直接把 `SKILL.md` 和 `references/` 复制到 WorkBuddy 技能目录，**不需要任何软链命令**，对所有系统最友好。

### 两平台完全相同

- 加载扩展的 UI：`chrome://extensions` → 右上角开「开发者模式」→ 「加载已解压的扩展程序」
- 解压 `chrome-extensions.zip` 后，选其中的 `tmwd_cdp_bridge` 目录
- 验证命令完全一致（见下文）

---

## 安装（两种）

### 方式 A：直接放本仓库（推荐，离线自包含，小白友好）

1. 把 `SKILL.md` 与 `references/` 放进 WorkBuddy 技能目录：
   - **macOS**：`mkdir -p ~/.workbuddy/skills/workbuddy-browser-cli && cp SKILL.md references/* ~/.workbuddy/skills/workbuddy-browser-cli/`
   - **Windows (PowerShell)**：
     ```powershell
     New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.workbuddy\skills\workbuddy-browser-cli"
     Copy-Item SKILL.md, references\* "$env:USERPROFILE\.workbuddy\skills\workbuddy-browser-cli" -Recurse
     ```
2. `npm install -g @sleepinsummer/agent-browser-cli`
3. 解压 `chrome-extensions.zip`，在 Chrome `chrome://extensions` 开启开发者模式 → 加载已解压的 `tmwd_cdp_bridge` 目录
4. Chrome 至少打开一个普通网页标签页（不要停在 `about:blank` / `chrome://`）

### 方式 B：用上游 install-skill（需补一条 WorkBuddy 软链）

1. `npm install -g @sleepinsummer/agent-browser-cli`
2. `agent-browser-cli install-skill`（自动软链到 Codex / Claude / Kimi CLI / Cursor / Gemini，**不含 WorkBuddy**）
3. 补 WorkBuddy 软链：
   - **macOS**：`ln -s ~/.agents/skills/agent-browser-cli ~/.workbuddy/skills/agent-browser-cli`
   - **Windows (管理员 PowerShell)**：`mklink /D "%USERPROFILE%\.workbuddy\skills\agent-browser-cli" "%USERPROFILE%\.agents\skills\agent-browser-cli"`（或复制文件夹）
4. 加载扩展（本仓库 zip 或上游 Release 的 `chrome-extensions.zip`）

---

## 小白操作指南（一步一步，别慌）

> 最省事的办法：**直接把文章开头的那句话发给 WorkBuddy，让它替你做**。下面这份是「想自己动手」或「WorkBuddy 装完你来检查」时的对照清单。

**第 1 步 · 装 Node.js**
- 打开 https://nodejs.org ，点「LTS」下载，双击安装，一路下一步。
- 验证：打开终端 / PowerShell，输入 `node -v`，能显示版本号（如 `v22.x`）就成功。

**第 2 步 · 装浏览器控制 CLI**
- 在终端 / PowerShell 里输入：`npm install -g @sleepinsummer/agent-browser-cli`
- 验证：输入 `agent-browser-cli --help`，不报错就成功。

**第 3 步 · 放技能（二选一）**
- 懒人法：把本仓库的 `SKILL.md` 和 `references/` 文件夹，复制进 WorkBuddy 的技能目录（路径见上方「平台适配」）。
- 或：让 WorkBuddy 直接帮你做（开头那句话）。

**第 4 步 · 加载 Chrome 扩展（必须手动，AI 替不了你点）**
1. 打开你常用的 Chrome，保持它运行。
2. 地址栏输入 `chrome://extensions` 回车。
3. 右上角打开「开发者模式」开关。
4. 点左侧/左上「加载已解压的扩展程序」。
5. 选本仓库解压出来的 `chrome-extensions.zip` 里面的 `tmwd_cdp_bridge` 文件夹。
6. 列表出现「Agent Browser CLI Bridge」且开关是开着的 → 完成。
7. 开一个普通网页（如 `https://www.baidu.com`）当作测试页。

> Windows 首次这一步若弹「Windows 安全警报」，勾「专用网络」并允许，否则本机通信会被拦。

**第 5 步 · 验证**
- 在终端 / PowerShell 输入下面三条命令，结果对得上就装好了：
  ```bash
  agent-browser-cli tabs      # ok:true 且 tabs_count > 0
  agent-browser-cli open https://www.baidu.com
  agent-browser-cli status    # healthy:true, summary:"ready"
  ```

---

## 验证

```bash
agent-browser-cli tabs      # ok:true 且 tabs_count > 0
agent-browser-cli open https://www.baidu.com
agent-browser-cli status    # healthy:true, summary:"ready"
```

## 常见问题

- **`status` 显示 `extension_connected:false`**：扩展没加载或没开网页。回到「第 4 步」检查 Chrome 扩展是否启用、是否开了一个普通标签页。
- **Windows 防火墙弹窗**：允许「专用网络」即可；公网点不允许。
- **macOS `EACCES` 权限报错**：不要 `sudo npm`，改用 `npm install -g` 前先 `sudo chown -R $(whoami) $(npm config get prefix)/{lib,bin}` 修复目录归属。
- **`command not found: agent-browser-cli`**：CLI 没装好或不在 PATH；重跑第 2 步，macOS 可能需要重开终端。

## Release 说明

本仓库按语义化版本打 tag 并发布 GitHub Release。`chrome-extensions.zip` 作为 Release 资产随附，便于离线安装。

## 许可证

MIT。SKILL.md / references/operations.md 改编自上游同名文件；Chrome 扩展来自上游 Release。版权归 sleepinginsummer（2026）。详见 [LICENSE](./LICENSE)。
