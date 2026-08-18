# 数据看板/信息型页面生成器

## 📌 适用场景

生成**数据驱动的信息型页面**，如产品对比、数据看板、设备参数展示、评测汇总等。以 `tvboxpk`（电视盒子性能对比页）为典型范例，涵盖**数据管理、表格渲染、图表可视化、交互筛选、响应式设计**等完整能力。

---

## 一、视觉风格系统

### 1. 色彩体系

| 用途 | 颜色值 | 说明 |
|------|--------|------|
| 页面背景 | `#0b0f1a` | 深空蓝黑，科技感基底 |
| 容器背景 | `rgba(12,20,35,0.65)` | 毛玻璃半透明效果 |
| 边框/高光 | `rgba(0,180,255,0.15)` | 蓝色系点缀 |
| 主标题渐变 | `#7eb8ff → #a78bfa → #6b8cff` | 蓝紫渐变 |
| 表格头部 | `rgba(0,160,255,0.08)` | 极淡蓝底 |
| 表格文字 | `#c8dbe9` | 柔白蓝 |
| 高亮数字 | `#b0d4ff` | 分数/指标高亮 |
| ✔ 符号 | `#4ade80` | 支持（绿色） |
| ✘ 符号 | `#f87171` | 不支持（红色） |
| 金色徽章 | `#ffd700` | 千兆网口/高级特性 |

### 2. 毛玻璃效果（Glassmorphism）

