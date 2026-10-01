# 安装指南（不需要 GitHub）

GitHub 只是**获取文件**的渠道之一，装到本机是另一件事。本页覆盖：不通过 GitHub 怎么拿到文件、全局与项目级各装哪里、各工具的可用命令。

---

## 一、先拿文件（三种方式任选）

### A. 下载 ZIP（不用 git，最省事）

```powershell
# Windows PowerShell
$zip = "$env:TEMP\wuhui.zip"
Invoke-WebRequest "https://github.com/cloudxys/wu-hui-hua-xia-style/archive/refs/heads/main.zip" -OutFile $zip
Expand-Archive $zip -DestinationPath "$env:TEMP\wuhui" -Force
# 解压后技能目录位于 <解压目录>\wu-hui-hua-xia-style-main\wu-hui-hua-xia-style
```

```bash
# macOS / Linux
tmp=$(mktemp -d)
curl -L -o "$tmp/wuhui.zip" https://github.com/cloudxys/wu-hui-hua-xia-style/archive/refs/heads/main.zip
unzip -q "$tmp/wuhui.zip" -d "$tmp"
# 技能目录位于 $tmp/wu-hui-hua-xia-style-main/wu-hui-hua-xia-style
```

### B. 别人直接给你文件夹

拿到 `wu-hui-hua-xia-style/` 这个目录即可（内含 `SKILL.md` 与 `references/`），拷到哪都行。

### C. 用 git 克隆（唯一需要 git 的方式）

```bash
git clone https://github.com/cloudxys/wu-hui-hua-xia-style.git
```

---

## 二、全局 vs 项目级：到底差在哪

| | **全局（用户级）** | **项目级（非全局）** |
|---|---|---|
| 装在哪 | 用户主目录下的技能根 | 项目根目录下的技能目录 |
| 生效范围 | **这台机器上所有项目** | **只有该项目/仓库** |
| 适合谁 | 个人常用工具 | 团队共享、随仓库走 |
| 跟着 git 走吗 | 不 | **会**，提交后队友拉下来就有 |
| 换机器要重装吗 | 要 | 不用（仓库自带） |

**"非全局"不等于"功能少"**——两者加载的都是同一份技能，只是可见范围不同。

---

## 三、全局安装（各工具目录）

| 工具 | 全局技能根（相对用户主目录） |
|---|---|
| **跨工具共享**（Claude Code / Gemini CLI / OpenCode / DSH 等都会读） | `~/.agents/skills/` |
| Claude Code | `~/.claude/skills/` |
| Codex | `~/.codex/skills/` |
| Gemini CLI | `~/.gemini/skills/` |
| OpenCode | `~/.config/opencode/skills/` |
| DSH（DeepSeek Harness） | `~/.dsh/skills/` |

### Windows（PowerShell）— 复制方式

```powershell
# 把 $src 改成你的技能目录（ZIP 解压目录或克隆目录）
$src = "$env:TEMP\wuhui\wu-hui-hua-xia-style-main\wu-hui-hua-xia-style"

$roots = @(
  "$env:USERPROFILE\.agents\skills",
  "$env:USERPROFILE\.claude\skills",
  "$env:USERPROFILE\.codex\skills",
  "$env:USERPROFILE\.gemini\skills",
  "$env:USERPROFILE\.config\opencode\skills",
  "$env:USERPROFILE\.dsh\skills"
)
foreach ($r in $roots) {
  New-Item -ItemType Directory -Force -Path $r | Out-Null
  $dst = Join-Path $r 'wu-hui-hua-xia-style'
  if (Test-Path $dst) { Remove-Item $dst -Recurse -Force }
  Copy-Item -LiteralPath $src -Destination $dst -Recurse -Force
  Write-Host "installed -> $dst"
}
```

### Windows（PowerShell）— 一键在线安装（不下载 ZIP 也行）

