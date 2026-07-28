# Taste Skill 使用文档

> Taste Skill 是一套「反 AI 套路（Anti-Slop）」的前端设计技能框架，让 AI 编码助手生成的界面不再像模板。
> 官方仓库：<https://github.com/Leonxlnx/taste-skill> ｜ 文档站：<https://tasteskill.dev>
> 作者：Leonxlnx ｜ 许可证：MIT

---

## 一、Taste Skill 是什么

Taste Skill 是一组**可移植的 Agent Skills（`SKILL.md` 指令文件）**，用于提升 AI 生成界面的布局、排版、动效和间距质量，避免千篇一律的模板感。

它和 Impeccable 的定位相似但路线不同：

| | Impeccable | Taste Skill |
| --- | --- | --- |
| 形态 | 1 个技能 + 23 个斜杠命令 + CLI 检测器 + 钩子 | 多个独立 `SKILL.md`（按风格/场景选装） |
| 交互 | `/impeccable <命令> <目标>` | 无命令，Agent 自动读取 `SKILL.md` |
| 确定性检查 | 60 条 CLI 检测规则（无需 LLM） | 依赖 LLM 遵守规则 + 强制 pre-flight 自检清单 |
| 额外能力 | 浏览器实时迭代（live 模式） | 内置**图片生成技能**（生成参考稿再交给编码 Agent） |
| 许可 | Apache 2.0 | MIT |

**适用范围**：落地页（landing）、作品集（portfolio）、重设计（redesign）、编辑型/博客页面。
**不适用**：仪表盘、密集产品 UI、数据表格、多步表单、代码编辑器、原生移动端、实时协作 UI（详见「九、不适用场景」）。

---

## 二、快速开始

在项目根目录执行（安装核心技能 v2）：

```bash
npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"
```

这会往你的项目里添加一个 `SKILL.md` 文件。**Agent 会自动读取，无需任何额外配置，也没有斜杠命令**。

之后正常提需求即可：

```
帮我做一个 SaaS 落地页，Linear 风格的极简感，面向技术买家
```

Agent 会先输出一句「设计判读（Design Read）」，再开始写代码。

---

## 三、安装方式

### 1. 只装核心技能（推荐起点）

```bash
npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"
```

`--skill` 后面传的是**安装名（install name）**，即 `SKILL.md` frontmatter 里的 `name:` 字段，**不是文件夹名**。

### 2. 装全部技能

```bash
npx skills add https://github.com/Leonxlnx/taste-skill
```

`npx skills add` 会扫描仓库的 `skills/` 目录，所有技能（含图片生成技能）都用同样的方式安装。

### 3. 指定 Agent（如 Codex）

```bash
npx skills add Leonxlnx/taste-skill -a codex
```

### 4. 固定使用旧版 v1

```bash
npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend-v1"
```

### 5. 手动方式

直接把任意 `SKILL.md` 复制进项目，或粘贴到 ChatGPT / Codex 对话里。也可以：

```bash
git clone https://github.com/Leonxlnx/taste-skill
```

### 从 v1 升级到 v2

安装名 `design-taste-frontend` 未变，重跑安装命令即可就地覆盖旧文件：

```bash
npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"
```

> **版本状态提示**：默认安装的是 **v2（experimental）**。官方推荐 v2（明显优于 v1），但仍在迭代，v2.0.0 stable 之前章节编号和规则措辞可能变化。安装名保持稳定。

---

## 四、技能清单

### 实现类技能（输出代码）

