# Impeccable 使用文档

> Impeccable 是一套「让 AI 编码助手更擅长做设计」的设计语言（design language）。
> 官方地址：<https://github.com/pbakaus/impeccable> ｜ 文档站：<https://impeccable.style>
> 作者：Paul Bakaus ｜ 许可证：Apache 2.0

---

## 一、Impeccable 是什么

Impeccable 是给 AI 编码 Agent（如 Cursor、Claude Code、Codex 等）使用的**前端设计指导规范**。它的核心目标是解决一个普遍问题：

> 几乎所有大模型都在相同的 SaaS 模板上训练，如果不加设计约束，AI 生成的界面永远是同一套「AI 味」：全站 Inter 字体、紫到蓝的渐变、卡片套卡片、彩色背景上的灰字、每个标题上方都有一个圆角方形图标块。

Impeccable 通过一套「技能 + 命令 + 检测规则」的组合，给 AI 注入设计品味和明确的规范约束。

它包含三大部分：

1. **1 个技能（Skill）**：`impeccable`，统一入口 `/impeccable`。
2. **23 个命令（Commands）**：与 AI 共享的设计词汇，如 `polish`、`audit`、`critique`、`animate` 等。
3. **60 条确定性检测规则（Detector Rules）**：无需 LLM、无需 API Key，CLI 和浏览器扩展即可运行，用于识别「AI slop（AI 生成的设计陋习）」。

---

## 二、快速开始

在你的项目根目录执行：

```bash
# 1. 安装 impeccable
npx impeccable install

# 2. 在你的 AI 编码工具里运行初始化
/impeccable init
```

- `npx impeccable install`：检测你使用的 AI 工具（如 `.claude`、`.codex`、`.cursor`），安装技能与钩子。
- `/impeccable init`：一次性设置，收集设计上下文，生成 `PRODUCT.md` 和 `DESIGN.md`，配置实时模式，并推荐后续步骤。

`init` 会先询问当前界面属于哪个类型：

- **brand（品牌）**：营销页、落地页、作品集。
- **product（产品）**：应用 UI、仪表盘、工具。

随后写入设计上下文（受众、品牌/产品定位、语气、反面参考、配色、字体、组件），供后续所有命令读取。

---

## 三、安装方式（5 种）

### 方式 1：CLI 安装器（推荐）

```bash
npx impeccable install
```

它会：
- 显示检测到的工具目录（如 `~/.claude`、`~/.codex`、`~/.grok`、项目内 `.cursor`）；
- 让你保留检测集或自定义 providers；
- 询问安装到当前项目还是全局。

脚本化时可跳过交互：

```bash
npx impeccable install --providers=claude,codex,cursor,grok --scope=project
```

- `--scope` 取值：`project` | `global`
- 在 Claude Code、Cursor、Codex、GitHub Copilot、Grok Build 上，还会安装各工具原生的钩子清单。
- 安装完成后需重载你的 AI 工具。

刷新已有安装：

```bash
npx impeccable update
```

> 注意：
> - Codex 用户在安装/更新后需打开 `/hooks` 并批准项目钩子（Codex 按钩子定义追踪信任，改动 `.codex/hooks.json` 后需重新批准）。
> - Grok Build 用户需要先信任项目目录（`/hooks-trust` 或以 `--trust` 启动），`.grok/hooks/` 脚本才会运行。

### 方式 2：Git 子模块（适合团队 vendored 管理）

```bash
git submodule add https://github.com/pbakaus/impeccable .impeccable
npx impeccable link --source=.impeccable --providers=claude,cursor
git add .gitmodules .impeccable .claude .cursor
git commit -m "Add Impeccable skills"
```

可用 providers：`claude`、`cursor`、`gemini`、`codex`、`github`、`grok`、`opencode`、`pi`、`qoder`、`trae`、`trae-cn`、`rovo-dev`、`vibe`。

后续更新：

