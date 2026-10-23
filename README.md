# dsh-review-linux

[dsh-vscode-review](https://github.com/Tlyer233/dsh-vscode-review) 的 **Linux (Ubuntu) 分支**：dsh 插件 + VS Code 扩展，把 agent 的 write/edit 收成可逐 hunk 接受 / 撤回的行内审查。

## Features

- Shadow git 记录 write/edit（以及 shell `rm` 删除）
- VS Code 行内接受 / 撤回 hunk
- ipynb 选区指针含块号：`nb.ipynb C3 L1~L5`（跨块 `C2~C4`），普通文件仍为 `path L1~L2`
- 点击 chip / Edited 卡片行 / plan 卡片「查看全文」→ 在 VS Code 打开（不抢聊天焦点；Alt+点 抢焦点；工作区外文件如 /tmp 截图也能开，0.1.70/0.1.26）
- 剪贴板贴图 / 文件管理器 Ctrl+C→Ctrl+V 粘贴成引用 chip（X11 走 xclip，Wayland 走 wl-paste）
- 仅 VS Code iframe 内显示 dock；独立浏览器不改原生对话 UI

## 依赖

```bash
sudo apt install git python3 xclip        # X11；Wayland 换 wl-clipboard
npm i -g pnpm                              # dsh plugin add 走 pnpm
# VS Code（code CLI 在 PATH）、dsh（dsh CLI 在 PATH）、node
```

## Install

```bash
./install.sh
```

装完必须：**`Ctrl+Q` 彻底退出 VS Code（只关窗口不算）→ 重开**，再重启 dsh（侧栏 Restart）。脚本幂等，升级 = 重跑。

## UnInstall

```bash
./uninstall.sh
```

## 键位（Linux）

| 操作 | 快捷键 |
|---|---|
| 编辑器选区 → dsh | `ctrl+alt+u` |
| 资源管理器选中文件 → dsh | `ctrl+alt+l` / `ctrl+shift+l` |
| 打开 dsh 侧栏 | `ctrl+alt+d` |

`install.sh` 会自动清理 keybindings.json 里永远不会触发的旧 `dshReview.*` 死行（改前写 `.bak-<时间戳>` 备份）。

## Fork 策略（与上游的关系）

- 完整保留上游 git 历史（本仓从上游 main clone 后叠加 Linux 提交），`git merge upstream/main` 无痛
- 上游 JS（`extension.js`、`lib/`、`client.js`）**保持跨平台原样**：mac/win 分支在 Linux 上是不执行的死代码，留着换来干净合并
- 只 Linux 化 `install.sh`（删 darwin/win 分支、加依赖预检、Ctrl+Q 文案），删 `.ps1`
- 扩展 ID 保持 `dsn.dsh-review-vscode`：`client.js` 消息名、`argv.json`、键位 when 子句全绑这个 ID，不改名
- `EXT_VER` 由 `install.sh` 自动读 `dsh-review-vscode/package.json`，版本永不脱节
- **仓库里不要放 `.vsix`**：`install.sh` 检测到 vsix 会优先装它，源码更新会被陈旧 vsix 顶掉

### 同步上游

```bash
git remote add upstream https://github.com/Tlyer233/dsh-vscode-review  # 已配好则跳过
git fetch upstream && git merge upstream/main   # JS 冲突直接取上游，install.sh/README 冲突留本地
```

## Storage

```text
$DSH_HOME/review/
  shadow/repo.git/          # shadow git
  shadow/pending/*.json     # 文件版本号
  plans/*.md                # plan 查看全文的落盘文件
```

## License

MIT
