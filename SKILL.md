---
name: ui-discipline
description: 在写或改任何界面样式时使用——新建页面或组件、重写 CSS、调整视觉规范、做 UI 评审、把设计参考落到代码里；**以及选择、安装、升级前端依赖库时**（图标 / 动画 / 状态管理 / 拖拽 / 布局分栏）。用于把「好不好看」的模糊判断降级成可机械检查的规则（裸色值数量、字阶档位、圆角档位、字号下限），把「优先复用成熟库、限制重复依赖」降级成具体的选型表与禁止清单，并拦住高频反模式（一指标一色相、彩色渐变图标、重阴影撑边界、一行 N 个按钮并列）。**半路接手、项目已有自己视觉规范时，先问一句「按原来的还是按本技能」，不要直接套用**（见「开工前」一节）。用户要求先探索视觉方向、做多方案对比、或明确说「先随便看看」时不适用——那一步不设约束。
agent_created: true
---

# 界面纪律

## 目的

界面「不好看」，九成不是审美问题，是**没有 token** 的问题。

扫过一个项目：`src/` 下 210 个不同色值，其中 3 套主蓝、2 套中性灰、3 套红。
而它 `:root` 里其实早就定义好了一套变量。210 个值不是 210 次审美决策，
是 210 次「随手写个值」——每一次都局部合理，合起来就散了。

所以真正要防的不是「配色难看」，是**随手**。

唯一目标：**在写下任何一个色值、字号、圆角之前，先问它有没有出处。**

## 开工前：先问一句按谁的标准

这个技能**不是无条件生效**的。半路接手的项目往往已经有一套自己的视觉规范，先确认按谁的来，再动手。

### 先判断有没有现行规范（这个不用问，看就行）

- `:root` 里有成体系的变量（`--color-*` / `--slate-*` 之类，超过 10 个）
- `package.json` 里有 UI 库或设计系统（Vben、Element Plus、Ant Design Vue、shadcn、MUI……）
- 有 `tokens.json` / 主题目录 / `tailwind.config.*` 里的 `theme.extend`
- 组件目录有明显约定（`components/ui/`、`components/base/`）

命中两条以上 → 视为「有现行规范」，**必须问**。

### 只问这一句，二选一

> 这个项目已经有一套视觉规范了，是按原来的来，还是按 skill 的规则来？

- **A. 按原有规范**——技能退化成「一致性检查」。只保留与框架无关的部分：裸值盘点、档位统计、重复依赖检查、反模式识别。**不补新 token、不改档位、不换库**。判据从「这个值有没有出处」换成「跟项目已有的那套是不是一回事」。
- **B. 按本技能**——先出存量盘点（现在几档裸值、几套色相、几个阴影）加迁移方案，**拿到确认再动手**。存量大的项目实际落地必然是**新老分区**：新写的按新规则，老的不动，等单独排期收口。不要一边定规则一边顺手改老页面。

选 B 也要说清代价：这是**重写视觉层**，不是修 bug。它会让改前改后的截图对不上，需要单独排期和验收。

### 什么时候不必问

- 全新项目、空目录，没有现行规范
- 用户当轮已经明确说了按哪个来
- 同一个项目之前确认过——沿用那次的选择，别再问第二遍

## 核心判据

每写一个视觉值前，先问：

> **这个值是从已有 token 里选的，还是我临时想的？**

- token 里有 → 直接用。
- 没有但确实需要 → **先补 token，再用它**。补 token 是一次决策，写裸值是 200 次决策。
- 只是「这样看着舒服」→ **不行**。
- 答不上来 → 默认用最近的那个 token。

## 硬规则

