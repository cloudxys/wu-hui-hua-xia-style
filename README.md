# wuhuistyle-skills

面向 AI Agent 的**国风扁平化画风转换**技能集。当前收录 1 个技能：

| 技能 | 目录 | 作用 |
|---|---|---|
| 无悔华夏风格转换器 | [`wu-hui-hua-xia-style/`](wu-hui-hua-xia-style/SKILL.md) | 把任意**含人物**的图片整体转换为《无悔华夏》式新国风扁平插画：人物硬切面平涂 + 加粗墨线 + 不画眼睛，背景柔边云气渐变 |

> English documentation: [README.en.md](README.en.md)

## 效果对比

| 原图 | 转换结果 |
|---|---|
| <img src="docs/original.png" width="380"> | <img src="docs/result.jpg" width="380"> |

人物被重构为大块锐利切面：块内平涂、块间硬边零过渡、明暗仅 2–3 块，外轮廓加粗墨线，眼位完全留空；背景由照片转为柔边云气渐变，保留空气感与纵深。

## 这个技能解决什么问题

普通"风格迁移"提示词只说"变成扁平插画"，模型会自由发挥，典型翻车是：人物被画成柔和晕染的厚涂立绘、眼睛画出眼珠高光、背景直接沿用原照片、或是把背景做成马赛克方块。本技能把这些变成**可核对的硬指标**：

- **人物硬**：大块锐利切面，块内平涂、块间硬边零过渡，明暗只 2–3 块
- **背景柔**：3–5 层柔边云气渐变、层间带雾，禁止硬边色块与马赛克
- **不画眼睛**：眼位完全留空，无眼珠 / 眼白 / 瞳孔 / 睫毛 / 高光 / 眼线
- **从简原则**：珠串只用单色圆点、纹样只保留概括块面或粗线，禁止精细刻画
- **构图锁定**：人物数量、姿态、站位与背景布局必须与原图一致

## 安装

技能遵循通用 Agent Skills 约定：每个技能是 `SKILL.md` + 可选 `references/` 的目录。

### 方式一：克隆仓库（推荐）

```bash
git clone https://github.com/cloudxys/wu-hui-hua-xia-style.git
```

克隆下来即可用「方式二」复制或「方式三」联接；也可以直接把工具的技能目录指向 `wu-hui-hua-xia-style/`。

### 方式二：复制到各工具的全局技能目录

把 `wu-hui-hua-xia-style/` 整个目录复制到你所用工具的技能根下：

| 工具 | 全局技能目录 |
|---|---|
| 跨工具共享（Claude Code / Gemini CLI / OpenCode 等均读取） | `~/.agents/skills/` |
| Claude Code | `~/.claude/skills/` |
| Codex | `~/.codex/skills/` |
| Gemini CLI | `~/.gemini/skills/` |
| OpenCode | `~/.config/opencode/skills/` |

Windows（PowerShell）：

```powershell
$src = ".\wu-hui-hua-xia-style"
$roots = @("$env:USERPROFILE\.agents\skills", "$env:USERPROFILE\.claude\skills", "$env:USERPROFILE\.codex\skills", "$env:USERPROFILE\.gemini\skills", "$env:USERPROFILE\.config\opencode\skills")
foreach ($r in $roots) {
  New-Item -ItemType Directory -Force -Path "$r" | Out-Null
  Copy-Item -LiteralPath $src -Destination $r -Recurse -Force
}
```

macOS / Linux：

```bash
for r in ~/.agents/skills ~/.claude/skills ~/.codex/skills ~/.gemini/skills ~/.config/opencode/skills; do
  mkdir -p "$r" && cp -r wu-hui-hua-xia-style "$r/"
done
```

安装后**重启对应工具**（技能在会话启动时扫描）。

### 方式三：只装一处，用目录联接统一

把 5 个根都指向同一份源目录，避免多副本漂移（本仓库作者就是这么用的）。macOS / Linux：