```powershell
$zip = "$env:TEMP\wuhui.zip"
Invoke-WebRequest "https://github.com/cloudxys/wu-hui-hua-xia-style/archive/refs/heads/main.zip" -OutFile $zip
Expand-Archive $zip -DestinationPath "$env:TEMP\wuhui" -Force
$src = "$env:TEMP\wuhui\wu-hui-hua-xia-style-main\wu-hui-hua-xia-style"
foreach ($r in @("$env:USERPROFILE\.agents\skills","$env:USERPROFILE\.claude\skills","$env:USERPROFILE\.codex\skills","$env:USERPROFILE\.gemini\skills","$env:USERPROFILE\.config\opencode\skills","$env:USERPROFILE\.dsh\skills")) {
  New-Item -ItemType Directory -Force -Path $r | Out-Null
  $dst = Join-Path $r 'wu-hui-hua-xia-style'
  if (Test-Path $dst) { Remove-Item $dst -Recurse -Force }
  Copy-Item -LiteralPath $src -Destination $dst -Recurse -Force
}
Write-Host "done"
```

### macOS / Linux — 复制方式

```bash
SRC="$HOME/Downloads/wu-hui-hua-xia-style"   # 改成你的技能目录

for r in "$HOME/.agents/skills" "$HOME/.claude/skills" "$HOME/.codex/skills" \
         "$HOME/.gemini/skills" "$HOME/.config/opencode/skills" "$HOME/.dsh/skills"; do
  mkdir -p "$r"
  rm -rf "$r/wu-hui-hua-xia-style"
  cp -r "$SRC" "$r/wu-hui-hua-xia-style"
  echo "installed -> $r/wu-hui-hua-xia-style"
done
```

### 全局安装的两种进阶做法

**软链接 / 目录联接**：只保留一份源，多个根都指过去，改一处即全生效。

```bash
# macOS / Linux
TARGET="$HOME/Downloads/wu-hui-hua-xia-style"
for r in "$HOME/.agents/skills" "$HOME/.claude/skills" "$HOME/.codex/skills" \
         "$HOME/.gemini/skills" "$HOME/.config/opencode/skills" "$HOME/.dsh/skills"; do
  mkdir -p "$r"
  rm -rf "$r/wu-hui-hua-xia-style"
  ln -s "$TARGET" "$r/wu-hui-hua-xia-style"
done
```

```powershell
# Windows（目录联接；不需要管理员权限）
$target = "C:\path\to\wu-hui-hua-xia-style"
foreach ($r in @("$env:USERPROFILE\.agents\skills","$env:USERPROFILE\.claude\skills","$env:USERPROFILE\.codex\skills","$env:USERPROFILE\.gemini\skills","$env:USERPROFILE\.config\opencode\skills","$env:USERPROFILE\.dsh\skills")) {
  New-Item -ItemType Directory -Force -Path $r | Out-Null
  $dst = Join-Path $r 'wu-hui-hua-xia-style'
  if (Test-Path $dst) { Remove-Item $dst -Recurse -Force }
  New-Item -ItemType Junction -Path $dst -Target $target | Out-Null
}
```

> **软链 vs 复制怎么选**：想"改一处、处处生效"用软链/联接；想"各装各的、互不影响"用复制。注意软链的代价是**源目录被移动或删除时链接会断**。

**只装共享根**：如果用的工具都读 `~/.agents/skills/`（Claude Code、Gemini CLI、OpenCode、DSH 都读），装这一个就够，不必六处都装。

---

## 四、项目级安装（非全局）

把技能放进**项目根目录**下对应工具的目录，随仓库提交即可让队友共享。

### 项目级目录约定

| 工具 | 项目内目录 |
|---|---|
| 通用 / 跨工具 | `.agents/skills/wu-hui-hua-xia-style/` |
| Claude Code | `.claude/skills/wu-hui-hua-xia-style/` |
| OpenCode | `.opencode/skills/wu-hui-hua-xia-style/` |
| Gemini CLI | `.gemini/skills/wu-hui-hua-xia-style/` |
| Cursor | `.cursor/skills/wu-hui-hua-xia-style/` |
| DSH | `.dsh/skills/wu-hui-hua-xia-style/` |

