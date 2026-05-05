# 蓝鲸动力官网 - 代码架构文档

## 1. 项目概述

本项目是**无锡蓝鲸动力科技有限公司**的官方网站，属于纯前端的单页应用（SPA），用于展示公司介绍、核心技术、产品中心、应用场景、新闻动态以及联系方式等信息。网站采用深色主题 + 毛玻璃拟态设计风格，所有数据均以静态常量形式内嵌于组件中，无需后端服务支撑。

---

## 2. 目录结构

```
bluewhale-website/
├── dist/                          # 构建输出目录（Vercel 部署来源）
│   ├── assets/                    # 静态资源（JS/CSS，含 hash 文件名）
│   └── index.html                 # 构建后入口 HTML
├── src/                           # 源码目录
│   ├── components/                # 公共组件
│   │   ├── Navbar.tsx             # 顶部导航栏（含桌面/移动端菜单、下拉子菜单）
│   │   ├── Footer.tsx             # 页脚（公司信息、链接分组、版权声明）
│   │   ├── PageTransition.tsx     # 页面切换过渡动画包裹组件
│   │   └── ScrollToTop.tsx        # 路由切换时自动滚动至顶部
│   ├── pages/                     # 页面组件
│   │   ├── Home.tsx               # 首页（Hero、核心优势、产品概览、应用场景、新闻预览、CTA）
│   │   ├── About.tsx              # 关于我们（企业简介、愿景使命、发展历程、核心业务、战略布局）
│   │   ├── Technology.tsx         # 核心技术（技术体系、核心专利、荣誉奖项、技术对比表）
│   │   ├── Products.tsx           # 产品中心（分类筛选、产品网格、产品详情弹窗）
│   │   ├── Applications.tsx       # 应用场景（领域筛选、案例网格、案例详情弹窗）
│   │   ├── News.tsx               # 新闻动态（分类筛选、新闻列表、新闻详情弹窗）
│   │   └── Contact.tsx            # 联系我们（联系信息、在线表单、FAQ）
│   ├── App.tsx                    # 应用根组件（路由配置、全局布局）
│   ├── main.tsx                   # 应用入口（ReactDOM 挂载、BrowserRouter 注入）
│   ├── index.css                  # 全局样式（Tailwind 指令 + 自定义样式类）
│   └── vite-env.d.ts              # Vite 环境类型声明
├── index.html                     # HTML 入口模板（SEO 元信息、字体加载）
├── package.json                   # 项目依赖与脚本
├── vite.config.ts                 # Vite 构建配置（别名、代码分割、Source Map）
├── tailwind.config.js             # Tailwind CSS 配置（自定义颜色、字体、动画）
├── postcss.config.js              # PostCSS 配置（Tailwind + Autoprefixer）
├── tsconfig.json                  # TypeScript 编译配置
├── tsconfig.node.json             # Node 环境 TypeScript 配置
└── vercel.json                    # Vercel 部署配置
```

---

## 3. 技术栈

| 类别 | 技术 | 版本 | 用途 |
|------|------|------|------|
| **UI 框架** | React | ^18.2.0 | 声明式组件化 UI 开发 |
| **类型系统** | TypeScript | ^5.2.2 | 静态类型检查，提升代码健壮性 |
| **构建工具** | Vite | ^5.1.4 | 开发服务器 + 生产构建，HMR 热更新 |
| **路由** | React Router DOM | ^6.22.0 | 客户端 SPA 路由管理 |
| **动画** | Framer Motion | ^11.0.0 | 页面过渡、滚动动画、交互反馈 |
| **图标** | Lucide React | ^0.344.0 | 轻量级 SVG 图标库 |
| **样式** | Tailwind CSS | ^3.4.1 | 原子化 CSS 框架 |
| **CSS 后处理** | PostCSS + Autoprefixer | ^8.4.35 / ^10.4.18 | 自动添加浏览器前缀 |
| **部署** | Vercel | - | 静态站点托管与自动部署 |

---

## 4. 架构设计

### 4.1 应用启动流程

```
index.html
  └─ <script type="module" src="/src/main.tsx">
       └─ ReactDOM.createRoot(#root)
            └─ <React.StrictMode>
                 └─ <BrowserRouter>        ← 路由容器
                      └─ <App />           ← 应用根组件
```