```bash
git submodule update --remote .impeccable
npx impeccable link --source=.impeccable --providers=claude,cursor
```

### 方式 3：插件安装

**Claude Code：**

```bash
/plugin marketplace add pbakaus/impeccable
```

> 仅限 Claude Code。添加市场后，打开 `/plugin` 从列表安装 Impeccable。

**Grok Build：**

```bash
grok plugin install pbakaus/impeccable#plugin --trust
```

> `#plugin` 后缀安装精简插件包（skills、agents、hooks），随后在 Grok 会话运行 `/impeccable init`。

### 方式 4：从官网下载

访问 <https://impeccable.style>，下载对应工具的 ZIP 并解压到项目。

### 方式 5：从仓库手动复制

**Cursor：**

```bash
cp -r dist/cursor/.cursor your-project/
```

> Cursor 需额外设置：
> 1. 在 Cursor Settings → Beta 切换到 Nightly 通道；
> 2. 在 Cursor Settings → Rules 启用 Agent Skills。

**Claude Code：**

```bash
# 项目级
cp -r dist/claude-code/.claude your-project/
# 或全局（对所有项目生效）
cp -r dist/claude-code/.claude/* ~/.claude/
```

其他工具（OpenCode、Pi、Gemini CLI、Codex CLI、GitHub Copilot、Trae、Rovo Dev、Qoder、Mistral Vibe、Grok Build）均有对应的 `cp -r dist/<tool>/...` 命令，用法类似。

---

## 四、23 个命令详解

所有命令都通过 `/impeccable` 调用。格式：

```
/impeccable <command> <target>
```

| 命令 | 作用 |
| --- | --- |
| `/impeccable craft` | 完整的「先塑形再构建」流程，带可视化迭代 |
| `/impeccable init` | 一次性设置：收集设计上下文，写 PRODUCT.md 和 DESIGN.md，配置实时模式，推荐后续步骤 |
| `/impeccable document` | 从现有项目代码生成根目录 DESIGN.md |
| `/impeccable extract` | 把可复用组件与 token 提取进设计系统 |
| `/impeccable shape` | 写代码前先规划 UX/UI |
| `/impeccable critique` | UX 设计评审：层级、清晰度、情感共鸣 |
| `/impeccable audit` | 运行技术质量检查（无障碍、性能、响应式） |
| `/impeccable polish` | 最终打磨、设计系统对齐、上线就绪度 |
| `/impeccable bolder` | 增强平淡的设计 |
| `/impeccable quieter` | 收敛过于张扬的设计 |
| `/impeccable distill` | 剥离到本质、去除复杂度 |
| `/impeccable harden` | 错误处理、国际化、文本溢出、边界情况 |
| `/impeccable onboard` | 首次运行流程、空状态、激活路径 |
| `/impeccable animate` | 添加有目的的动效 |
| `/impeccable colorize` | 引入有策略的色彩 |
| `/impeccable typeset` | 修正字体选择、层级、字号 |
| `/impeccable layout` | 修正布局、间距、视觉节奏 |
| `/impeccable delight` | 添加令人愉悦的细节 |
| `/impeccable overdrive` | 添加技术上极致的效果 |
| `/impeccable clarify` | 改进不清晰的 UX 文案 |
| `/impeccable adapt` | 适配不同设备 |
| `/impeccable optimize` | 性能优化 |
| `/impeccable live` | 可视化变体模式：在浏览器中迭代元素 |

### 命令使用示例

```
/impeccable audit 博客           # 审查博客首页 + 文章页
/impeccable critique 落地页       # UX 设计评审
/impeccable polish 设置页         # 上线前的最终打磨
/impeccable harden 结账页         # 添加错误处理 + 边界情况
```

也可以直接用 `/impeccable` 加自然语言描述：

```
/impeccable 重做这个 hero 区块
```

大多数命令都接受一个可选参数，用于聚焦特定区域：

```
/impeccable audit 页头
/impeccable polish 结账表单
```

### 固定（pin）常用命令