| 技能文件夹 | 安装名 | 说明 |
| --- | --- | --- |
| **taste-skill** | `design-taste-frontend` | **v2（实验版，默认）**。读需求 → 推断设计语言 → 调三个档位（VARIANCE / MOTION / DENSITY）。含需求判读、设计系统映射、破折号全面禁用、GSAP 标准代码骨架、重设计审计协议、强制 pre-flight 自检。 |
| **taste-skill-v1** | `design-taste-frontend-v1` | 原版 v1，为依赖其确切行为的项目保留。仅在 v2 破坏了你的特定流程时使用。 |
| **gpt-tasteskill** | `gpt-taste` | 面向 GPT / Codex 的更严格变体：更高布局差异度、更强 GSAP 动效指引、更激进的反套路。 |
| **image-to-code-skill** | `image-to-code` | 图片优先流水线：先生成参考图 → 分析 → 按图实现前端。 |
| **redesign-skill** | `redesign-existing-projects` | 已有项目：先做六大类站点审计，再修布局、间距、层级、样式。 |
| **soft-skill** | `high-end-visual-design` | 精致、克制、高级感 UI：更柔和的对比、大量留白、高级字体、弹簧动效。 |
| **output-skill** | `full-output-enforcement` | 当模型交半成品时用：强制完整输出，禁止占位注释和跳过章节。 |
| **minimalist-skill** | `minimalist-ui` | 编辑型产品 UI（Notion / Linear 气质），克制配色、清晰结构。 |
| **brutalist-skill** | `industrial-brutalist-ui` | 硬派机械语言：瑞士式排版、强对比、实验性布局。 |
| **stitch-skill** | `stitch-design-taste` | 兼容 Google Stitch 的规则，可选导出 `DESIGN.md` 格式。 |

### 图片生成类技能（只输出图片，不出代码）

配合 ChatGPT Images、Codex 图片模式或任意能生图的 Agent 使用。

| 技能文件夹 | 安装名 | 说明 |
| --- | --- | --- |
| **imagegen-frontend-web** | `imagegen-frontend-web` | 网站视觉稿：hero、落地页、多板块，强排版与间距，带反套路美术指导。 |
| **imagegen-frontend-mobile** | `imagegen-frontend-mobile` | 移动端页面与流程：iOS / Android / 跨平台原型稿，字号可读、成套一致。 |
| **brandkit** | `brandkit` | 品牌套件板：Logo 方向、配色、字体、跨场景识别应用。 |

### 该选哪个

- 一般情况从 **taste-skill** 开始，最安全的通用默认。
- 依赖原版行为 → **taste-skill-v1**。
- 想要更严格的 GPT / Codex 规则 → **gpt-taste**。
- 图片 → 分析 → 代码的工作流 → **image-to-code-skill**。
- 改造已有代码库而非从零 → **redesign-skill**。
- **视觉方向已确定** → 叠加 **soft-skill** / **minimalist-skill** / **brutalist-skill**。
- Agent 老是截断输出 → 叠加 **output-skill**。
- 交付物是**图片**（视觉稿、流程图、品牌板）→ 用 imagegen 系列，再把结果喂给编码 Agent。

> **图片优先小技巧**：使用 `image-to-code-skill` 时，在提示里明确流水线，例如：
> `follow the skill: generate images, then analyze, then code`

---

## 五、三个核心档位（Dials）

`taste-skill` 的核心配置是三个 1-10 的档位，写在 `SKILL.md` 顶部。所有布局、动效、密度决策都受它们控制。

| 档位 | 含义 | 1 端 | 10 端 |
| --- | --- | --- | --- |
| `DESIGN_VARIANCE` | 布局实验程度 | 完全对称、居中、干净 | 不对称、艺术化混乱 |
| `MOTION_INTENSITY` | 动效深度 | 静态、仅 hover | 电影感 / 物理引擎、滚动与磁吸 |
| `VISUAL_DENSITY` | 每屏信息量 | 美术馆式、极空旷 | 驾驶舱式、密集数据 |

**基线值：`8 / 6 / 4`**，除非需求判读要求覆盖。

### 档位推断表（需求信号 → 档位）