- **禁止裸值**。`#0ea5e9`、`12px` 这类字面量不进组件样式，一律走变量。宽高、栅格这类单次性尺寸可以裸写；但**间距必须落在 4 的倍数上**（4/8/12/16/24/32）——间距决定节奏，随手写个 13px 的 padding 比色值更容易毁一致性。纯白 `#fff`、`transparent`、`currentColor` 不算裸值。
- **字号只有 4 档**：12（辅助）/ 14（正文）/ 16（小标题）/ 20（页面标题），数据大数字单开一档 28。**最小 12px**，低于它用字重和颜色区分层级，不是缩小。**13px 是历史遗留档位**：收口时按用途归并——辅助信息归 12，正文归 14，**不要再新增**。
- **圆角只有 3 档**：4（小控件）/ 8（按钮、输入框）/ 10（卡片）。**胶囊用 `999px`** 是第 4 种合法值（标签、开关、头像）。嵌套时**内圆角 = 外圆角 − padding**，否则两个圆角看起来不同心。
- **阴影最多 2 档**，且优先用边框。卡片默认「白底 + 0.5px 边框」，不用阴影撑边界；只有真正浮起的元素（弹窗、下拉）才给阴影。
- **语义色只给真语义**。红 = 失败/删除，绿 = 成功，黄 = 警告。**一个指标卡配一个色相不是语义，是装饰**——去掉。
- **线条用 0.5px**，但见下方「0.5px 的代价」。
- **文案不带分隔符**。不用 `·` 拼接（「问答 · 记录」），拆成标题和副标题，或者直接省略。不写解释口径的句子（「未采集到用量的记录不计入」），把信息压成数字（「2 条未采集」）。
- **一行不超过 3 个按钮**。第 4 个起收进「更多」，但**不能静默藏**——入口要有可发现的线索。
- **数字右对齐 + `font-variant-numeric: tabular-nums`**。位数不同还左对齐，就没法竖着扫。

## 0.5px 的代价

0.5px 在 Retina（`devicePixelRatio ≥ 2`）上渲染为物理 1px，细腻；但在 `dpr = 1` 的普通屏会被舍入成 1px，部分 Safari 版本和 Windows 缩放比例下**直接消失**。

- 用可以，但**元素的边界不能只靠这一根线**：底色也要有差异（白卡放在 `slate-2` 上，线只是加强）。
- 更稳的写法是 `box-shadow: 0 0 0 0.5px var(--border)`，它不会参与盒模型，也不会因舍入把布局挤动。
- 判断依据：**管理端给内部人用**，Retina 概率高，0.5px 可以；**C 端面向游客**（含老安卓、Windows 浏览器）必须假设有普通屏，用 1px 或者靠底色区分。

## 最小 token 结构

铺 token 时 `:root` 里长这样，**先只加变量、不改任何页面**（这一步外观零变化，风险为零）：

```css
:root {
  /* 色：抄 Radix 12 阶，只抄用得到的 */
  --slate-1: #fcfcfd;  --slate-2: #f9f9fb;  --slate-6: #d9d9e0;
  --slate-11: #60646c; --slate-12: #1c2024;
  --sky-3: #e1f6fd;    --sky-11: #00749e;
  --red-3: #feebec;    --red-11: #ce2c31;

  /* 形状：只有这几档，组件里不许出现别的值 */
  --radius-sm: 4px;  --radius-md: 8px;  --radius-lg: 10px;  --radius-pill: 999px;

  /* 阴影：只两档，且能不用就不用 */
  --shadow-card: 0 0 0 0.5px var(--slate-6);
  --shadow-float: 0 8px 24px rgba(28, 32, 36, .12);

  /* 字阶：只有 4 档 + 数据大数字 */
  --fs-aux: 12px; --fs-body: 14px; --fs-subtitle: 16px; --fs-title: 20px; --fs-metric: 28px;
}
```

**命名跟用途走，不跟颜色走**：`--sky-11` 可以，`--primary-blue` 不行——后者下次换色相就得改 200 处引用。

## 色板：抄 Radix Colors，不引包

Radix 的每个色相有 12 阶，**每阶有固定用途**，照它用就不会再出现两套灰并排：