如果你经常用某个命令，可以把它固定成独立快捷方式：

```
/impeccable pin audit      # 之后可直接用 /audit
```

输入 `/impeccable`（不带参数）可查看完整命令列表。

> **Codex 特别说明**：Codex 用的是 skills 而非 `/prompts:` 命令。打开 `/skills` 或输入 `$impeccable`。仓库内安装位于 `.agents/skills/`，用户级安装位于 `~/.agents/skills/`。GitHub Copilot 用 `.github/skills/`。新安装的技能不显示时请重启工具。

---

## 五、反面模式（Anti-Patterns）

技能内置了明确的「不要做什么」指导：

- 不要用被滥用的字体（Arial、Inter、系统默认字体）；
- 不要在彩色背景上用灰色文字；
- 不要用纯黑/纯灰（始终带一点色调 tint）；
- 不要把所有东西都塞进卡片，或卡片里再套卡片；
- 不要用弹跳/弹性缓动（bounce/elastic，显得过时）。

---

## 六、独立 CLI（无需 AI 工具）

Impeccable 提供了一个独立 CLI，用于在没有 AI 工具的情况下检测反面模式：

```bash
npx impeccable detect src/                   # 扫描目录
npx impeccable detect index.html             # 扫描 HTML 文件
npx impeccable detect https://example.com    # 扫描 URL（用 Puppeteer）
npx impeccable detect --json .               # CI 友好的 JSON 输出
npx impeccable detect --no-config src/       # 原始扫描，忽略项目配置/上下文
```

管理检测忽略项：

```bash
npx impeccable ignores list                          # 查看检测忽略项
npx impeccable ignores add-file "src/legacy/**"
npx impeccable ignores add-value overused-font Inter --reason "Brand font"
```

检测器能识别 **60 类确定性问题**，涵盖：
- **AI slop**：侧边栏边框、紫色渐变、弹跳缓动、暗色辉光等；
- **通用设计质量**：行长、拥挤的内边距、过小的点击目标、跳级的标题层级等。

默认情况下，`detect` 会尊重 `.impeccable/config.json` 与 `.impeccable/config.local.json` 中的检测配置：`detector.ignoreRules`、`detector.ignoreFiles`、`detector.ignoreValues`、`detector.designSystem.enabled`。

**行内豁免**（只对单文件生效，随文件走而非项目配置）：

```html
<!-- impeccable-disable overused-font: exported brand doc -->
```

该标记支持任意注释语法，默认作用于整个文件；也可用 `impeccable-disable-line` / `impeccable-disable-next-line` 只作用于一行。`--no-inline-ignores` 或 `--no-config` 可绕过。

完整检测器文档：<https://impeccable.style/docs/detector>

---

## 七、设计钩子（Design Hook）

在 **Claude Code、GitHub Copilot、Codex、Cursor、Grok Build** 上，`npx impeccable install` 和 `npx impeccable update` 会随技能一起安装各工具原生的钩子清单。钩子会在 UI 文件被直接编辑时运行 Impeccable 检测器，并把结果反馈到 Agent 流程中。

- Claude Code、GitHub Copilot、Codex、Grok Build：编辑后反馈（在支持的地方 Stop 时做更深检查）。
- **Cursor：在坏的写入落地前直接拦截**。

各工具的钩子位置：

| 工具 | 钩子位置 | 运行脚本 |
| --- | --- | --- |
| Claude Code | `.claude/settings.local.json`（gitignore，机器本地） | `.claude/skills/impeccable/scripts/hook.mjs` |
| GitHub Copilot | `.github/hooks/impeccable.json`（提交共享） | `.github/skills/impeccable/scripts/hook.mjs` |
| Cursor | `.cursor/hooks.json` | `.cursor/skills/impeccable/scripts/hook-before-edit.mjs` |
| Codex | `.codex/hooks.json` | `.agents/skills/impeccable/scripts/hook.mjs` |