| 需求信号 | VARIANCE | MOTION | DENSITY |
| --- | --- | --- | --- |
| 极简 / 干净 / 平静 / 编辑型 / Linear 风 | 5-6 | 3-4 | 2-3 |
| 高端消费品 / Apple 感 / 奢侈 / 品牌 | 7-8 | 5-7 | 3-4 |
| 好玩 / 狂野 / Dribbble / Awwwards / 实验 / 创意机构 | 9-10 | 8-10 | 3-4 |
| 落地页 / 作品集 / 营销站（默认） | 7-9 | 6-8 | 3-5 |
| 信任优先 / 公共部门 / 强监管 / 无障碍关键 | 3-4 | 2-3 | 4-5 |
| 重设计 - 保留品牌 | 匹配现状 | +1 | 匹配现状 |
| 重设计 - 彻底翻新 | +2 | +2 | 匹配现状 |

### 场景预设

| 场景 | VARIANCE | MOTION | DENSITY |
| --- | --- | --- | --- |
| 落地页（主流 SaaS） | 7 | 6 | 4 |
| 落地页（创意机构） | 9 | 8 | 3 |
| 落地页（高端消费品） | 7 | 6 | 3 |
| 作品集（设计师 / 工作室） | 8 | 7 | 3 |
| 作品集（开发者） | 6 | 5 | 4 |
| 编辑型 / 博客 | 6 | 4 | 3 |
| 公共部门服务 | 3 | 2 | 5 |

> **重要**：不需要手动改文件里的数字。**档位通过对话覆盖即可**，比如直接说「动效再收一点，MOTION 降到 3」。

---

## 六、v2 的工作流程（SKILL.md 章节结构）

v2 的核心变化是**先读需求再动手**。整个 `SKILL.md` 分 15 个章节，Agent 按序执行：

| 章节 | 名称 | 作用 |
| --- | --- | --- |
| 0 | Brief Inference | 生成前先「读懂现场」 |
| 1 | The Three Dials | 设定三个档位 |
| 2 | Brief → Design System Map | 需求映射到真实设计系统 |
| 3 | Default Architecture | 默认技术栈与约定 |
| 4 | Design Engineering Directives | 偏差纠正 + 三把锁 |
| 5 | Context-Aware Proactivity | 动效标准代码骨架 |
| 6 | Performance & A11y Guardrails | 性能与无障碍护栏 |
| 7 | Dial Definitions | 档位技术参考 |
| 8 | Dark Mode Protocol | 暗色模式协议 |
| 9 | AI Tells (Forbidden Patterns) | 反套路禁用清单 |
| 10 | Reference Vocabulary | Agent 应掌握的模式名词 |
| 11 | Redesign Protocol | 重设计协议（审计优先） |
| 12 | The Block Library | 区块库 schema |
| 13 | Out of Scope | 不适用场景 |
| 14 | Final Pre-Flight Check | 强制上线前自检 |

### 0. 需求判读（Brief Inference）

Agent 在写代码前先读六类信号：

1. **页面类型** —— 落地页（SaaS / 消费 / 机构 / 活动）、作品集（开发者 / 设计师 / 创意工作室）、重设计（保留 vs 翻新）、编辑/博客。
2. **气质词** —— "极简"、"平静"、"Linear 风"、"Awwwards"、"粗野主义"、"高端消费"、"Apple 感"、"严肃 B2B"、"编辑型"、"玻璃感"、"暗色科技"。
3. **参考信号** —— 你贴的 URL、截图、点名的产品、竞品品牌。
4. **受众** —— B2B 采购决策组 vs 有设计意识的消费者 vs 快速扫作品集的招聘方。**受众决定审美，而不是 Agent 的个人品味**。
5. **已有品牌资产** —— Logo、色彩、字体、摄影。重设计时这些是起始素材，不是可选输入。
6. **隐性约束** —— 无障碍优先、公共部门、强监管行业、信任优先的电商、儿童产品。**这些约束优先于审美偏好**。

然后输出一句「设计判读」再开始：

```
Reading this as: B2B SaaS landing for technical buyers,
with a Linear-style minimalist language,
leaning toward Tailwind utilities + Geist + restrained motion.
```