| 阶 | 用途 |
|---|---|
| 1–2 | 页面底 / 次级底 |
| 3–5 | 组件底（默认 / hover / active） |
| 6–8 | 边框（弱 / 默认 / 强调） |
| 9–10 | 实心背景（按钮、标签） |
| 11 | 次要文字、图标 |
| 12 | 主要文字 |

**不装依赖**，直接抄值进 `:root`：

```bash
curl -s https://unpkg.com/@radix-ui/colors@3.0.0/{slate,sky,red,amber,green}.css
```

只取 `:root` 段的前 12 行，`@supports` 的 P3 版本可以一并抄（现代浏览器会自动升级）。
主色深色文字配 `11` 阶（不是 `9`，`9` 太浅配白字看不清）；实心按钮底用 `11`，白字。

## 技术栈：默认选型与禁止清单

> **仅 React 项目生效。** Vue 项目跳过本节——shadcn/ui 和 Zustand 都不支持 Vue，图标（`lucide-vue-next`）、动画（Motion 有 Vue runtime）虽有对应包，但具体选型未定，**不要照搬本节的 React 结论**。Vue 项目只沿用本技能前面的 token / 字号 / 圆角 / 反模式规则。

### 默认选型

| 职能 | 用这个 | 理由（不写会踩什么） |
|---|---|---|
| 组件库 | `shadcn/ui`，style 定 `base-nova` | 组件源码在仓库里，能改结构；antd / MUI / Element 那类带自己设计语言的套装会跟本技能的 token 体系打架，最后变成一层层 `!important` 覆盖 |
| 图标 | `lucide-react` | 单一图标源，线条粗细一致 |
| 动画 | `motion`，import 自 `motion/react` | 声明式，动画作为 props 写在 JSX 里，跟组件状态同源 |
| 布局分栏 | `react-resizable-panels` | 支持键盘、百分比尺寸、持久化 |
| 拖拽 | `@dnd-kit/core` | 无障碍、支持键盘拖拽、支持触摸 |
| 状态 | `zustand` | 一个 store 一个 hook，不用包 Provider |

**「优先复用成熟库」的判据**：遇到这六类场景（组件、图标、动画、分栏、拖拽、全局状态），**先找库，别自己写**。自己实现的拖拽和分栏几乎一定在「触摸屏手势 / 键盘可达 / 越界回弹」三处出问题——这些是库已经烧过的钱，重烧一遍不划算。

### 禁止清单（重复依赖）

同一职能只允许一个库。下面每条「禁止」都是真实撞过的组合，不是理论风险：

| 职能 | 唯一允许 | 禁止 | 撞了会怎样 |
|---|---|---|---|
| 图标 | `lucide-react` | `react-icons`、`@heroicons/react`、`@tabler/icons-react`、`@radix-ui/react-icons` | 三套图标混用，同一个「设置」图标三种线条粗细和视觉重量 |
| 动画 | `motion` | `framer-motion`、`react-spring`、`gsap`、`@react-spring/*` | **`framer-motion` 是 `motion` 的旧名（2024-11 改名）**，两者是同一个库的两份拷贝；两个都装会让 `AnimatePresence` 的退出动画互相抢，表现为「退出时闪一下」或「动画不触发」，极难查 |
| 状态 | `zustand` | `redux-toolkit`、`react-redux`、`jotai`、`recoil`、`mobx`、`valtio` | 两套状态机制并存，同一个值要在两处同步，改一处另一处就脏 |
| 拖拽 | `@dnd-kit/core` | `react-dnd`、`react-beautiful-dnd`、`sortablejs`、`@atlaskit/pragmatic-drag-and-drop` | `react-beautiful-dnd` 已停止维护，React 18 严格模式下直接崩 |
| 布局分栏 | `react-resizable-panels` | `react-split-pane`、`allotment`、`react-resizable` | 尺寸持久化实现不同，混用会互相覆盖 localStorage key |
| 组件基座 | Base UI | Radix UI、`@radix-ui/*` 直接 import | `components.json` 只能记一个 base，混装后 `shadcn add` 会按错的 base 生成代码；且 Radix 版没有 Toast 了（见下） |