- 交互式 `install`/`update` 时会解释钩子并询问是否安装（默认是）。你的选择记录在 gitignore 的 `.impeccable/config.local.json`，之后不再询问；`--no-hooks` 只跳过本次而不记录。
- 钩子生命周期设置在 `.impeccable/config.json` 的 `hook` 键下；检测器忽略项在 `detector` 键下。
- 调试：在 `.impeccable/config.json` 设置 `hook.auditLog` 为一个路径（或用旧的 `IMPECCABLE_HOOK_LOG` 环境变量），会为每次钩子调用写一行 NDJSON。正常使用时保持不设置。
- **Codex 需要一步手动操作**：安装/更新后打开 `/hooks` 批准项目钩子。

完整钩子文档：<https://impeccable.style/docs/hooks>

---

## 八、把 `.impeccable` 排除出 Git

运行命令时，Impeccable 会在 `.impeccable/` 下写入工作文件：评审/打磨截图、实时模式会话与预览状态、运行时缓存、每位开发者的配置。大部分是临时的、不应提交；少数是共享的项目产物，应留在仓库里。

在 `.gitignore` 添加以下块：

```gitignore
# impeccable-ignore-start
.impeccable/config.local.json
.impeccable/hook.cache.json
.impeccable/hook.pending.json
.impeccable/*.png
.impeccable/live/server.json
.impeccable/live/sessions/
.impeccable/live/previews/
.impeccable/live/annotations/
.impeccable/live/cache/
.impeccable/live/manual-edit-apply-transaction.json
.impeccable/live/manual-edit-events.jsonl
.impeccable/live/manual-edit-evidence/
.impeccable/live/pending-manual-edits.json
.impeccable/live/deferred-svelte-component-accepts.json
.impeccable/live/*.png
# impeccable-ignore-end
```

**应保持被 Git 跟踪的文件**（共享项目产物，不要加入 `.gitignore`）：

- `.impeccable/config.json`（统一共享配置）
- `.impeccable/live/config.json`（实时模式框架接线）
- `.impeccable/design.json`（共享设计规范）
- `.impeccable/critique/*.md`（评审报告）

> 如果某个临时文件在你添加该块之前已被提交，`.gitignore` 不会自动取消跟踪。执行 `git rm --cached <path>` 停止跟踪但保留本地副本。

---

## 九、支持的工具

Cursor、Claude Code、GitHub Copilot、Gemini CLI、Codex CLI、Grok Build、OpenCode、Pi、Kiro、Trae、Rovo Dev、Qoder、Mistral Vibe。

---

## 十、典型工作流建议

一个从零到上线的常见流程：

```
1. /impeccable init          # 初始化设计上下文（PRODUCT.md / DESIGN.md）
2. /impeccable shape         # 写代码前规划 UX/UI
3. /impeccable craft         # 塑形并构建，带可视化迭代
4. /impeccable critique      # 设计评审（层级/清晰度/情感）
5. /impeccable audit         # 技术检查（a11y/性能/响应式）
6. /impeccable harden        # 补充错误处理与边界情况
7. /impeccable polish        # 上线前最终打磨
```

对已有项目：

```
/impeccable document         # 从现有代码生成 DESIGN.md
/impeccable extract          # 抽取组件与 token 进设计系统
npx impeccable detect src/   # 在 CI 中做确定性检测
```

---

## 十一、完整实战示例：用 Impeccable 做一个 SaaS 落地页

下面用一个**从零做落地页**的场景，把 Impeccable 从安装到上线的每一步串起来。

**场景设定**：给团队协作工具 **"Pulse"** 做营销落地页（landing page），技术栈 React + Tailwind，AI 工具用 Cursor。

### 第 0 步：安装

在项目根目录终端执行：

```bash
npx impeccable install
```

交互过程大致如下：

```
✔ Detected harness folders: .cursor
? Install providers: (●) cursor
? Scope: (●) project  ( ) global
? Install design hook? (Y/n) Y
✔ Installed skill to .cursor/skills/impeccable
✔ Installed hook to .cursor/hooks.json
→ Reload Cursor to activate.
```