如果需求确实模糊，Agent **只问一个问题**（不会连环追问），例如「这个更接近 Linear 式干净，还是 Awwwards 式实验？」。能从上下文推断出来就不问。

### 2. 需求 → 设计系统映射

如果需求读起来就是某个成熟设计系统，Agent 会**装官方包**，而不是手搓 CSS：

| 需求读起来像 | 采用 |
| --- | --- |
| 微软 / 企业级 SaaS / 仪表盘 | `@fluentui/react-components` |
| Google 风格、Material 味产品 | `@material/web` + Material 3 tokens |
| IBM 风格 B2B / 企业分析 | `@carbon/react` + `@carbon/styles` |
| Shopify 应用界面 | Polaris |
| Atlassian / Jira 风格产品 | `@atlaskit/*` + `@atlaskit/tokens` |
| GitHub 风格开发者工具 / 社区页 | `@primer/css` 或 `@primer/react-brand` |
| 英国公共部门服务 | `govuk-frontend` |
| 美国公共部门 / 信任优先 | `uswds` |
| 快速本地商户 / 机构 MVP | Bootstrap 5.3 |
| 现代无障碍 React 基座 | `@radix-ui/themes` |
| 现代 SaaS 且要自己掌控组件 | shadcn/ui |
| Tailwind 系现代 SaaS / AI 营销站 | Tailwind v4 + `dark:` |

两条硬规则：
- **诚实规则**：命中上表就用**官方包**，不要手工复刻它的 CSS，也不要导入了 tokens 却覆盖 90%。
- **一个项目一个系统**：不要在同一棵树里混用 Fluent 和 Carbon，不要把 shadcn/ui 塞进 Material 3 应用。

如果需求只是一种**美学**（玻璃拟态、Bento、粗野主义、编辑型、暗色科技、极光渐变、动态排版、Apple Liquid Glass），则用原生 CSS + Tailwind 实现，并**诚实标注这是网页近似方案**而非官方material。

### 3. 默认技术栈

- **框架**：React / Next.js，默认 Server Components。用 Motion、滚动监听、指针物理的组件必须是带 `'use client'` 的隔离叶子节点。
- **样式**：Tailwind v4（v4 下用 `@tailwindcss/postcss` 或 Vite 插件，不要在 `postcss.config.js` 里用 `tailwindcss` 插件）。
- **动画**：Motion（原 Framer Motion），从 `motion/react` 导入。
- **字体**：用 `next/font` 或自托管 `@font-face` + `font-display: swap`。生产环境**不要**用 link 引 Google Fonts。
- **状态**：绝不用 `useState` 跟踪连续值（鼠标位置、滚动进度、磁吸悬停），要用 Motion 的 `useMotionValue` / `useTransform` / `useScroll`。

### 4. 三把锁（The Locks）

Agent 永不放松的三条规则，保证整页视觉连贯：

- **色彩一致锁**：整页**一个强调色**。暖灰色调的站点不会在第 7 屏突然冒出一个蓝色 CTA。
- **形状一致锁**：整页**一套圆角体系**（全直角、全圆角或全药丸形），混用需有明确记录的例外。
- **页面主题锁**：明暗模式在**页面级别选一次**（light / dark / auto），不允许中途翻转成另一种主题。

### 8. 暗色模式协议

默认双模式。两套主题都要满足 WCAG AA 对比度、层级对等、品牌保真。用**近黑与近白**，绝不用纯黑纯白。

### 11. 重设计协议

先判断模式（**误判模式是重设计输出变差的最大原因**）：

- **Greenfield** —— 无既有站点，或已批准彻底翻新。
- **Redesign - Preserve** —— 现代化但不破坏品牌。先审计，抽取品牌 tokens，渐进演化。
- **Redesign - Overhaul** —— 在既有内容上换新视觉语言，视觉按 greenfield 处理，但保留内容与信息架构。