**Motion 的坑单独记一条**：`npm i framer-motion` 不会报错，旧包至今仍在同步发版，所以「装上了、也能 import」给人一切正常的错觉。判断当前项目用的是哪个，看 import 路径而不是包名：

```bash
# 正确：motion/react
rg "from ['\"]motion/react" src
# 混入旧包：出现 framer-motion 就是没迁干净
rg "framer-motion" src package.json
```

迁移是纯重命名：卸 `framer-motion` → 装 `motion` → 把 `from "framer-motion"` 全改成 `from "motion/react"`。API 一模一样，不用改逻辑。

### Nova 不是主题，是组件源码

最容易搞错的点：以为选 Nova 等于换个配色。**不是。** shadcn 的 style 会改写组件本身的间距、内边距、结构，换 style 要**重装组件**，改 CSS 变量没用。

落地点在 `components.json`：

```json
{ "style": "base-nova" }
```

`base-` 是底层用 Base UI，`radix-` 是 Radix UI。**定了不能混**（见上面禁止清单最后一行），也**不要中途换**——换基座要把全部组件 `add --overwrite` 重装一遍，`asChild` 还会变成 `render`。

**为什么定 Base UI 而不是 Radix**（2026-10 现状）：

- shadcn 自 2026-07 起把 Base UI 设为默认底层、新项目官方推荐。Radix 仍在维护、**不会被弃用**，但它现在的定位是「已经把项目跑起来的人别动」。
- **Radix 侧的 Toast 已废弃**，官方文档原话是改用 Sonner。也就是走 Radix 路线想要 toast 就得再装 sonner，直接跟上面「同一职能只允许一个库」冲突。Base UI 侧 Toast 是原生组件。
- Radix 被 WorkOS 收购后原语发版明显放缓，Base UI 有全职团队在推（2025-12 到 1.0，现在 1.9.x，基本每月一版）。
- 代价也要认：Base UI 体积略大（社区实测 8 个基础原语 gzip 约 124KB，Radix 约 99KB），第三方教程和示例少一些。这两条不足以抵消上面的方向性。

**Base UI 必做的一件事——根元素加 `isolation: isolate`**：

```css
.root { isolation: isolate; }
```

官方 quick start 明确要求。不加以后的症状是「弹窗被自己页面内容盖住」，或者 `z-index` 怎么调都没用——因为 Portal 出去的浮层跑到了别的层叠上下文里，跟页面内容不在同一套层叠规则下比大小。**这时候不要靠加 `z-50` 硬拉**，那是治标，会引出下一个位置更高的冲突。

同时给 iOS 26+ Safari 补一条，否则滚动后弹窗遮罩盖不满视口：

```css
body { position: relative; }
```

Nova 的特点是 padding / margin 更紧，为 dashboard、后台、数据密集界面设计。**面向 C 端游客的展示页不要用 Nova**——那种页面要松，Maia 更合适。选错风格，后面所有间距都得靠 `p-6` 一个个救，正好破坏本技能的间距规则。

### shadcn 的圆角会多出第 5 档，必须钉死

shadcn 的圆角是一个值派生的（官方固定公式）：

```
--radius-sm: calc(var(--radius) * 0.6)    --radius: 0.625rem (10px)
--radius-md: calc(var(--radius) * 0.8)         ↓ 代进去
--radius-lg: var(--radius)                 sm=6  md=8  lg=10
--radius-xl: calc(var(--radius) * 1.4)     xl=14 2xl=18 3xl=22
```

本技能的硬规则是「圆角只有 4 / 8 / 10，胶囊 999」。**6px 和 14px 都不是合法档位**，`rounded-sm`、`rounded-xl` 一用就违规。

初始化时直接覆写 sm，别用派生值：

