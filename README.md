# workbuddy-browser-cli

WorkBuddy 适配版的浏览器控制技能。把 [sleepinginsummer/agent-browser-cli](https://github.com/sleepinginsummer/agent-browser-cli)（MIT 许可）的 CLI + Chrome 扩展桥，打包成可直接放进 WorkBuddy 的技能。

- 复用你真实 Chrome 的登录态与 Cookie
- 标签页扫描 / 切换、页面 JS 执行、Cookie 读取、CDP 控制、截图、PDF、文件上传、下拉框点击
- 不是 Selenium / Playwright

## 本仓库包含什么

- `SKILL.md` — WorkBuddy 技能定义（含 WorkBuddy 专属安装说明与完整操作 SOP）
- `references/operations.md` — 运维排障手册
- `chrome-extensions.zip` — 已内置的 Chrome 扩展（`tmwd_cdp_bridge`），离线可装
- `LICENSE` — MIT（含上游出处）

> 本仓库**不**分发 CLI 原生二进制。CLI 通过上游 npm 安装：
> `npm install -g @sleepinsummer/agent-browser-cli`

## 安装到 WorkBuddy

### 方式 A：直接放本仓库（推荐，离线自包含）

1. 把 `SKILL.md` 与 `references/` 复制到 `~/.workbuddy/skills/workbuddy-browser-cli/`
2. `npm install -g @sleepinsummer/agent-browser-cli`
3. 解压 `chrome-extensions.zip`，在 Chrome `chrome://extensions` 开启开发者模式 → 加载已解压的 `tmwd_cdp_bridge` 目录
4. Chrome 至少打开一个普通网页标签页（不要停在 `about:blank` / `chrome://`）

### 方式 B：用上游 install-skill（需补一条 WorkBuddy 软链）

1. `npm install -g @sleepinsummer/agent-browser-cli`
2. `agent-browser-cli install-skill`（自动软链到 Codex / Claude / Kimi CLI / Cursor / Gemini，**不含 WorkBuddy**）
3. `ln -s ~/.agents/skills/agent-browser-cli ~/.workbuddy/skills/agent-browser-cli`
4. 加载扩展（本仓库 zip 或上游 Release 的 `chrome-extensions.zip`）

## 验证

```bash
agent-browser-cli tabs      # ok:true 且 tabs_count > 0
agent-browser-cli open https://www.baidu.com
agent-browser-cli status    # healthy:true, summary:"ready"
```

## Release 说明

本仓库按语义化版本打 tag 并发布 GitHub Release。`chrome-extensions.zip` 作为 Release 资产随附，便于离线安装。

## 许可证

MIT。SKILL.md / references/operations.md 改编自上游同名文件；Chrome 扩展来自上游 Release。版权归 sleepinginsummer（2026）。详见 [LICENSE](./LICENSE)。