**动手前先审计**：品牌 tokens、信息架构、内容区块、要保留的模式、要淘汰的模式、既有站点的档位读数、**SEO 基线（重设计第一大风险）**。

**现代化手段按风险从低到高**：① 排版更新（单位风险视觉提升最大）→ ② 间距与节奏 → ③ 色彩重校准 → ④ 动效层 → ⑤ Hero 与关键板块重构 → ⑥ 整块替换（仅在无法挽救时）。

**绝不静默变更**：URL 结构 / 路由 slug、主导航文案、表单字段名与顺序（会破坏埋点和自动填充）、品牌 Logo、既有法务/同意/Cookie 文案。

---

## 七、反套路禁用清单（Anti-Slop Ban System）

`SKILL.md` 第 9 章是一份严格的禁用清单，Agent 每次生成都要执行。以下是官方列出的主要项：

| 被禁模式 | 说明 |
| --- | --- |
| **破折号 `—` / `–`** | 输出文案中**全面禁用**：标题、眉标、药丸标签、正文、引用、署名、图注、按钮文字、alt 文本。改用连字符或重组句子。 |
| **编号式眉标** | `00 / INDEX`、`001 · Capabilities`、`06 · how it works`。眉标应该用平实语言点明主题。 |
| **Hero 版本标签** | `V0.6`、`BETA`、`INVITE-ONLY`，除非需求明确是产品发布。 |
| **把图注当装饰** | 库图下面写 `Field study no. 12 · Ines Caetano`。只允许对真实摄影师的真实署名。 |
| **Hero 底部装饰文字条** | `BRAND. MOTION. SPATIAL.`、`TYPE / FORM / MOTION` 这类等宽大写字条。 |
| **图片上叠药丸标签** | 不要在照片上浮标签。需要说明就放在图片正下方作图注。 |
| **营销页版本页脚** | `v1.4.2`、`Build 0048`、`last sync 4s ago`。开发工具的装置不属于落地页/作品集。 |
| **地区/城市/时间/天气条** | `Lisbon 14:23 · 18°C` 这类氛围条，99% 的需求都不该有。 |
| **滚动提示** | `Scroll`、向下箭头、`Scroll to explore`。用户在 hero，他们知道怎么滚。 |
| **装饰性状态点** | 默认为零。只在表达真实语义状态时使用，且每个板块最多一个。 |
| **每行都 `border-t` + `border-b`** | 长列表和规格表禁用。改用卡片、Tab、scroll-snap 药丸、跑马灯或轮播。 |
| **div 拼的假产品界面** | 不要用样式化 div 搭假任务列表、假终端、假仪表盘。用真截图或生成图。 |
| **三张等宽特性卡** | 默认禁用。改用双列 zig-zag、非对称网格或滚动固定方案。 |
| **AI 紫渐变 / 网格斑点渐变** | 默认禁用。中性底 + 一个高对比强调色。仅当品牌本身明确是紫色时（LILA 规则）才覆盖。 |
| **手搓装饰性 SVG 插画** | 强烈不建议作为默认。仅限简单几何标记，或需求明确要求。 |
| **`window.addEventListener('scroll')`** | JS 中禁用。改用 Motion `useScroll`、GSAP ScrollTrigger、IntersectionObserver 或 CSS 滚动驱动动画。 |

### Hero 纪律

首屏的硬性规则，**hero 必须在初始视口内装得下**：

- **标题**：桌面端最多两行。
- **副文案**：最多 20 词且最多 4 行。
- **主 CTA**：无需滚动即可见。
- **导航**：桌面端单行，高度不超过 80px。
- **顶部内边距**：桌面端最多 `pt-24`，hero 内容不能飘到视口中间。
- **元素堆叠**：hero 最多 4 个文本元素（眉标或品牌条、标题、副文案、CTA）。CTA 下面不再加小标语，hero 内不放信任微条。

---