### 4.2 全局布局结构

`App.tsx` 定义了所有页面共享的布局骨架：

```
┌──────────────────────────────────────┐
│  ScrollToTop（路由切换自动回顶）       │  ← 无 UI 渲染
├──────────────────────────────────────┤
│  Navbar（固定定位，滚动透明→毛玻璃）    │  ← z-50，始终可见
├──────────────────────────────────────┤
│                                      │
│  <main>                              │
│    <Routes>                          │
│      └─ 各页面组件                     │  ← 路由匹配渲染
│    </Routes>                         │
│  </main>                             │
│                                      │
├──────────────────────────────────────┤
│  Footer                              │  ← 页面底部
└──────────────────────────────────────┘
```

### 4.3 路由映射

| 路径 | 页面组件 | 说明 |
|------|----------|------|
| `/` | `Home` | 首页 |
| `/about` | `About` | 关于我们 |
| `/technology` | `Technology` | 核心技术 |
| `/products` | `Products` | 产品中心 |
| `/applications` | `Applications` | 应用场景 |
| `/news` | `News` | 新闻动态 |
| `/contact` | `Contact` | 联系我们 |

其中 `/products` 和 `/applications` 页面支持 URL Search Params 进行分类筛选：
- `/products?category=land|sea|mobile`
- `/applications?field=transport|ocean|industry`

---

## 5. 模块职责详解

### 5.1 公共组件层 (`src/components/`)

#### Navbar
- **功能**：顶部全局导航栏，固定于页面顶部
- **状态管理**：
  - `isScrolled` — 监听滚动事件，控制导航栏背景（透明 → 毛玻璃效果）
  - `isMobileMenuOpen` — 移动端侧边菜单开关
  - `activeDropdown` — 桌面端下拉子菜单当前激活项
- **导航数据**：通过 `navItems` 常量定义，支持二级菜单（`children` 字段）
- **响应式**：桌面端水平导航 + 悬停下拉；移动端汉堡菜单 + 侧边抽屉
- **动画**：使用 Framer Motion 的 `AnimatePresence` 实现下拉菜单与移动端菜单的进出动画

#### Footer
- **功能**：页面底部信息区域
- **内容**：公司简介与联系信息、产品链接、应用场景链接、公司页面链接、版权声明
- **链接数据**：通过 `footerLinks` 常量定义，与 Navbar 保持一致的链接结构

#### PageTransition
- **功能**：页面切换时的淡入上移动画包裹组件
- **实现**：Framer Motion `motion.div`，设置 `initial`、`animate`、`exit` 动画状态
- **使用方式**：每个页面组件的根元素均被 `<PageTransition>` 包裹

#### ScrollToTop
- **功能**：路由路径变化时自动平滑滚动至页面顶部
- **实现**：监听 `useLocation()` 的 `pathname` 变化，调用 `window.scrollTo()`
- **无 UI**：组件返回 `null`，仅产生副作用

### 5.2 页面组件层 (`src/pages/`)

#### Home — 首页
- **区块**：Hero 区域 → 核心优势 → 产品概览 → 应用场景 → 新闻预览 → CTA 行动号召
- **数据**：各区块的展示数据均以数组常量形式定义在组件内部
- **动画模式**：统一使用 `fadeInUp` 变体实现滚动触发动画，`staggerContainer`/`staggerItem` 实现列表交错动画

#### About — 关于我们
- **区块**：Hero → 企业简介 → 愿景使命 → 发展历程 → 核心业务 → 战略布局
- **特点**：发展历程使用时间线布局；战略布局聚焦海洋工程与低空经济两大方向

#### Technology — 核心技术
- **区块**：Hero → 技术体系 → 核心专利 → 荣誉奖项 → 技术对比表
- **数据**：`technologies`（3 大核心技术）、`patents`（6 件专利）、`awards`（3 项荣誉）
- **技术对比表**：以 HTML `<table>` 展示蓝鲸无线供电 vs 传统有线充电 vs 电池更换的对比