```bash
target="$PWD/wu-hui-hua-xia-style"
for r in ~/.agents/skills ~/.claude/skills ~/.codex/skills ~/.gemini/skills ~/.config/opencode/skills; do
  mkdir -p "$r" && rm -rf "$r/wu-hui-hua-xia-style" && ln -s "$target" "$r/wu-hui-hua-xia-style"
done
```

Windows（PowerShell）：

```powershell
$target = "D:\path\to\wu-hui-hua-xia-style"
foreach ($r in @("$env:USERPROFILE\.agents\skills","$env:USERPROFILE\.claude\skills","$env:USERPROFILE\.codex\skills","$env:USERPROFILE\.gemini\skills","$env:USERPROFILE\.config\opencode\skills")) {
  $p = Join-Path $r "wu-hui-hua-xia-style"
  if (Test-Path $p) { Remove-Item $p -Recurse -Force }
  New-Item -ItemType Junction -Path $p -Target $target | Out-Null
}
```

## 使用

发一张含人物的图片，并说「转成无悔华夏风格」即可触发。技能会：

1. 读图并逐项记录人物（数量 / 位置 / 姿态 / 服饰 / 道具）与背景元素清单
2. 读取 `references/style-prompts.md` 的正向与负向提示词，以**原图作为图生图输入**调用图像生成能力
3. 回读成品，按 11 条自检清单核对（前 5 条一票否决），不达标就回原图重生成

**双参考图用法（强烈推荐）**：把「原图」和「一张已确认风格的样张」一起传给生图工具——图 1 锁构图、图 2 锁画风，比纯文字提示词可靠得多。

## 仓库结构

```
wu-hui-hua-xia-style/
├── README.md                        中文说明
├── README.en.md                     English documentation
├── LICENSE
├── docs/                            效果对比图与支持二维码
│   ├── original.png
│   ├── result.jpg
│   └── support.png
└── wu-hui-hua-xia-style/
    ├── SKILL.md                     技能正文：输入、识别、流程、工具调用、异常处理、验收
    └── references/
        ├── style-prompts.md         提示词与画风规范：硬指标、优先级、正/负向提示词、修正指令表、色板、量化自检清单
        └── stability.md             稳定性规范：正面替代写法、量化验收、稳定版短提示词（"不稳定"时必读）
```

## 更新记录

- **v1.5.0** 新增稳定性规范：规则优先级（不画眼睛 > 硬切面 > 背景柔边渐变）、眼睛的"正面替代"写法（眼窝 = 一块浅色平面，色块数 1、线条数 0）、量化验收、稳定版短提示词；背景排除水彩晕染与脏点；重试上限 2 次，同一失败模式连续 2 次即切换稳定版 + 双参考图
- **v1.4.0** 拆分「人物硬 / 背景柔」两条独立规则；新增从简原则（珠串、纹样、衣褶不得精细刻画）
- **v1.3.0** 眼睛改为强制完全不画；背景由硬边色块改为柔边渐变
- **v1.2.0** 引入背景分层与主次关系硬指标
- **v1.1.0** 增加风格锚点、色板、自检清单；修正墨线适用范围
- **v1.0.0** 首版

## 支持作者

如果这个技能帮你省下了反复调提示词的时间，欢迎扫码请我喝杯咖啡 ☕

<p align="center">
  <img src="docs/support.png" width="300" alt="赞赏码">
</p>

## 免责声明（重要）

- 本项目是**非官方的粉丝向提示词工程实践**，与《无悔华夏》及其开发商、发行商**没有任何关联**，未获其授权或认可。
- 仓库内**不包含任何游戏官方素材、截图或美术资源**，全部内容为原创的提示词、规则与文档。
- `docs/original.png` 为演示用第三方人像照片，`docs/result.jpg` 为使用本技能生成的风格化结果，仅作效果展示；如权利人提出异议将立即移除。
- 「无悔华夏」等名称与相关商标归其权利人所有，本项目仅作**描述性引用**。
- 请勿使用本技能生成侵犯他人版权、肖像权的内容；生成结果的合规性由使用者自行负责。

## 许可

本仓库的提示词与文档以 [MIT License](LICENSE) 发布。