## 八、上线前强制自检（Pre-Flight Check）

第 14 章是最后一道过滤器：**每个复选框都必须诚实通过，否则输出不算完成**。部分关键项：

```
[ ] 是否声明了需求判读一句话（0.B）？
[ ] 档位值是否显式给出并有理由，而非默默用基线？
[ ] 若适用，是否从第 2 章选了设计系统，或诚实标注了美学近似？
[ ] 若适用，是否识别了重设计模式并完成审计（第 11 章）？
[ ] 全页零破折号（—）？标题/眉标/药丸/正文/引用/署名/图注/按钮/alt 全部为零，不可协商
[ ] 页面主题锁：整页一个主题，没有板块中途翻转成反色？
[ ] 色彩一致锁：一个强调色在所有板块一致使用？
[ ] 形状一致锁：一套圆角体系一致应用？
[ ] 按钮对比度：每个 CTA 文字在其背景上可读（WCAG AA 4.5:1，无白底白字）？
[ ] CTA 文案在桌面端不折行到两行？
[ ] 表单对比度：输入框、占位符、聚焦环、标签都过 WCAG AA？
[ ] 衬线体纪律：若用衬线，不是 Fraunces 或 Instrument_Serif（除非有明确品牌理由）？
[ ] Hero 装得下视口：标题 ≤2 行，副文案 ≤20 词且 ≤4 行，CTA 无需滚动可见？
[ ] 眉标数量（机械计数）：uppercase tracking 微标签总数 ≤ ceil(板块数 / 3)？
[ ] 禁止「左侧大标题 + 右侧小解释段」的板块头模式？
[ ] Zigzag 交替上限：不允许 3 个以上连续板块用同一种图文分栏布局？
[ ] 无重复意图 CTA：同页不能既有「Get in touch」又有「Let's talk」？
[ ] Logo 墙只放 Logo：Logo 下面不印行业/品类标签？
[ ] Bento 背景多样性：至少 2-3 个格子有真实视觉变化，不是全白底文字卡？
[ ] 「Used by / Trusted by」Logo 墙放在 hero 之下（不在 hero 内），且用真实 SVG Logo？
[ ] 文案自审：每条可见文字重读过，没有语法错误或 AI 幻觉短语？
[ ] 动效有动机：每个动画都能用一句话说明理由（层级/叙事/反馈/状态转换），没有炫技式 GSAP？
[ ] 跑马灯每页最多一个？
[ ] 导航桌面端单行，高度 ≤80px？
[ ] 板块布局不重复：8 个板块里至少 4 种不同布局家族？
[ ] Bento 有节奏且格子数精确（N 项 → N 格，中间和末尾无空格）？
[ ] 长列表用对了 UI 组件（>5 项不要默认 divide-y 列表）？
[ ] 用了真实图片（生图工具优先，其次 Picsum seed，再次显式占位槽）？
```

完整清单见 `SKILL.md` 第 14 章。

---

## 九、不适用场景（Out of Scope）

这个技能**不适用于**以下场景，遇到时 Agent 应明确说明并指向正确工具：

| 场景 | 应该用 |
| --- | --- |
| 仪表盘 / 密集产品 UI / 管理后台 | Fluent、Carbon、Atlassian、Polaris |
| 数据表格 | TanStack Table 或 AG Grid |
| 多步表单 / 向导 | 专门的表单模式，此技能帮不上 |
| 代码编辑器 | Monaco / CodeMirror 及其官方皮肤方案 |
| 原生移动端 | 直接用 Apple HIG / Material |
| 实时协作 UI（在线状态、多人光标） | 另一类问题，不在范围内 |

如果需求属于上述之一，只把此技能中营销页 / 关于页 / 落地页相关的部分应用到适用的界面上。

---

## 十、兼容的 AI 工具

任何支持 `SKILL.md` 的 Agent 都可用：Cursor、Claude Code、Codex、Antigravity、Gemini CLI、AI Studio、v0、Lovable、OpenCode、OpenClaw、Windsurf、GitHub Copilot。