重载 Cursor（并确认 Nightly 通道 + Settings → Rules 里启用了 Agent Skills）。

### 第 1 步：初始化设计上下文

在 Cursor 聊天里输入：

```
/impeccable init
```

AI 会开始提问，一次真实对话可能是：

```
Impeccable: 这个界面是 brand（营销/落地页/作品集）还是 product（应用/仪表盘）？
你: brand

Impeccable: 目标受众？
你: 中小团队的项目经理，25-40 岁

Impeccable: 品牌气质用三个词形容？
你: 专业、快、值得信赖

Impeccable: 有没有想避开的"反面参考"（不想像谁）？
你: 不要像又一个通用 SaaS 模板，避开紫蓝渐变

Impeccable: 主色倾向？
你: 深墨绿 + 暖白，点缀一点琥珀色
```

完成后，它会在项目里生成：

```
PRODUCT.md          # 产品定位、受众、语气
DESIGN.md           # 配色、字体、间距、组件规范
.impeccable/design.json
.impeccable/config.json
```

`DESIGN.md` 里可能写入类似的规范（AI 生成的具体内容）：

```markdown
## Colors
- Primary: #14352B (deep pine)   ← 不用纯黑，带色调
- Surface: #FAF7F0 (warm white)
- Accent:  #E0A458 (amber)
## Type
- Display: "Fraunces", serif     ← 避开 Inter
- Body: "Public Sans", sans-serif
## Motion
- Easing: cubic-bezier(0.2, 0, 0, 1)  ← 不用 bounce/elastic
```

### 第 2 步：先规划，再写代码

```
/impeccable shape hero 区块
```

AI 不直接写代码，而是先给出 UX/UI 方案：

```
Hero 结构建议：
1. 左对齐大标题（Fraunces 展示字体），一句话价值主张
2. 副标题：具体收益，而非口号
3. 单一主 CTA + 次级文字链接（避免两个同权重按钮）
4. 右侧放产品真实截图，而不是抽象插画
5. 背景用暖白，避免全屏渐变
确认后我再开始构建。
```

### 第 3 步：塑形并构建（带可视化迭代）

```
/impeccable craft
```

AI 开始写代码。**此时设计钩子在工作**——如果 AI 想写出反面模式，Cursor 会在写入前拦截。例如它本来打算写：

```jsx
// AI 初版尝试（被钩子拦截）
<section className="bg-gradient-to-r from-purple-500 to-blue-500">
  <h1 className="font-sans text-gray-400">Welcome to Pulse</h1>
```

钩子反馈：

```
⚠ impeccable detector 阻止了此次写入：
  - purple-to-blue gradient (AI slop)
  - gray text on colored background
  - overused font fallback
```

AI 据此改成：

```jsx
<section className="bg-[#FAF7F0]">
  <h1 className="font-[Fraunces] text-[#14352B] text-6xl leading-[1.05]">
    Ship faster when your team actually sees the plan
  </h1>
  <p className="text-[#3A4A43] mt-6 max-w-xl">
    Pulse turns scattered updates into one live timeline...
  </p>
```

### 第 4 步：设计评审

```
/impeccable critique 落地页
```

输出（保存到 `.impeccable/critique/landing.md`）：

```
UX 评审：
✅ 层级清晰，主标题 → 收益 → CTA 动线顺畅
⚠ 情感共鸣：hero 副本偏功能化，缺少一个能让 PM 共情的痛点钩子
⚠ CTA "Get Started" 太泛，建议改成 "See your first timeline"
⚠ 特性区三张卡片视觉重量一致，缺主次
```

### 第 5 步：技术审查

```
/impeccable audit
```

```
♿ a11y：CTA 对比度 3.8:1，未达 AA（需 ≥4.5），建议加深琥珀色
📱 响应式：hero 标题在 375px 下溢出，需要 clamp() 字号
⚡ 性能：产品截图 1.2MB 未压缩，建议转 WebP + lazy
```

