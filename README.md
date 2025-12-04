# 承霖的图表展示网站

这是一个基于 Vue 3 和 Swiper 的图表轮播展示组件项目。

## 功能特点

- ✨ 支持 5 个图表的轮播展示
- 🎯 使用 Swiper 实现流畅的滑动切换效果
- 📱 响应式设计，支持移动端和桌面端
- 🎨 美观的 UI 设计和过渡动画
- ⚡ 基于 Vue 3 + Vite 构建，快速开发

## 快速开始

### 安装依赖

```bash
npm install
```

### 开发模式

```bash
npm run dev
```

访问 http://localhost:3000 查看效果

### 构建生产版本

```bash
npm run build
```

构建后的文件在 `dist` 目录中。

### 预览生产版本

```bash
npm run preview
```

## 组件使用说明

### ChartCarousel 组件

这是核心的图表轮播组件，位于 `src/components/ChartCarousel.vue`

#### Props

- `title` (String): 轮播图标题，默认为 "数据图表展示"
- `charts` (Array): 图表数据数组，每个对象包含：
  - `title`: 图表标题
  - `description`: 图表描述

#### 示例用法

```vue
<template>
  <ChartCarousel
    title="数据可视化图表"
    :charts="chartData"
  />
</template>

<script setup>
import { ref } from 'vue';
import ChartCarousel from './components/ChartCarousel.vue';

const chartData = ref([
  {
    title: '图表 1',
    description: '第一个图表的内容'
  },
  {
    title: '图表 2',
    description: '第二个图表的内容'
  },
  // ... 更多图表
]);
</script>
```

## 自定义图表内容

要替换示例内容为真实的图表，请修改 `ChartCarousel.vue` 中的 chart-placeholder 部分：

```vue
<div class="chart-placeholder">
  <!-- 替换为你的图表组件，例如 ECharts、Chart.js 等 -->
  <YourChartComponent :data="chart.data" />
</div>
```

## 技术栈

- Vue 3 - 渐进式 JavaScript 框架
- Vite - 下一代前端构建工具
- Swiper - 现代化的移动端触摸滑动库
- JavaScript ES6+

## 项目结构

```
.
├── src/
│   ├── components/
│   │   └── ChartCarousel.vue  # 图表轮播组件
│   ├── App.vue                # 主应用组件
│   └── main.js                # 应用入口
├── index.html                 # HTML 模板
├── vite.config.js            # Vite 配置
├── package.json              # 项目依赖
└── README.md                 # 项目说明

```

## 浏览器支持

- Chrome (最新版)
- Firefox (最新版)
- Safari (最新版)
- Edge (最新版)

## License

MIT