规则针对**设计意图**而非某个框架 API，因此 React、Vue、Svelte 都适用。

---

## 十一、自定义

`SKILL.md` **完全可编辑**。在项目根目录打开它，增删改任意规则以匹配你的设计方向。

- Agent **每次运行都会重读该文件**，改动在下一次生成时生效。
- 如果你维护了项目专属的风格指南，**把它粘贴到 `SKILL.md` 顶部**，Agent 会把它当作最高优先级的事实来源。

---

## 十二、完整实战示例：做一个 SaaS 落地页

**场景设定**：给团队协作工具 **"Pulse"** 做营销落地页，技术栈 React + Tailwind，AI 工具用 Cursor。

### 第 1 步：安装

```bash
npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"
```

输出大致如下：

```
✔ Fetching skills from Leonxlnx/taste-skill
✔ Installed design-taste-frontend → .cursor/skills/design-taste-frontend/SKILL.md
```

没有别的配置步骤，Agent 会自动读取。

### 第 2 步（可选）：叠加风格技能

已经确定要极简编辑风，再装一个：

```bash
npx skills add https://github.com/Leonxlnx/taste-skill --skill "minimalist-ui"
```

### 第 3 步：直接提需求（不需要命令）

在 Cursor 聊天里写清楚**页面类型 + 气质词 + 受众 + 参考**，这是决定输出质量的关键：

```
帮我做 Pulse 的落地页。Pulse 是团队协作工具，把零散的进度更新汇成一条实时时间线。
受众：中小团队的项目经理，25-40 岁。
气质：Linear 式极简、专业、可信，不要花哨。
参考：linear.app 的克制感，但配色用深墨绿 + 暖白 + 一点琥珀。
板块：hero、客户 Logo 墙、三段核心价值（不要三张等宽卡）、真实产品截图、价格、FAQ、页脚 CTA。
文案我给：
- 主标题：让团队真正看得见计划
- 副标题：Pulse 把零散更新汇成一条实时时间线
- 主 CTA：生成你的第一条时间线
```

### 第 4 步：Agent 输出设计判读

它会先给出一行判读，而不是直接堆代码：

```
Reading this as: B2B SaaS landing for project managers,
with a Linear-style minimalist language,
leaning toward Tailwind v4 + Geist + restrained motion.

Dials: DESIGN_VARIANCE 6 / MOTION_INTENSITY 3 / VISUAL_DENSITY 3
（依据「极简 / Linear 风」信号，从基线 8/6/4 下调）
```

### 第 5 步：对话式调整档位

不要去改文件，直接说：

```
动效再多一点，MOTION 提到 5，滚动进场可以有，但不要磁吸效果
```

### 第 6 步：Agent 生成代码（禁用规则在生效）

被禁的写法它不会产出。例如**不会**出现这些：

```jsx
// 这些都会被禁用规则拦住
<section className="bg-gradient-to-br from-purple-600 to-blue-500">   {/* AI 紫渐变 */}
  <span className="uppercase tracking-widest">001 · Capabilities</span> {/* 编号式眉标 */}
  <h1>Ship faster — see everything</h1>                                 {/* 破折号 */}
  <div className="text-xs">Scroll to explore ↓</div>                    {/* 滚动提示 */}
</section>
<div className="grid grid-cols-3">...</div>                             {/* 三张等宽卡 */}
```

实际产出更接近：

```jsx
<section className="bg-[#FAF7F0] pt-24 pb-32">
  <h1 className="font-[Geist] text-[#14352B] text-6xl leading-[1.05] max-w-[18ch]">
    让团队真正看得见计划
  </h1>
  <p className="mt-6 max-w-xl text-[#3A4A43]">
    Pulse 把零散更新汇成一条实时时间线
  </p>
  <a className="mt-10 inline-flex rounded-full bg-[#14352B] px-6 py-3 text-[#FAF7F0]">
    生成你的第一条时间线
  </a>
</section>
{/* 核心价值用双列 zig-zag，不用三等分卡 */}
```

