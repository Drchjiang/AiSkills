📦 技能清单：可复用的「HTML 幻灯片/报告生成器」配置
你可以把下面这段内容复制保存，下次发给我时，只需说“按以下技能，帮我生成 [主题] 的 HTML 幻灯片”，我就会自动调用。

技能名称：现代化可编辑 HTML 幻灯片/信息页生成器
一、视觉风格系统（Frontend Style）
1. 色彩体系

背景：浅色渐变，如 linear-gradient(135deg, #f0f4ff 0%, #dce3f5 100%)，营造明亮通透感。

主色：蓝色 #4f8cff（用于标题、重点、激活状态）

辅助色：

成功/稳定：绿色 #34d399

强调/科技：紫色 #a78bfa

警告/一般：橙色 #fb923c

活力/高亮：粉色 #f472b6

文字色：深色 #1a202c（主标题）、#2d3748（正文）、#718096（辅助说明）

卡片背景：rgba(255,255,255,0.65~0.75) + backdrop-filter: blur(16px)（毛玻璃效果）

边框：rgba(255,255,255,0.6~0.9)（轻盈高光）

2. 排版

字体：'Inter', 'Segoe UI', system-ui, sans-serif

标题大小：封面 52px，内页 28px，卡片标题 18px

正文字号：13~15px，数字指标 17~22px

3. 卡片 & 容器

卡片圆角：24px；幻灯片容器圆角：48px

卡片阴影：0 8px 30px rgba(0,0,0,0.04)，悬停加深至 0 20px 50px rgba(0,0,0,0.08)

卡片悬停：translateY(-6px)，边框变亮

二、动画与交互规范
1. 页面切换动画

使用 @keyframes slideFade：从 opacity:0.4; transform: translateY(18px) scale(0.97) 到 opacity:1; transform: translateY(0) scale(1)

持续时间：0.5s，缓动函数 cubic-bezier(0.23, 1, 0.32, 1)

2. 卡片入场动画

使用 @keyframes cardUp：从 opacity:0; transform: translateY(20px) 到 opacity:1; transform: translateY(0)

延迟依次递增：nth-child(1) 0.05s，nth-child(2) 0.15s，nth-child(3) 0.25s

每次翻页时，用 JS 重置动画（移除并重新添加类或重置 animation）

3. 导航交互

底部毛玻璃导航条，包含左右箭头按钮、圆点指示器、页码计数器

键盘支持：←/→ 键翻页，空格 键下一页

触摸支持：左右滑动翻页（阈值 40px）

激活圆点：宽度从 10px 变为 28px（圆角矩形），带主色发光阴影

三、内容可编辑（ContentEditable）
所有标题、数据数值、标签文字、配置参数均设置 contenteditable="true"

悬停时显示浅色虚线边框提示可编辑，聚焦时显示主色轮廓高亮

编辑实时生效，无需刷新

四、布局结构（PPT 多页模式）
分页容器：

html
<div class="slides-wrapper">
  <div class="slide active" data-index="0"> ... </div>
  <div class="slide" data-index="1"> ... </div>
  ...
</div>
每页独立，display: none 切换为 flex

每页内容用 flex-direction: column 纵向排列

页面内网格布局：grid-2（2列）、grid-3（3列），响应式自动堆叠

典型页面构成：

封面页：徽章 + 大标题（渐变文字）+ 副标题 + 元数据标签 + 环境标识

对比页：页面标题（左）+ 副标题（右）→ 2列或3列卡片网格

总结页：柱状图 + 关键指标卡片（优势数据突出显示）

五、数据展示组件
1. 对比卡片（Card）

头部：设备图标（SVG）+ 名称 + 连接方式徽章（WiFi/网口/USB，带颜色区分）

指标区：延迟（带状态标签：极佳/优秀/一般）+ 分辨率

配置区：芯片、制程、CPU、GPU、内存、安卓版本（网格 2 列展示）

2. 柱状图（总结页）

使用 CSS 实现：bar-track 背景 + bar-fill 宽度百分比

标签 + 进度条 + 数值，可编辑

3. 优势指标卡

4 个指标并列（性能领先、延迟优化、制程差距、WiFi版本）

数值用彩色（蓝/绿/紫）突出

六、性能与网络优化
CDN 策略：优先使用 cdn.jsdelivr.net，备选 cdnjs.cloudflare.com

字体加载：使用 preconnect 和 Google Fonts Inter（但需防阻塞）

ECharts（如有）：懒加载 + 预加载（延迟 3 秒后静默下载），避免首屏阻塞

七、响应式断点
≤992px：内页间距缩小，2列变1列（grid-2 保持2列），封面字体 38px

≤640px：全屏单列，封面字体 28px，卡片紧凑布局，隐藏编辑提示
---
## 八、预设风格模板库（新增）

生成幻灯片时，优先从以下风格库中让用户选择，而不是每次都从零设计：

**next-slide 50+ 风格分类**：
- 暗色（11种）：Keynote Noir、Neon Cyber、Terminal Green、Dark Botanical 等
- 浅色（11种）：Swiss Modern、Paper & Ink、Pastel Geometry、Wabi-Sabi Zen 等
- 编辑（4种）：Editorial Serif、Fashion Editorial、Newsprint Broadsheet 等
- 大胆（7种）：Electric Studio、Pop Art、Bold Typography 等
- 复古（5种）：Grainy Retro、Art Deco Gatsby、Risograph Overprint 等
- 艺术（7种）：Surrealism Gallery、Scrapbook Portfolio、Art Nouveau Botanical 等
- 文化（8种）：东方墨韵、和風、Bauhaus Primary、Swiss Grid 等

**beautiful-html-templates 34 套模板**：每套含封面/中段/尾页三种布局，覆盖杂志、霓虹、极简、复古等多种视觉风格。

**html-ppt-skill 36 主题 + 15 完整 Deck**：含 31 种页面布局、27 种 CSS 动画、20 种 Canvas 特效。

## 九、HTML → PPTX 导出（新增）

生成的 HTML 幻灯片应支持导出为可编辑的 PowerPoint 文件。

**推荐工具链**：
1. **html-to-pptx**（前端库）：在浏览器中将 DOM 元素转为原生 PPT 形状/文本框/表格/图表，支持 CSS 动画映射。`npm install html-to-pptx`
2. **html-to-pptx**（Python 库）：用 headless 浏览器测量 DOM，映射为原生 PPTX。`pip install html-to-pptx`
3. **@0-ai/slide-gen**[reference:35]：用 Playwright 渲染 HTML，导出 PPTX/PDF/PNG

**导出规范**：
- 每页幻灯片用 `<section class="slide">` 包裹，宽高比 16:9（1920×1080）
- 文本、表格、图片均转换为可编辑的 PPT 原生对象，而非截图

## 十、演讲者模式（新增）

生成的幻灯片应内置演讲者模式，方便现场演讲。

**参考实现**（html-ppt-skill）：
- 按 `S` 键弹出独立演讲者窗口
- 四个可拖拽卡片：当前幻灯片、下一页预览、逐字稿、计时器
- 通过 `BroadcastChannel` 双向同步，无刷新无闪烁
- 逐字稿规范：每页 150-300 字，写提示信号而非照读台词

## 十一、动画 Skill 参考（新增）

复杂动画场景可参考以下专业 Skill：

| Skill | 用途 |
|-------|------|
| **gsap-skills**[reference:40] | GreenSock 官方开源，让 AI 更懂 GSAP 动画和性能最佳实践 |
| **web-motion-design**[reference:41] | CSS 动画、JavaScript 过渡、React/Vue 动效通用 Skill |
| **auto-animate**[reference:42] | 零配置动画（3.28KB），适用于列表/手风琴/Toast 等 |

**动画原则**：
- 优先 CSS-only 动画，复杂场景用 GSAP/Motion One
- 聚焦高 impact 时刻：一次精心编排的页面加载 + 交错揭示（animation-delay）
- 避免零散微交互，集中力量做好入场动画

## 十二、设计系统（新增）

建议将设计规范抽象为 CSS 变量（token 化），便于全局切换主题。

**参考 Peg Design System**：
- 6 个颜色 token、1 个暖白背景、1 个翡翠绿强调色
- Inter 字体、无阴影无渐变、内容直接呈现在“纸面”上
- 所有动画尊重 `prefers-reduced-motion`

**html-ppt-skill 的 token 系统**[reference:45]：所有页面共享基于 token 的设计系统，36 种主题以独立 CSS 文件组织。

---
## 十三、字体与文字特效系统（新增）

### 1. 字体多样化

生成的幻灯片应提供丰富的字体选择，满足不同风格需求。

**推荐字体清单**：

| 类别 | 字体 | 用途 |
|------|------|------|
| **英文字体** | Inter、Arial、Georgia、Times New Roman、Courier New | 通用、正式、古典、等宽 |
| **中文字体** | PingFang SC、Microsoft YaHei、Noto Sans SC | 系统黑体，清晰通用 |
| **中文字体** | Songti SC、SimSun、Noto Serif SC | 系统宋体，古典正式 |
| **手写/个性** | ZCOOL XiaoWei、Comic Sans MS | 轻松、创意、非正式场合 |

**Google Fonts 加载示例**：
```html
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700;800&family=Noto+Sans+SC:wght@400;500;700;900&family=ZCOOL+XiaoWei&display=swap" rel="stylesheet" />
字体切换机制：

全局字体切换：通过 CSS 变量 --font-family 控制，在全局设置面板中提供下拉菜单

局部字体切换：选中文字后，在浮动工具栏中单独修改字体

2. 文字特效系统
为文字提供多种夸张风格，满足艺术字、标题强调等需求。

支持的特效类型：

特效名称	CSS 实现方式	效果描述
渐变	background: linear-gradient(...); -webkit-background-clip: text; -webkit-text-fill-color: transparent;	文字填充渐变色，时尚醒目
描边	-webkit-text-stroke: 2px #color; color: transparent;	文字轮廓描边，适合标题
3D 立体	多层 text-shadow 叠加 + transform: skewX(-5deg)	模拟立体凸起效果，有厚度感
霓虹发光	多层 text-shadow 发光色 + 深色背景	模拟霓虹灯管发光，酷炫吸睛
特效应用示例（3D立体）：

css
.text-effect-3d {
    color: #1a202c;
    text-shadow: 
        1px 1px 0 #cbd5e0,
        2px 2px 0 #a0aec0,
        3px 3px 0 #718096,
        4px 4px 0 #4a5568,
        5px 5px 0 #2d3748,
        6px 6px 0 #1a202c;
    transform: skewX(-5deg);
}
特效应用机制：

选中文字后，在浮动工具栏的“特效”下拉菜单中选择

特效通过添加 CSS 类（如 .text-effect-gradient）实现，不污染原始内容

可随时清除特效，恢复默认样式

3. 选中文字格式工具栏
提供一个浮动工具栏，让用户对选中的文字进行精细化样式调整。

工具栏包含的功能：

功能	控件类型	说明
字体	下拉菜单	从预设字体列表中选择
字号	下拉菜单	12-40px，常用字号
文字颜色	颜色选择器	任意颜色，实时预览
加粗	按钮	切换粗体
斜体	按钮	切换斜体
下划线	按钮	切换下划线
特效	下拉菜单	渐变/描边/3D/霓虹
清除格式	按钮	移除所有手动样式，恢复默认
交互逻辑：

当用户选中可编辑区域中的文字时，工具栏自动出现在选区上方

工具栏的位置跟随选区变化，避免遮挡内容

选中文字时，工具栏自动读取并显示当前文字的样式状态

点击“清除格式”可一键移除所有手动样式

4. 全局样式控制面板
在底部导航条中提供 ⚙️ 按钮，点击弹出全局样式设置面板。

面板包含的控制项：

字体选择：下拉菜单，切换全局字体（影响所有文字）

基础字号：滑块（12-24px），调整整体文字大小

标题放大倍数：滑块（1.0-2.0x），控制标题与正文的比例关系

实现方式：

使用 CSS 变量（--font-family、--base-font-size、--heading-scale）

所有页面元素按比例响应，保持视觉和谐

调整实时生效，无需刷新

十四、字体与特效资源参考
1. Google Fonts 推荐字体
字体名称	加载地址	风格特点
Inter	Inter:wght@400;500;700;800	现代无衬线，适合标题和正文
Noto Sans SC	Noto+Sans+SC:wght@400;500;700;900	中文字体，清晰通用
ZCOOL XiaoWei	ZCOOL+XiaoWei	手写风格，轻松活泼
Playfair Display	Playfair+Display:wght@400;700	衬线字体，优雅古典
Poppins	Poppins:wght@400;500;700	圆润无衬线，亲和现代
2. CSS 文字特效完整代码片段
渐变文字：

css
.gradient-text {
    background: linear-gradient(135deg, #f093fb, #f5576c, #4facfe);
    -webkit-background-clip: text;
    background-clip: text;
    -webkit-text-fill-color: transparent;
    color: transparent;
}
描边文字：

css
.stroke-text {
    -webkit-text-stroke: 2px #4f8cff;
    text-stroke: 2px #4f8cff;
    color: transparent;
}
3D 立体文字：

css
.text-3d {
    color: #1a202c;
    text-shadow: 
        1px 1px 0 #cbd5e0,
        2px 2px 0 #a0aec0,
        3px 3px 0 #718096,
        4px 4px 0 #4a5568,
        5px 5px 0 #2d3748,
        6px 6px 0 #1a202c;
    transform: skewX(-5deg);
}
霓虹发光文字：

css
.neon-text {
    color: #fff;
    text-shadow: 
        0 0 7px #4f8cff,
        0 0 10px #4f8cff,
        0 0 21px #4f8cff,
        0 0 42px #4f8cff,
        0 0 82px #4f8cff;
    background: #1a202c;
    padding: 4px 12px;
    border-radius: 8px;
    display: inline-block;
}
3. 技能来源
本次新增的字体与特效技能，综合参考了以下资源：

资源	贡献内容
Google Fonts	多字体加载与切换方案
CSS-Tricks	文字特效（渐变、描边、3D、霓虹）的 CSS 实现
html-ppt-skill	选中文字格式工具栏的交互逻辑
gsap-skills	复杂文字动画的 GSAP 实现思路（如需更高级动画）
4. 使用建议
标题：适合使用渐变、3D 立体或霓虹特效，增强视觉冲击力

正文：保持简洁，避免特效，保证可读性

数据指标：可使用描边或渐变突出关键数字

特效数量：每页建议不超过 2-3 处特效，避免视觉杂乱

响应式：3D 和霓虹特效在移动端可能影响性能，建议仅在桌面端使用
