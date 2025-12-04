<template>
  <div id="app">
    <header>
      <h1>承霖的图表展示</h1>
    </header>

    <main>
      <ChartCarousel
        title="数据可视化图表"
        :charts="chartData"
      />
    </main>

    <footer>
      <p>© 2024 承霖的网站</p>
    </footer>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import ChartCarousel from './components/ChartCarousel.vue';

const chartData = ref([
  {
    title: '销售数据统计',
    description: '这里可以放置销售数据的图表组件'
  },
  {
    title: '用户增长趋势',
    description: '这里可以放置用户增长趋势的图表组件'
  },
  {
    title: '产品分布分析',
    description: '这里可以放置产品分布分析的图表组件'
  },
  {
    title: '市场占有率',
    description: '这里可以放置市场占有率的图表组件'
  },
  {
    title: '收益对比图',
    description: '这里可以放置收益对比的图表组件'
  }
]);
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  min-height: 100vh;
}

#app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

header {
  background: rgba(255, 255, 255, 0.95);
  padding: 20px;
  text-align: center;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

header h1 {
  color: #333;
  font-size: 36px;
}

main {
  flex: 1;
  padding: 40px 20px;
}

footer {
  background: rgba(255, 255, 255, 0.95);
  padding: 20px;
  text-align: center;
  color: #666;
}

@media (max-width: 768px) {
  header h1 {
    font-size: 24px;
  }

  main {
    padding: 20px 10px;
  }
}
</style>