#### Products — 产品中心
- **交互模式**：分类筛选 → 网格浏览 → 弹窗查看详情
- **状态管理**：
  - `activeCategory` — 从 URL Search Params 读取当前筛选分类
  - `selectedProduct` — 当前选中查看详情的产品（`null` 表示弹窗关闭）
- **数据模型**：`Product` 接口定义了 `id`、`name`、`category`、`power`、`features`、`specs`、`applications` 等字段
- **9 款产品**：分属 land（陆上 3 款）、sea（海下 3 款）、mobile（移动式 3 款）三大类

#### Applications — 应用场景
- **交互模式**：领域筛选 → 案例网格 → 弹窗查看详情
- **与 Products 结构类似**：同样使用 `useSearchParams` 驱动筛选，`useState` 管理弹窗状态
- **数据模型**：`CaseStudy` 接口定义了 `challenge`（痛点）、`solution`（方案）、`result`（效果）等业务字段
- **6 个案例**：分属 transport（2 个）、ocean（2 个）、industry（2 个）三大领域

#### News — 新闻动态
- **交互模式**：分类筛选（组件内 `useState`）→ 新闻列表 → 弹窗查看全文
- **与 Products/Applications 的区别**：分类筛选不使用 URL 参数，而是组件内部状态
- **6 篇新闻**：分为公司新闻、项目动态、行业资讯、产品发布、技术动态五类

#### Contact — 联系我们
- **区块**：联系信息 → 在线咨询表单 → 常见问题（FAQ）
- **表单状态**：`formData`（6 个字段）+ `isSubmitted`（提交成功标识）
- **表单提交**：当前为模拟提交（`setTimeout` 模拟），3 秒后重置表单

---

## 6. 数据流向

### 6.1 整体数据流

本项目为纯静态展示站点，**无后端 API 调用**，所有数据内嵌于各页面组件中：

```
常量数据（组件内定义）
    ↓
页面组件渲染
    ↓
用户交互（筛选 / 点击查看详情）
    ↓
本地状态更新（useState / useSearchParams）
    ↓
UI 重新渲染
```

### 6.2 关键交互数据流

#### 产品/应用场景筛选

```
URL Search Params (?category=land)
        ↓
  useSearchParams() 读取
        ↓
  计算 filteredProducts / filteredCases
        ↓
  AnimatePresence 渲染筛选后的列表
```

#### 详情弹窗

```
用户点击卡片
        ↓
  setSelectedProduct(product) / setSelectedCase(caseItem)
        ↓
  条件渲染 Modal（AnimatePresence 包裹）
        ↓
  用户点击关闭 / 遮罩层
        ↓
  setSelectedProduct(null) / setSelectedCase(null)
```

#### 导航栏状态

```
window.scroll 事件
        ↓
  setIsScrolled(window.scrollY > 20)
        ↓
  导航栏样式切换（透明 ↔ 毛玻璃背景）

useLocation() 变化
        ↓
  关闭移动端菜单 + 重置下拉状态
        ↓
  高亮当前路由对应的导航项
```

---

## 7. 设计系统

### 7.1 自定义颜色

在 `tailwind.config.js` 中定义了三组语义化颜色：

| 色组 | 用途 | 关键色值 |
|------|------|----------|
| `primary` | 品牌主色（蓝色系） | `500: #0066ff` |
| `ocean` | 海洋辅助色（青色系） | `500: #00afd7` |
| `dark` | 深色背景 | `900: #0a1628`（最深）、`800: #0f1d32`、`700: #162544`、`600: #1e3055` |

### 7.2 自定义 CSS 类（`index.css` @layer components）

| 类名 | 作用 |
|------|------|
| `.section-container` | 内容区水平内边距，响应式适配 |
| `.section-padding` | 区块垂直内边距（py-16 → py-24） |
| `.gradient-text` | 文字渐变效果（ocean-400 → primary-400） |
| `.glass-card` | 毛玻璃卡片（半透明深色背景 + 模糊 + 边框 + 圆角） |
| `.hover-lift` | 悬停上浮效果（上移 + 阴影 + 过渡） |
| `.btn-primary` | 主按钮（渐变背景 + 悬停缩放阴影） |
| `.btn-outline` | 描边按钮（边框 + 悬停填充） |

