<template>
  <div class="chart-carousel-container">
    <h2 class="carousel-title">{{ title }}</h2>

    <swiper
      :modules="modules"
      :slides-per-view="1"
      :space-between="30"
      :navigation="true"
      :pagination="{ clickable: true }"
      :loop="true"
      :autoplay="{
        delay: 3000,
        disableOnInteraction: false,
      }"
      class="chart-swiper"
      @swiper="onSwiper"
      @slideChange="onSlideChange"
    >
      <swiper-slide v-for="(chart, index) in charts" :key="index">
        <div class="chart-slide">
          <div class="chart-content">
            <h3>{{ chart.title }}</h3>
            <div class="chart-placeholder">
              <!-- Replace this with your actual chart component -->
              <div class="chart-demo">
                {{ chart.description }}
              </div>
            </div>
          </div>
        </div>
      </swiper-slide>
    </swiper>

    <div class="slide-indicator">
      当前: {{ currentSlide + 1 }} / {{ charts.length }}
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { Swiper, SwiperSlide } from 'swiper/vue';
import { Navigation, Pagination, Autoplay } from 'swiper/modules';

// Import Swiper styles
import 'swiper/css';
import 'swiper/css/navigation';
import 'swiper/css/pagination';

const props = defineProps({
  title: {
    type: String,
    default: '数据图表展示'
  },
  charts: {
    type: Array,
    default: () => [
      {
        title: '图表 1',
        description: '这里是第一个图表的内容区域'
      },
      {
        title: '图表 2',
        description: '这里是第二个图表的内容区域'
      },
      {
        title: '图表 3',
        description: '这里是第三个图表的内容区域'
      },
      {
        title: '图表 4',
        description: '这里是第四个图表的内容区域'
      },
      {
        title: '图表 5',
        description: '这里是第五个图表的内容区域'
      }
    ]
  }
});

const modules = [Navigation, Pagination, Autoplay];
const currentSlide = ref(0);
const swiperInstance = ref(null);

const onSwiper = (swiper) => {
  swiperInstance.value = swiper;
};

const onSlideChange = (swiper) => {
  currentSlide.value = swiper.realIndex;
};
</script>

<style scoped>
.chart-carousel-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

.carousel-title {
  text-align: center;
  font-size: 32px;
  margin-bottom: 30px;
  color: #333;
}

.chart-swiper {
  width: 100%;
  height: 500px;
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
}

.chart-slide {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-radius: 12px;
}

.chart-content {
  width: 100%;
  height: 100%;
  padding: 40px;
  color: white;
}

.chart-content h3 {
  font-size: 28px;
  margin-bottom: 20px;
  text-align: center;
}

.chart-placeholder {
  width: 100%;
  height: calc(100% - 60px);
  background: rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  backdrop-filter: blur(10px);
}

.chart-demo {
  font-size: 20px;
  text-align: center;
  padding: 20px;
}

.slide-indicator {
  text-align: center;
  margin-top: 20px;
  font-size: 18px;
  color: #666;
  font-weight: 500;
}

/* Custom Swiper navigation buttons */
:deep(.swiper-button-next),
:deep(.swiper-button-prev) {
  color: white;
  background: rgba(0, 0, 0, 0.5);
  width: 50px;
  height: 50px;
  border-radius: 50%;
}

:deep(.swiper-button-next):after,
:deep(.swiper-button-prev):after {
  font-size: 20px;
}

:deep(.swiper-pagination-bullet) {
  background: white;
  opacity: 0.5;
  width: 12px;
  height: 12px;
}

:deep(.swiper-pagination-bullet-active) {
  opacity: 1;
  background: white;
}

/* Responsive design */
@media (max-width: 768px) {
  .chart-swiper {
    height: 400px;
  }

  .chart-content {
    padding: 20px;
  }

  .carousel-title {
    font-size: 24px;
  }
}
</style>