```css
@theme inline {
  --radius-sm: 4px;                        /* 钉死，不派生 */
  --radius-md: calc(var(--radius) * 0.8);  /* 10px → 8px，合法 */
  --radius-lg: var(--radius);              /* 10px，合法 */
}
```

同时**禁止使用 `rounded-xl` 及以上**。需要「更圆」的观感时，用间距和底色区分层级，不是加大圆角。

## 反模式

看到这些先怀疑，不要照抄：

- **一指标一色相**：5 张卡 5 种颜色，颜色不承载任何语义，只是让每张卡「有存在感」。
- **彩色渐变图标**：`linear-gradient` 底 + 白色线性图标。这是后台模板的默认长相，换成「主色 3 阶浅底 + 主色 11 阶图标」立刻安静。
- **卡片左边缘色条**：用来标状态，但状态用文字或标签更准确。
- **重阴影撑边界**：`0 4px 12px rgba(0,0,0,.08)` 铺满全站，白卡会浮起来像贴纸。
- **数字左对齐**：表格里的数字一律右对齐 + `font-variant-numeric: tabular-nums`，否则位数不同就无法竖着扫。
- **用省略号吃掉信息**：`text-overflow: ellipsis` 把「未计费 23…」截断，等于白算。
- **一行 9 个按钮**：每个都是「配置 XX」，用户每次都要重新找。这是**功能问题伪装成视觉问题**。

## 改存量页面的流程

> 动手前先确认按谁的标准，见「开工前：先问一句按谁的标准」。以下按「用户已经选了 B」写。

1. **先跑一次自检存基线**，不先改。把数字贴进对话，改完才能证明是降了还是升了。
2. **列裸值**，把这一页所有 hex 和 px 字号打出来，看有多少。
3. **按用途映射，不按颜色相似度**。`#3b82f6` 和 `#0ea5e9` 都是蓝，但一个是链接、一个是主色，映射到不同阶。按「这个颜色在这里干什么」决定，不按「长得像不像」。
4. **替换**，一次只动一页。
5. **截图对比**，改前改后各一张。数字证明一致性变好了，截图证明没变难看——两者缺一不可。
6. **再跑自检**，和基线比。

## 自检（机械可验证，不靠眼睛）

在**项目根目录**跑，只看数字，不做审美判断：

```bash
python3 - <<'EOF'
import re, pathlib, collections

STYLE = ('.css', '.scss', '.vue')      # 样式写在这里
CODE  = ('.tsx', '.jsx')               # Tailwind 项目里样式其实写在这里
files = [p for p in pathlib.Path('src').rglob('*') if p.suffix in STYLE + CODE]

def norm(h):
    """#fff 与 #ffffff 是同一个颜色，不展开会虚高。"""
    h = h.lower()
    return '#' + ''.join(c * 2 for c in h[1:]) if len(h) == 4 else h

# Tailwind 任意值必须按职能分开：text-[13px] 是字号，rounded-[6px] 是圆角，混着统计就没意义
ARB = {
    'color':  r'\b(?:bg|text|border|ring|fill|stroke)-\[(#[0-9a-fA-F]{3,8})\]',
    'text':   r'\btext-\[([0-9.]+px)\]',
    'radius': r'\brounded(?:-[a-z]+)?-\[([0-9.]+px)\]',
    'space':  r'\b(?:p|m|gap)[xytrbl]?-\[([0-9.]+px)\]',
}

hexes, sizes, radii, shadows = (collections.Counter() for _ in range(4))
arb = {k: collections.Counter() for k in ARB}

for f in files:
    t = f.read_text(encoding='utf-8', errors='ignore')
    for k, pat in ARB.items():
        arb[k].update(re.findall(pat, t))
    if f.suffix not in STYLE:
        continue
    t = re.sub(r'\[[^\]]*\]', '', t)   # 剔除任意值，否则 class 里的会被当 CSS 再算一次
    hexes.update(norm(m) for m in re.findall(r'#[0-9a-fA-F]{3,8}\b', t))
    sizes.update(re.findall(r'font-size:\s*([0-9.]+px)', t))
    radii.update(re.findall(r'border-radius:\s*([0-9.]+px)', t))
    shadows.update(s.strip() for s in re.findall(r'box-shadow:\s*([^;}\n]+)', t))

all_hex  = hexes + arb['color']
all_size = sizes + arb['text']
all_rad  = radii + arb['radius']

print('裸色值 %d（其中 class 任意值 %d）' % (len(all_hex), len(arb['color'])))
print('字号档位 %d（其中 class 任意值 %d）| 圆角档位 %d | 阴影种数 %d'
      % (len(all_size), len(arb['text']), len(all_rad), len(shadows)))
print('低于 12px:', {k: v for k, v in all_size.items() if float(k[:-2]) < 12} or '无')
print('超出档位的圆角:', {k: v for k, v in all_rad.items()
                        if k not in ('4px', '8px', '10px', '999px')} or '无')
print('不在 4 倍数上的间距:', {k: v for k, v in arb['space'].items() if float(k[:-2]) % 4} or '无')
print('阴影最多的 5 个:', shadows.most_common(5))
EOF
```