### 第 7 步：让它跑 pre-flight 自检

```
按 SKILL.md 第 14 章跑一遍 pre-flight check，逐条报告结果
```

它会逐项核对（零破折号、三把锁、hero 是否装得下视口、眉标数量是否 ≤ ceil(板块数/3)、布局家族是否够 4 种……）并修掉不过的项。

### 改造已有项目的变体流程

```bash
npx skills add https://github.com/Leonxlnx/taste-skill --skill "redesign-existing-projects"
```

然后：

```
审计现有站点后再改：保留品牌和信息架构，只做视觉现代化。
不要动 URL 结构、导航文案和表单字段名。
```

Agent 会先出审计报告（品牌 tokens、信息架构、要保留/淘汰的模式、SEO 基线、现状档位读数），再按「排版 → 间距 → 色彩 → 动效 → Hero 重构」的低风险顺序推进。

### 图片优先流程（先出稿再写码）

```bash
npx skills add https://github.com/Leonxlnx/taste-skill --skill "image-to-code"
```

提示里明确流水线：

```
follow the skill: generate images, then analyze, then code
先出 3 个 hero 方向的参考稿，我选一个，然后再实现
```

---

## 十三、需求和文案该写在哪

Taste Skill **没有命令，也没有 `PRODUCT.md` 之类的上下文文件**，所有信息都在两个地方：

| 你想描述的东西 | 放在哪 |
| --- | --- |
| 页面类型、气质、受众、参考、配色方向 | **每次对话的提示里**（Agent 用它做「需求判读」） |
| 布局激进度、动效强度、信息密度 | 对话中说明，或让 Agent 推断；也可直接改 `SKILL.md` 顶部档位 |
| 项目长期风格约定、设计系统 tokens | **粘贴到 `SKILL.md` 顶部**，成为最高优先级事实来源 |
| 具体一字一句的文案 | 直接写在提示里；Agent 会做「文案自审」但不替你创作业务文案 |

因为没有持久化的项目上下文文件，**如果你希望约定长期生效，务必写进 `SKILL.md`**，否则每次新对话都要重复交代。

---

## 十四、常见问题

**和其他 AI 设计技能有什么不同？**
多个专用变体、关键技能里可调档位、基于专门研究的反重复规则，且跨主流编码 Agent 框架无关。

**支持 React / Vue / Svelte 吗？**
支持。规则针对设计意图，而非单一框架 API。

**`SKILL.md` 是什么？**
一种可移植的指令文件，Agent 能自动加载。通过 `npx skills add` 安装，或直接复制进仓库/粘贴到对话。

**图片生成技能也能用 `npx skills add` 装吗？**
可以。它们和代码技能一起放在 `skills/` 下，同一个 CLI 就能发现。

**关于代币的声明**：Taste Skill **没有**任何官方 token、币或加密项目。任何借用作者名义的代币均与本项目无关。

---

## 十五、资源与反馈

- GitHub 仓库：<https://github.com/Leonxlnx/taste-skill>
- 官方文档站：<https://tasteskill.dev>
- 变更日志：仓库内 `CHANGELOG.md`
- 背景研究材料：仓库内 `research/`
- 反馈渠道：GitHub Issue / PR、X 上的 [@lexnlin](https://x.com/lexnlin) 与 [@blueemi99](https://x.com/blueemi99)、邮箱 <hello@tasteskill.dev>
- 赞助：<https://github.com/sponsors/Leonxlnx>

---

*本文档整理自 Taste Skill 官方 README、文档站与 `SKILL.md` v2（<https://github.com/Leonxlnx/taste-skill>），由 AI 生成，仅供参考。v2 仍在迭代，规则措辞可能变化，以官方最新文档为准。*