> **⚠️ "项目根"的定义**：各工具都是**从当前工作目录向上找仓库根**（以 `.git` 为准）。如果项目里没有 `.git`，就按当前工作目录算。所以最好装在**仓库根目录**，而不是某个子目录里。

### 命令行安装（在项目根目录执行）

```bash
# macOS / Linux：装进通用的 .agents/skills（多数工具都读）
mkdir -p .agents/skills
cp -r /path/to/wu-hui-hua-xia-style .agents/skills/

# 想同时兼容 Claude Code 与 OpenCode 的约定：
mkdir -p .claude/skills .opencode/skills
cp -r /path/to/wu-hui-hua-xia-style .claude/skills/
cp -r /path/to/wu-hui-hua-xia-style .opencode/skills/
```

```powershell
# Windows：在项目根目录执行
New-Item -ItemType Directory -Force -Path ".agents\skills" | Out-Null
Copy-Item -LiteralPath "C:\path\to\wu-hui-hua-xia-style" -Destination ".agents\skills\wu-hui-hua-xia-style" -Recurse -Force
```

### 随仓库共享

```bash
git add .agents/skills/wu-hui-hua-xia-style
git commit -m "chore: 引入 wu-hui-hua-xia-style 技能"
git push
```

队友 `git pull` 后即可使用，无需各自安装。

### 只想本地用、不想提交

把目录加入忽略规则：

```gitignore
# .gitignore
.agents/skills/wu-hui-hua-xia-style/
.claude/skills/wu-hui-hua-xia-style/
```

---

## 五、装完怎么确认成功

**1. 结构检查**（技能 = 一个目录 + `SKILL.md`）

```bash
ls ~/.agents/skills/wu-hui-hua-xia-style/
# 期望看到：SKILL.md  references/
```

**2. 校验 frontmatter**

```bash
head -5 ~/.agents/skills/wu-hui-hua-xia-style/SKILL.md
# 期望：name: wu-hui-hua-xia-style / description: ... / version: 1.6.0
```

**关键规则：`name` 必须与所在目录名完全一致**（`wu-hui-hua-xia-style`），否则多数工具会直接忽略该技能；名字只允许小写字母、数字与连字符。

**3. 重启工具**：技能在**会话启动时**扫描，装完必须重开，热加载一般不生效。

**4. 在会话里确认**

| 工具 | 查看方式 |
|---|---|
| Gemini CLI | `/skills list` |
| Claude Code | `/skills`（或直接问模型"你有哪些技能"） |
| DSH | 技能以系统提示注入，直接说「转成无悔华夏风格」测试 |
| OpenCode | 技能列表在 `skill` 工具的说明里 |

---

## 六、卸载

```bash
# 全局（macOS / Linux）
rm -rf ~/.agents/skills/wu-hui-hua-xia-style
# 项目级
rm -rf .agents/skills/wu-hui-hua-xia-style
```

```powershell
# Windows
Remove-Item -Recurse -Force "$env:USERPROFILE\.agents\skills\wu-hui-hua-xia-style"
```

> Windows 上如果之前是用**目录联接**装的，直接删目标目录可能误删源文件。先删链接安全：
> ```powershell
> (Get-Item "$env:USERPROFILE\.agents\skills\wu-hui-hua-xia-style" -Force).Delete()
> ```

---

## 七、常见问题

| 现象 | 原因 | 处理 |
|---|---|---|
| 工具里看不到这个技能 | 没重启 / 装错根 | 重启工具；核对第三节的目录是否与实际使用的工具一致 |
| 技能被忽略 | `SKILL.md` 的 `name` 与目录名不一致 | 目录必须叫 `wu-hui-hua-xia-style` |
| 项目级不生效 | 装在子目录，或项目没有 `.git` | 移到仓库根目录；或给项目 `git init` |
| 加载不了提示词 | 只拷了 `SKILL.md`，漏了 `references/` | 必须整个目录一起拷 |
| Windows 复制报权限错 | 目标目录被占用 | 关闭对应工具后重试 |
| 想多工具同时用 | 各工具根不同 | 统一装到 `~/.agents/skills/`，或对每个根做软链 |