**Tailwind 项目必看**：装了 shadcn / Tailwind 之后，样式从 `.css` 搬进 `className`，写成 `bg-[#0ea5e9]` 这种任意值。只扫 `.css` 的脚本会报「裸色值 0」——**假阴性比不检查更危险**，数字干净但什么都没管住。上面的脚本已经把两边合起来统计，括号里是 class 任意值的数量。任意值还按职能分开了（字号 / 圆角 / 间距），因为 `rounded-[6px]` 归到字号里会让人误判。

## 自检 Ⅱ：重复依赖（只看 package.json）

在**项目根目录**跑，对照上面的禁止清单：

```bash
python3 - <<'EOF'
import json, pathlib

BAN = {
    'framer-motion': 'Motion 的旧名，改用 motion + import from "motion/react"',
    'react-icons': '图标统一 lucide-react',
    '@heroicons/react': '图标统一 lucide-react',
    '@tabler/icons-react': '图标统一 lucide-react',
    'react-dnd': '拖拽统一 @dnd-kit/core',
    'react-beautiful-dnd': '已停止维护，改用 @dnd-kit/core',
    'react-split-pane': '分栏统一 react-resizable-panels',
    'allotment': '分栏统一 react-resizable-panels',
    'redux': '状态统一 zustand',
    '@reduxjs/toolkit': '状态统一 zustand',
    'react-redux': '状态统一 zustand',
    'jotai': '状态统一 zustand',
    'recoil': '状态统一 zustand',
    'mobx': '状态统一 zustand',
    'react-spring': '动画统一 motion',
    '@react-spring/web': '动画统一 motion',
    'radix-ui': '组件基座统一 Base UI（base-nova）',
}

p = pathlib.Path('package.json')
if not p.exists():
    print('没有 package.json，跳过')
else:
    d = json.loads(p.read_text(encoding='utf-8'))
    deps = {**d.get('dependencies', {}), **d.get('devDependencies', {})}
    # @radix-ui/* 有几十个子包，穷举不现实，按前缀拒
    PREFIX = {'@radix-ui/': '组件基座统一 Base UI（base-nova），见上文选型'}
    hits = {k: (v, BAN[k]) for k, v in deps.items() if k in BAN}
    for k, v in deps.items():
        for pre, why in PREFIX.items():
            if k.startswith(pre):
                hits[k] = (v, why)
    if not hits:
        print('重复依赖：无')
    for k, (v, why) in sorted(hits.items()):
        print('命中 %-24s %-10s %s' % (k, v, why))
EOF
```

命中任意一行都要处理：**卸掉它，把调用点迁到允许的那个库**。不要「先留着以后再说」——两个库同时装着，bug 会以「动画不触发」「拖拽偶尔失灵」这种形式出现，很难归因到依赖上。