### 第 6 步：加固边界情况

```
/impeccable harden 注册表单
```

AI 补充：空邮箱校验、超长文本溢出处理、加载/错误/成功三态、i18n 文案抽取。

### 第 7 步：上线前打磨

```
/impeccable polish
```

做最终的设计系统对齐（间距统一到 4px 网格、圆角统一、hover 态补全），并给出上线就绪度清单。

### （可选）CI 里做确定性检测

不依赖 AI，在 CI 流水线加一步：

```bash
npx impeccable detect src/ --json
```

任何人后续引入紫渐变、弹跳动效、过小点击目标，都会被卡住。

如果某个文件确实需要用被禁字体（比如导出的品牌文档），行内豁免：

```html
<!-- impeccable-disable overused-font: exported brand doc -->
```

### 一图流总结

```
安装            npx impeccable install
初始化          /impeccable init          → PRODUCT.md / DESIGN.md
规划            /impeccable shape
构建            /impeccable craft         ← 钩子实时拦截 AI slop
评审            /impeccable critique
技术审查        /impeccable audit
加固            /impeccable harden
打磨            /impeccable polish
持续守护(CI)    npx impeccable detect
```

---

## 十二、网站内容和文案该放在哪个命令

Impeccable 本质是**设计约束工具**，它不替你决定网站内容和逐字文案。"具体做成什么样、文案写什么"由你在不同环节以「自然语言描述 + 生成的上下文文件」提供，分三层：

### 1. 宏观定位（谁、什么调性）→ `/impeccable init`

`init` 把你的回答写进两个文件：

- **`PRODUCT.md`**：产品定位、目标受众、语气（voice）、反面参考。
- **`DESIGN.md`**：配色、字体、间距、组件规范。

这里存的是**方向和调性**（如"语气：专业、快、值得信赖"），**不是逐字文案**。后续所有命令都会读这两个文件，保证风格一致。

### 2. 页面具体长什么样（结构/板块）→ `/impeccable shape` 或 `/impeccable craft`

在这一步用自然语言把"页面要做成什么样、放哪些板块"告诉 AI：

```
/impeccable shape 一个落地页，包含 hero、三个特性卡片、价格表、FAQ、页脚 CTA
```

也可以不带子命令，直接描述：

```
/impeccable 帮我做一个 Pulse 的落地页，主打"团队进度一目了然"，要有实时时间线截图
```

### 3. 具体文案（一字一句）→ 你提供，`/impeccable clarify` 负责润色

Impeccable **不替你创作业务文案**。做法有两种：

- **你直接给**：在 `craft`/`shape` 的描述里把要用的标题、副标题、按钮文字写进去，AI 照着放。
- **`/impeccable clarify`**：唯一和文案相关的命令，作用是**改进不清晰的 UX 文案**（如把泛泛的 "Get Started" 改成更具体的表达），属于润色/优化，不是从零创作。

### 速查表

| 你想描述的东西 | 放在哪 |
| --- | --- |
| 受众、品牌调性、语气 | `/impeccable init` → `PRODUCT.md` |
| 页面结构、要哪些板块、整体长什么样 | `/impeccable shape` / `craft` 的自然语言描述 |
| 具体一字一句的文案 | 你在描述里直接给；`/impeccable clarify` 负责润色 |

---

## 十三、社区与资源

- GitHub Discussions：报 bug、提需求、帮助新人。
- npm 上的 Impeccable：获取 CLI、关注发布、star。
- Twitter `@pbakaus`：发布说明、示例 lint 报告、新规则视频。
- 文档站：<https://impeccable.style>
- 贡献指南见仓库 `DEVELOP.md`。

---

*本文档整理自 Impeccable 官方 README（<https://github.com/pbakaus/impeccable>），由 AI 生成，仅供参考。以官方最新文档为准。*