```css
background: rgba(12, 20, 35, 0.65);
backdrop-filter: blur(12px);
-webkit-backdrop-filter: blur(12px);
border: 1px solid rgba(0, 180, 255, 0.15);
border-radius: 40px;
box-shadow: 0 20px 60px rgba(0, 20, 80, 0.6);
3. 排版规范
元素	字号	字重	颜色
页面标题	42px	700	渐变蓝紫
板块标题	24px	600	#d6e8ff
表格表头	12px	500	#7eb8ff
表格内容	13px	400	#c8d6e8
型号名称	13px	600	#d6e8ff
筛选按钮	13px	500	#8aa8d0
二、数据层设计
1. 数据外置（data.json）
将数据与 HTML 分离，便于维护和更新：

json
{
  "devices": [
    {
      "name": "当贝H5（新）",
      "config": "2+64",
      "chip": "全志H618",
      "total": 91947,
      "process": "28nm",
      "cpu": 26430,
      "cores": "4核A53",
      "gpuScore": 12689,
      "gpuName": "Mali-G31 MP2",
      "mem": 35224,
      "bandwidth": 5.4,
      "sys": 17604,
      "android": "12",
      "eth": "无",
      "wifi": "双频WiFi5",
      "usb": "2.0",
      "isTv": false
    }
  ],
  "avData": [
    {
      "name": "当贝H5（新）",
      "4K60Hz": true,
      "1080P120Hz": false,
      "4K120Hz": false,
      "HDR10": true,
      "HDR10+": true,
      "杜比视界": false,
      "杜比全景声": true,
      "DTS:X": true
    }
  ]
}
2. 备份数据策略
当 data.json 加载失败时，使用 JS 内嵌备份数据，保证页面可用：

javascript
function loadData() {
    return fetch('data.json')
        .then(res => {
            if (!res.ok) throw new Error('Network response was not ok');
            return res.json();
        })
        .then(data => {
            devices = data.devices;
            avData = data.avData;
        })
        .catch(() => {
            console.warn('加载 data.json 失败，使用内嵌备份数据');
            devices = backupDevices;
            avData = backupAv;
        })
        .finally(() => {
            renderMain();
            renderAv();
            // ... 其他初始化
        });
}
三、表格渲染系统
1. 表格结构
html
<div class="table-wrap">
    <table id="mainTable">
        <thead>
            <tr>
                <th data-col="name">名称</th>
                <th data-col="total" class="sortable">总分</th>
                <!-- ... -->
            </tr>
        </thead>
        <tbody id="mainTbody"></tbody>
    </table>
</div>
2. 动态渲染
javascript
function renderMain() {
    const data = getSortedData(getFilteredData());
    const tbody = document.getElementById('mainTbody');
    tbody.innerHTML = data.map(d => `
        <tr>
            <td><span class="model-name" data-name="${d.name}">${d.name}</span>${d.isTv ? ' <span class="badge badge-tv">电视</span>' : ''}</td>
            <td>${d.config}</td>
            <td><span class="badge badge-chip">${d.chip}</span></td>
            <td class="score-highlight">${d.total.toLocaleString()}</td>
            <!-- ... -->
        </tr>
    `).join('');
}
3. 首列固定
css
th:first-child, td:first-child {
    position: sticky;
    left: 0;
    z-index: 6;
    background: rgba(8, 16, 30, 0.9);
    backdrop-filter: blur(4px);
    border-right: 1px solid rgba(0, 180, 255, 0.08);
}
四、交互功能
1. 排序
点击表头（带 .sortable 类）触发排序

支持升序/降序切换

显示 ▲/▼ 指示器

Toast 提示排序结果

javascript
function setupSorting() {
    const headers = document.querySelectorAll('#mainTable th.sortable');
    headers.forEach(th => {
        th.addEventListener('click', function() {
            const col = this.dataset.col;
            if (currentSort.col === col) {
                currentSort.dir = currentSort.dir === 'asc' ? 'desc' : 'asc';
            } else {
                currentSort.col = col;
                currentSort.dir = 'asc';
            }
            renderMain();
            showToast(`已按「${this.textContent.trim()}」${currentSort.dir === 'asc' ? '升序' : '降序'}排列`);
        });
    });
}
2. 筛选
javascript
function setupFilter() {
    const btns = document.querySelectorAll('.filter-btn');
    btns.forEach(btn => {
        btn.addEventListener('click', function() {
            btns.forEach(b => b.classList.remove('active'));
            this.classList.add('active');
            currentFilter = this.dataset.filter;
            renderMain();
            showToast(`筛选：${this.textContent}`);
        });
    });
}

function getFilteredData() {
    let data = devices;
    if (currentFilter !== 'all') {
        data = data.filter(d => d.chip.includes(currentFilter));
    }
    if (searchQuery.trim() !== '') {
        const q = searchQuery.trim().toLowerCase();
        data = data.filter(d => d.name.toLowerCase().includes(q));
    }
    return data;
}
3. 搜索
javascript
function setupSearch() {
    const input = document.getElementById('searchInput');
    input.addEventListener('input', function() {
        searchQuery = this.value;
        renderMain();
        if (searchQuery.trim() !== '') {
            showToast(`搜索：${searchQuery.trim()}`);
        }
    });
}
五、图表可视化（ECharts）
1. 懒加载 + 预加载
javascript
function lazyLoadChart() {
    const chartBox = document.getElementById('chartBox');
    const observer = new IntersectionObserver((entries) => {
        if (entries[0].isIntersecting && !loaded) {
            loaded = true;
            loadEChartsAndInit();
            observer.disconnect();
        }
    }, { threshold: 0.1 });
    observer.observe(chartBox);

    // 预加载：延迟 3 秒后静默下载
    if (typeof echarts === 'undefined') {
        setTimeout(function() {
            if (typeof echarts === 'undefined') {
                const preloadScript = document.createElement('script');
                preloadScript.src = 'https://cdn.jsdelivr.net/npm/echarts@5.4.3/dist/echarts.min.js';
                document.head.appendChild(preloadScript);
            }
        }, 3000);
    }
}
2. CDN 策略
优先级	CDN 地址	说明
主	cdn.jsdelivr.net/npm/echarts@5.4.3/dist/echarts.min.js	国内访问快
备	cdnjs.cloudflare.com/ajax/libs/echarts/5.4.3/echarts.min.js	全球备选
3. 图表配置
javascript
const option = {
    tooltip: { trigger: 'axis', axisPointer: { type: 'shadow' } },
    legend: { data: ['总分', 'CPU', 'GPU'], textStyle: { color: '#b0cce0' } },
    xAxis: { type: 'category', data: data.map(d => d.name) },
    yAxis: { type: 'value', name: '跑分' },
    series: [
        { name: '总分', type: 'bar', data: data.map(d => d.total) },
        { name: 'CPU', type: 'bar', data: data.map(d => d.cpu) },
        { name: 'GPU', type: 'bar', data: data.map(d => d.gpuScore) }
    ]
};
六、模态框（详情弹窗）
1. 触发方式
点击表格中的型号名称（.model-name）触发：

javascript
document.querySelectorAll('.model-name').forEach(el => {
    el.addEventListener('click', () => openModal(el.dataset.name));
});
2. 模态框结构
html
<div class="modal-overlay" id="modalOverlay">
    <div class="modal-card">
        <button class="close-btn" id="modalCloseBtn">✕</button>
        <div class="device-name" id="modalDeviceName"></div>
        <div class="device-tag" id="modalDeviceTag"></div>
        <div class="detail-grid" id="modalDetailGrid"></div>
        <div class="av-section">
            <div class="av-title">🎬 影音支持</div>
            <div class="av-grid" id="modalAvGrid"></div>
        </div>
    </div>
</div>
3. 键盘支持
javascript
document.addEventListener('keydown', e => {
    if (e.key === 'Escape') closeModal();
});
4. 页面模糊效果
css
.container.blur {
    filter: blur(4px);
    pointer-events: none;
    user-select: none;
}
七、Toast 轻提示
javascript
function showToast(msg) {
    const el = document.getElementById('toast');
    el.textContent = msg;
    el.classList.add('show');
    clearTimeout(el._timer);
    el._timer = setTimeout(() => el.classList.remove('show'), 2000);
}
css
#toast {
    position: fixed;
    top: 20px;
    left: 50%;
    transform: translateX(-50%);
    background: rgba(0, 180, 255, 0.9);
    color: #fff;
    padding: 10px 28px;
    border-radius: 30px;
    z-index: 2000;
    opacity: 0;
    transition: opacity 0.3s ease;
    pointer-events: none;
}
#toast.show { opacity: 1; }
八、徽章系统（Badge）
徽章类型	类名	用途
通用徽章	.badge	芯片、安卓版本
电视标识	.badge-tv	标注电视产品
芯片徽章	.badge-chip	芯片名称
金色徽章	.badge-gold	千兆网口、USB 3.0
css
.badge-gold {
    background: rgba(255, 215, 0, 0.15);
    border-color: rgba(255, 215, 0, 0.3);
    color: #ffd700;
    font-weight: 600;
    box-shadow: 0 0 12px rgba(255, 215, 0, 0.1);
}
九、响应式断点
断点	调整内容
≤1100px	内边距缩小，字号减小，表格最小宽度 1000px
≤600px	页面标题 24px，双栏变单栏，隐藏部分装饰元素
十、性能优化策略
策略	实现
数据外置	data.json 独立文件，便于缓存
备份数据	JS 内嵌备份，网络失败时降级
图表懒加载	Intersection Observer 检测进入视口
图表预加载	首屏 3 秒后静默下载 ECharts
CDN 备选	主 CDN 失败时自动切换备选
预连接 CDN	<link rel="preconnect" href="https://cdn.jsdelivr.net" />
十一、完整代码结构
text
tvboxpk/
├── index.html          # 主页面（含所有样式 + JS）
├── data.json           # 数据文件（设备 + 影音支持）
└── README.md           # 项目说明
十二、典型应用场景
场景	适配说明
产品参数对比	多行多列表格 + 筛选 + 排序
评测数据看板	表格 + 图表 + 详情弹窗
设备选型工具	筛选 + 搜索 + 高亮关键指标
性能排行榜	默认按总分排序 + 金色徽章标识亮点
十三、技能来源
本次总结的 tvboxpk 页面技能，综合了以下实践：

来源	贡献内容
毛玻璃设计	深色毛玻璃容器 + 蓝紫渐变标题
数据驱动渲染	JSON 外置 + JS 动态表格渲染
ECharts 图表	懒加载/预加载 + 深色主题适配
交互模式	排序/筛选/搜索 + Toast 反馈
响应式设计	表格横向滚动 + 移动端适配
十四、深色/浅色模式切换系统
1. 核心实现方案：CSS 变量 + data-theme
原理：通过 JS 控制 <html> 或 <body> 的 data-theme 属性，CSS 根据该属性值切换变量。

css
/* 浅色主题（默认） */
:root {
    --bg-primary: #f0f4ff;
    --text-primary: #1a202c;
    --card-bg: rgba(255, 255, 255, 0.70);
    --shadow: 0 8px 30px rgba(0, 0, 0, 0.06);
}

/* 深色主题 */
[data-theme="dark"] {
    --bg-primary: #0b0f1a;
    --text-primary: #c8dbe9;
    --card-bg: rgba(12, 20, 35, 0.65);
    --shadow: 0 8px 30px rgba(0, 0, 0, 0.4);
}

/* 使用变量 */
body {
    background: var(--bg-primary);
    color: var(--text-primary);
}
.card {
    background: var(--card-bg);
    box-shadow: var(--shadow);
}
javascript
// 切换主题
function toggleTheme() {
    const html = document.documentElement;
    const current = html.getAttribute('data-theme');
    const next = current === 'dark' ? 'light' : 'dark';
    html.setAttribute('data-theme', next);
    localStorage.setItem('theme', next);
}

// 初始化：读取 localStorage 或系统偏好
function initTheme() {
    const saved = localStorage.getItem('theme');
    const prefersDark = window.matchMedia('(prefers-color-scheme: dark)').matches;
    const theme = saved || (prefersDark ? 'dark' : 'light');
    document.documentElement.setAttribute('data-theme', theme);
}
// 在 <head> 顶部执行，避免闪屏
2. 防止闪屏（FOUC）
初始化脚本必须放在 <head> 最顶部，在所有样式表之前：

html
<head>
    <script>
        // 同步读取 localStorage，立即设置 data-theme
        (function() {
            const theme = localStorage.getItem('theme') || 
                (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light');
            document.documentElement.setAttribute('data-theme', theme);
        })();
    </script>
    <link rel="stylesheet" href="styles.css" />
</head>
3. 切换按钮设计
html
<button id="themeToggle" class="theme-switch" aria-label="切换主题">
    <span class="icon-sun">☀️</span>
    <span class="icon-moon">🌙</span>
</button>
css
.theme-switch {
    background: var(--card-bg);
    border: 1px solid rgba(255,255,255,0.1);
    border-radius: 30px;
    padding: 6px 12px;
    cursor: pointer;
    transition: all 0.3s;
    display: flex;
    gap: 6px;
}
.theme-switch .icon-moon { display: none; }
[data-theme="dark"] .theme-switch .icon-sun { display: none; }
[data-theme="dark"] .theme-switch .icon-moon { display: inline; }
4. 参考案例
个人主页主题切换：页面右下角提供"月亮/太阳"按钮切换深色与浅色模式，偏好保存在 localStorage 中。

Skeuo UI 组件库：纯 CSS 变量 Token 驱动主题，data-theme="dark" 一键切换。

主题切换最佳实践：使用 data-theme 属性比 @media (prefers-color-scheme) 更可控，兼容性更好。

十五、现代拟态风格（Neumorphism）
1. 核心设计语言
新拟态（Neumorphism）以同色背景上的双向柔和阴影取代边框与渐变，营造柔和立体的界面质感。

css
/* 凸起效果 */
.neumorphic-raised {
    background: #e0e5ec;
    border-radius: 20px;
    box-shadow: 
        -8px -8px 16px rgba(255, 255, 255, 0.7),
        8px 8px 16px rgba(174, 174, 192, 0.4);
}

/* 凹陷效果 */
.neumorphic-inset {
    background: #e0e5ec;
    border-radius: 20px;
    box-shadow: 
        inset -8px -8px 16px rgba(255, 255, 255, 0.7),
        inset 8px 8px 16px rgba(174, 174, 192, 0.4);
}
2. 深色拟态
深色模式下调整阴影颜色：

css
[data-theme="dark"] .neumorphic-raised {
    background: #1a1a2e;
    box-shadow: 
        -8px -8px 16px rgba(255, 255, 255, 0.05),
        8px 8px 16px rgba(0, 0, 0, 0.6);
}
3. 参考资源
Skeuo UI：包含 73 个组件的 Vue3 新拟态组件库，内置浅色/深色双主题

dsh-neu-theme：轻拟物与磨砂玻璃主题插件，提供环境光、材质纹理和细腻微交互

十六、页面切换与过渡动画
1. 页面/视图切换动画
使用 CSS 过渡和变换实现平滑切换：

css
.page {
    opacity: 0;
    transform: translateY(20px);
    transition: all 0.4s cubic-bezier(0.23, 1, 0.32, 1);
    pointer-events: none;
}
.page.active {
    opacity: 1;
    transform: translateY(0);
    pointer-events: auto;
}
2. 路由切换（SPA 风格）
javascript
function navigateTo(pageId) {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    const target = document.getElementById(pageId);
    target.classList.add('active');
    history.pushState({ page: pageId }, '', '#' + pageId);
}
3. 滚动驱动动画（Intersection Observer）
当元素进入视口时触发动画：

javascript
const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
        if (entry.isIntersecting) {
            entry.target.classList.add('visible');
        }
    });
}, { threshold: 0.1 });

document.querySelectorAll('.animate-on-scroll').forEach(el => {
    observer.observe(el);
});
css
.animate-on-scroll {
    opacity: 0;
    transform: translateY(30px);
    transition: all 0.6s ease;
}
.animate-on-scroll.visible {
    opacity: 1;
    transform: translateY(0);
}
十七、高级字体与排版系统
1. 可变字体（Variable Fonts）
使用 Google Fonts 的可变字体，一个文件包含多种字重和样式：

html
<link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,100..900&display=swap" rel="stylesheet" />
css
body {
    font-family: 'Inter', sans-serif;
    font-variation-settings: 'opsz' 14;
}
h1 { font-weight: 800; }
p { font-weight: 400; }
2. 字体加载优化
html
<!-- 预连接 Google Fonts -->
<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />

<!-- 使用 font-display: swap 避免 FOIT -->
<style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap');
</style>
3. 响应式排版（Clamp）
css
h1 {
    font-size: clamp(28px, 5vw, 52px);
}
p {
    font-size: clamp(14px, 1.2vw, 18px);
}
十八、高级搜索与筛选系统
1. 实时搜索（防抖）
javascript
function setupSearch() {
    const input = document.getElementById('searchInput');
    let timer;
    input.addEventListener('input', function() {
        clearTimeout(timer);
        timer = setTimeout(() => {
            const query = this.value.toLowerCase();
            filterData(query);
        }, 300); // 防抖延迟 300ms
    });
}
2. 多字段搜索
javascript
function filterData(query) {
    const results = allData.filter(item => {
        return item.name.toLowerCase().includes(query) ||
               item.category.toLowerCase().includes(query) ||
               item.tags.some(tag => tag.toLowerCase().includes(query));
    });
    renderResults(results);
}
3. 搜索高亮
javascript
function highlightText(text, query) {
    if (!query) return text;
    const regex = new RegExp(`(${query})`, 'gi');
    return text.replace(regex, '<mark>$1</mark>');
}
十九、CSS 3D 与全息效果
1. 3D Transform 基础
css
.card-3d {
    perspective: 1000px;
}
.card-3d .inner {
    transform: rotateY(10deg) rotateX(5deg);
    transition: transform 0.5s ease;
}
.card-3d:hover .inner {
    transform: rotateY(0deg) rotateX(0deg);
}
2. 全息卡片效果
css
.holographic {
    background: linear-gradient(
        135deg,
        rgba(100, 200, 255, 0.1),
        rgba(200, 100, 255, 0.1)
    );
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.2);
    box-shadow: 
        0 0 30px rgba(100, 200, 255, 0.1),
        inset 0 0 30px rgba(100, 200, 255, 0.05);
    animation: holographic-shimmer 3s ease-in-out infinite;
}

@keyframes holographic-shimmer {
    0%, 100% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
}
3. 参考资源
CSS 3D Transform 可实现角色展示、全息卡片等丰富视觉效果。

二十、高级动画技术参考
1. CSS 动画性能优化
使用 transform 和 opacity 进行动画（GPU 加速）

避免动画 width、height、top、left（触发重排）

使用 will-change: transform 提示浏览器

2. 关键帧动画模板
css
@keyframes fadeInUp {
    from {
        opacity: 0;
        transform: translateY(30px);
    }
    to {
        opacity: 1;
        transform: translateY(0);
    }
}
.fade-in-up {
    animation: fadeInUp 0.6s ease forwards;
}
3. 交错动画（Stagger）
css
.stagger-children > *:nth-child(1) { animation-delay: 0.05s; }
.stagger-children > *:nth-child(2) { animation-delay: 0.15s; }
.stagger-children > *:nth-child(3) { animation-delay: 0.25s; }
/* ... 或使用 JS 动态计算 */
4. 参考资源
GSAP：专业级 JavaScript 动画库，适合复杂时间线和交错动画

Anime.js：轻量级动画库，适合 Web 动画

二十一、技能来源汇总
新增技能	参考来源
深色/浅色模式切换	CSS 变量 + data-theme 方案、Skeuo UI、个人主页实践
现代拟态风格	Skeuo UI 组件库、dsh-neu-theme
页面切换动画	CSS 过渡 + Intersection Observer
高级字体排版	Google Fonts 可变字体、Clamp 响应式排版
搜索与筛选	防抖 + 多字段搜索 + 高亮
CSS 3D 效果	CSS 3D Transform 技术
高级动画	GSAP、Anime.js、CSS 性能优化
📋 使用方式
将本技能文档发给 AI，并提示：

"按此技能，帮我生成一个 [数据主题] 的参数对比页面"

AI 即可运用上述全部规范生成可直接部署的 HTML 文件。