### 7.3 动画

| 动画名 | 定义位置 | 效果 |
|--------|----------|------|
| `fade-in` | Tailwind 配置 | 0.5s 淡入 |
| `slide-up` | Tailwind 配置 | 0.5s 上移淡入 |
| `pulse-slow` | Tailwind 配置 | 3s 慢速脉冲 |
| `fadeInUp` | 页面组件内 | 滚动触发淡入上移（Framer Motion `whileInView`） |
| `staggerItem` | 页面组件内 | 列表项交错出现 |

---

## 8. 构建与部署

### 8.1 构建配置（`vite.config.ts`）

- **路径别名**：`@/` → `./src/`，需与 `tsconfig.json` 中 `paths` 配置保持一致
- **代码分割**（`manualChunks`）：
  - `vendor` — React、ReactDOM、React Router DOM
  - `animation` — Framer Motion
- **Source Map**：生产构建开启 `sourcemap: true`
- **输出目录**：`dist/`

### 8.2 构建脚本

```bash
npm run dev       # 启动 Vite 开发服务器（HMR）
npm run build     # TypeScript 编译 + Vite 生产构建
npm run preview   # 预览生产构建结果
npm run lint      # ESLint 代码检查
```

### 8.3 Vercel 部署

`vercel.json` 配置：
- `buildCommand`: `npm run build`
- `outputDirectory`: `dist`
- `framework`: `vite`

Vercel 会自动识别 `vercel.json`，每次推送到主分支后自动触发构建和部署。SPA 路由需确保 Vercel 将所有路径重写到 `index.html`（Vercel 对 Vite 框架默认支持）。

---

## 9. 代码约定

### 9.1 命名规范

- **组件文件**：PascalCase（如 `Navbar.tsx`、`PageTransition.tsx`）
- **组件函数**：PascalCase，使用 `export default function` 导出
- **常量数据**：camelCase（如 `navItems`、`products`、`caseStudies`）
- **接口/类型**：PascalCase（如 `Product`、`CaseStudy`、`NewsItem`）

### 9.2 组件结构模式

页面组件普遍遵循以下结构：

```tsx
import { motion } from 'framer-motion';
import { ... } from 'lucide-react';
import PageTransition from '../components/PageTransition';

// 1. 动画变体定义
const fadeInUp = { ... };

// 2. 数据常量定义
const data = [ ... ];

// 3. 组件导出
export default function PageName() {
  // 4. 状态声明
  const [state, setState] = useState(...);

  // 5. 派生数据计算
  const filteredData = ...;

  // 6. JSX 返回
  return (
    <PageTransition>
      {/* Hero 区块 */}
      <section>...</section>
      {/* 内容区块 */}
      <section>...</section>
      {/* 弹窗（可选） */}
      <AnimatePresence>...</AnimatePresence>
    </PageTransition>
  );
}
```

### 9.3 样式约定

- 优先使用 Tailwind 原子类，避免内联样式
- 复用 `index.css` 中定义的自定义组件类（`.glass-card`、`.btn-primary` 等）
- 响应式断点遵循 Tailwind 默认配置：`sm:640px`、`md:768px`、`lg:1024px`、`xl:1280px`、`2xl:1536px`

---

## 10. 已知局限与优化方向

| 层面 | 现状 | 优化建议 |
|------|------|----------|
| **数据管理** | 所有数据硬编码在组件内 | 可抽取为独立数据文件或接入 CMS/Headless API |
| **SEO** | 客户端渲染，搜索引擎可索引性有限 | 可引入 SSR（如 Next.js）或预渲染方案 |
| **表单提交** | 模拟提交，无实际后端 | 接入表单服务（如 Formspree、自建 API） |
| **地图展示** | 联系页地图为占位符 | 接入地图 SDK（如高德/腾讯地图） |
| **图片资源** | 产品/新闻图片使用图标占位 | 补充实际产品图片和新闻配图 |
| **国际化** | 仅支持中文 | 可引入 react-i18next 支持多语言 |
| **无障碍** | 未系统处理 ARIA 属性 | 增加语义化标签和 ARIA 属性 |