脚本只认 `px` 单位的字号（`1rem = 16px`，用 rem 的项目自行折算判定阈值）。

判定标准：

- 裸色值数量 **应该随改动下降**。改一页而它上升，就是又写了裸值。
- **Tailwind 项目看第二个数字**（class 任意值）：它是新增裸值的主要来源。`bg-[#0ea5e9]` 和 `bg-primary` 都是「一个颜色」，区别只在于后者有出处。
- 字号档位 **≤ 5**（4 档 + 数据大数字），低于 12px 的项 **必须为空**。
- 圆角 **只允许 4/8/10/999**，其余都是待收的。shadcn 项目额外注意 `rounded-sm`（派生出 6px）和 `rounded-xl`（14px）都不能用，见上文「钉死」那节。
- 阴影种数 **≤ 2**。超过说明在用阴影做本该由边框做的事。
- 重复依赖检查 **命中必须为零**。命中后只卸不迁等于把功能删了，必须把调用点一起改掉。
- 用 Grep 工具查 `·`，不要用 bash 的 `grep`（某些环境会静默返回空）。

## 反面示例

需求：「给互动问答页加一排指标卡。」

**错误做法**——每张卡配一个色相，看起来像认真设计了：

```css
.stat:nth-child(1) .icon { background: linear-gradient(135deg, #0ea5e9, #0284c7) }
.stat:nth-child(2) .icon { background: linear-gradient(135deg, #10b981, #059669) }
.stat:nth-child(3) .icon { background: linear-gradient(135deg, #f59e0b, #d97706) }
.stat:nth-child(4) .icon { background: linear-gradient(135deg, #ef4444, #dc2626) }
.stat:nth-child(1) b { color: #0ea5e9 }  /* 数字也跟着染色 */
.stat:nth-child(2) b { color: #10b981 }
```

问题不在好看不好看，在于：4 个色相里只有红承载语义（失败），其余三个是装饰。
用户看到红色会以为「这里有危险」，而它只是「第 4 张卡」。

**正确做法**——颜色只留给语义，其余全部中性：

```css
.stat .icon { background: var(--sky-3); color: var(--sky-11) }
.stat b { color: var(--slate-12); font-variant-numeric: tabular-nums }
/* 只有失败卡用语义色 */
.stat.is-danger .icon { background: var(--red-3); color: var(--red-11) }
.stat.is-danger b { color: var(--red-11) }
```

## 正面示例

一次合格的样式改动，改完看 diff 应该是这样：

```css
.card { background: #fff; border: 0.5px solid var(--slate-6); border-radius: var(--radius-lg) }
.card-icon { background: var(--sky-3); color: var(--sky-11); border-radius: var(--radius-md) }
.card-value { font-size: var(--fs-metric); color: var(--slate-12); font-variant-numeric: tabular-nums }
.card-label { font-size: var(--fs-aux); color: var(--slate-11) }
```

零渐变、零阴影，两档字号，两个圆角档位。删掉任意一行都会破坏一致性，所以它就该这么短。

## 与其他规则的关系

- 用户要求**探索视觉方向**（给几个方案挑、做对比稿）时，**本技能让位**——那一步需要放开发散，先确定方向再回来受约束。
- 改业务逻辑、修功能 bug 时**不适用**，哪怕顺手动了样式——只保证不引入裸值即可。
- 发现整个项目的样式体系有问题，**只报告，不主动整改**；改哪一页由用户指派。存量 210 个色值不是一次能收干净的，一页一页来。
- 用户明确说「先这样，别管一致性」时，本技能停止生效，不要反复提。
- **「技术栈」那一节仅 React 项目生效**。Vue 项目跳到该节开头的提示，只沿用 token / 字号 / 圆角 / 反模式规则，不要把 shadcn、Zustand 的结论套到 Vue 上。判断依据是项目里有没有 `react` 依赖，不是文件名后缀。
