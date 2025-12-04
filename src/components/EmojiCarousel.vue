<template>
    <div class="mt-24px flex flex-col justify-center">
        <div
            class="w-full relative select-none overflow-hidden"
            :style="{ cursor: isDragging ? 'grabbing' : 'grab' }"
            @mousedown="startDrag"
            @mousemove="onDrag"
            @mouseup="endDrag"
            @mouseleave="endDrag"
            @touchstart="startDrag"
            @touchmove="onDrag"
            @touchend="endDrag"
        >
            <div
                class="flex justify-between items-center w-full"
                :style="{
                    transform: `translateX(${currentTranslate}px)`,
                    transition: isDragging ? 'none' : 'transform 0.5s cubic-bezier(0.34, 1.56, 0.64, 1)',
                }"
            >
                <div
                    v-for="(item, index) in visibleCards"
                    :key="item.key"
                    class="flex flex-col items-center justify-center text-white font-bold cursor-pointer transition-all duration-500"
                    :style="cardTransform(index)"
                    @click="!isDragging && goTo(item.key)"
                >
                    <div :style="cardStyle(index)">
                        <v-img :src="item.value.url" class="wh-100% flex-grow-0"></v-img>
                    </div>

                    <!-- 标签，只在中间显示 -->
                    <div
                        class="mt-8px px-8px py-4px rounded-12px bg-primary-2 text-12px fm-bold font-700 text-primary-4_5 transition-all duration-500"
                        :style="labelStyle(index)"
                    >
                        {{ item.value.text }}
                    </div>
                </div>
            </div>
        </div>

        <!-- Optional: Navigation dots -->
        <div class="flex justify-center gap-8px mt-16px">
            <div
                v-for="(card, idx) in allCards"
                :key="idx"
                class="w-8px h-8px rounded-full cursor-pointer transition-all duration-300"
                :style="{
                    background: idx === centerIndex ? 'rgba(var(--v-theme-primary-4_5), 1)' : 'rgba(255, 255, 255, 0.3)',
                    transform: idx === centerIndex ? 'scale(1.2)' : 'scale(1)',
                }"
                @click="goTo(idx)"
            ></div>
        </div>
    </div>
</template>

<script setup>
import { ref, computed, watch, defineProps, defineEmits, onMounted, onUnmounted } from 'vue'
import f0 from '@/assets/images/profile/0.png'
import f1 from '@/assets/images/profile/1.png'
import f2 from '@/assets/images/profile/2.png'
import f3 from '@/assets/images/profile/3.png'
import f4 from '@/assets/images/profile/4.png'

const props = defineProps({
    modelValue: { type: Number, default: 2 },
})
const emit = defineEmits(['update:modelValue'])

const allCards = ref([
    { url: f0, text: 'Awful' },
    { url: f1, text: 'Bad' },
    { url: f2, text: 'Okay' },
    { url: f3, text: 'God' },
    { url: f4, text: 'Great' },
])

const centerIndex = ref(props.modelValue)

// 监听外部 v-model 改变
watch(
    () => props.modelValue,
    val => {
        if (val != null && val >= 0 && val < allCards.value.length) {
            centerIndex.value = val
        }
    }
)

// 拖拽相关 - 增强版
const isDragging = ref(false)
const startX = ref(0)
const startTime = ref(0)
const currentTranslate = ref(0)
const previousTranslate = ref(0)
const velocity = ref(0)
const animationId = ref(null)
const dragDistance = ref(0)

// 卡片间距 (根据屏幕尺寸动态计算)
const CARD_SPACING = ref(100)

onMounted(() => {
    updateCardSpacing()
    window.addEventListener('resize', updateCardSpacing)
})

onUnmounted(() => {
    window.removeEventListener('resize', updateCardSpacing)
    if (animationId.value) {
        cancelAnimationFrame(animationId.value)
    }
})

function updateCardSpacing() {
    const width = window.innerWidth
    CARD_SPACING.value = width < 640 ? 80 : 100
}

const visibleCards = computed(() => {
    const result = []
    const len = allCards.value.length
    for (let i = -2; i <= 2; i++) {
        let idx = (centerIndex.value + i + len) % len
        result.push({
            key: idx,
            value: allCards.value[idx],
        })
    }
    return result
})

const W_LARGE = 68
const W_SMALL = 44

// 计算卡片位置变换 - 根据拖拽距离动态调整
function cardTransform(i) {
    const progress = dragDistance.value / CARD_SPACING.value
    const baseOffset = (i - 2) * CARD_SPACING.value

    return {
        transform: `translateX(${baseOffset}px)`,
    }
}

const cardStyle = i => {
    // 计算与中心的距离 (考虑拖拽偏移)
    const progress = Math.abs(dragDistance.value / CARD_SPACING.value)
    const distanceFromCenter = Math.abs(i - 2) - progress

    const isCenter = Math.abs(distanceFromCenter) < 0.5
    const scale = isCenter ? 1 : 0.7

    return {
        width: `${isCenter ? W_LARGE : W_SMALL}px`,
        padding: '10px',
        borderRadius: '50px',
        filter: isCenter ? '' : 'grayscale(100%)',
        background: 'rgba(var(--v-theme-primary-4_5),1)',
        transform: `scale(${scale})`,
        transition: isDragging.value ? 'all 0.2s ease-out' : 'all 0.5s cubic-bezier(0.34, 1.56, 0.64, 1)',
        boxShadow: isCenter ? '0px 0px 138.4px #95FE3F' : 'none',
    }
}

const labelStyle = i => {
    const progress = Math.abs(dragDistance.value / CARD_SPACING.value)
    const distanceFromCenter = Math.abs(i - 2) - progress
    const isCenter = Math.abs(distanceFromCenter) < 0.5

    return {
        opacity: isCenter ? 1 : 0,
        transform: isCenter ? 'scale(1) translateY(0)' : 'scale(0.5) translateY(-10px)',
        pointerEvents: isCenter ? 'auto' : 'none',
        transition: isDragging.value ? 'all 0.2s ease-out' : 'all 0.5s cubic-bezier(0.34, 1.56, 0.64, 1)',
    }
}

// 点击切换到指定索引
function goTo(idx) {
    if (isDragging.value) return

    centerIndex.value = idx
    emit('update:modelValue', idx)

    // 重置拖拽状态
    currentTranslate.value = 0
    previousTranslate.value = 0
    dragDistance.value = 0
}

// 切换到上一个
function prev() {
    centerIndex.value = (centerIndex.value - 1 + allCards.value.length) % allCards.value.length
    emit('update:modelValue', centerIndex.value)
    resetDragState()
}

// 切换到下一个
function next() {
    centerIndex.value = (centerIndex.value + 1) % allCards.value.length
    emit('update:modelValue', centerIndex.value)
    resetDragState()
}

function resetDragState() {
    currentTranslate.value = 0
    previousTranslate.value = 0
    dragDistance.value = 0
    velocity.value = 0
}

// 拖拽事件 - 增强版
function startDrag(e) {
    if (e.target.closest('.cursor-pointer') && e.type === 'mousedown') {
        // 允许点击事件
        return
    }

    isDragging.value = true
    startX.value = e.type.includes('touch') ? e.touches[0].clientX : e.clientX
    startTime.value = Date.now()
    velocity.value = 0

    if (animationId.value) {
        cancelAnimationFrame(animationId.value)
    }

    e.preventDefault()
}

function onDrag(e) {
    if (!isDragging.value) return

    const currentX = e.type.includes('touch') ? e.touches[0].clientX : e.clientX
    const deltaX = currentX - startX.value
    const currentTime = Date.now()
    const deltaTime = currentTime - startTime.value

    // 计算速度
    if (deltaTime > 0) {
        velocity.value = deltaX / deltaTime
    }

    // 更新位置
    currentTranslate.value = previousTranslate.value + deltaX
    dragDistance.value = deltaX

    e.preventDefault()
}

function endDrag(e) {
    if (!isDragging.value) return

    isDragging.value = false

    const threshold = CARD_SPACING.value * 0.3 // 30% 的卡片间距作为阈值
    const velocityThreshold = 0.5 // 速度阈值

    // 根据拖拽距离和速度决定是否切换
    let shouldSwitch = false
    let direction = 0

    if (Math.abs(velocity.value) > velocityThreshold) {
        // 快速滑动：根据速度方向切换
        direction = velocity.value > 0 ? -1 : 1
        shouldSwitch = true
    } else if (Math.abs(dragDistance.value) > threshold) {
        // 慢速拖拽：根据距离切换
        direction = dragDistance.value > 0 ? -1 : 1
        shouldSwitch = true
    }

    if (shouldSwitch) {
        if (direction > 0) {
            next()
        } else {
            prev()
        }
    } else {
        // 回弹到原位
        animateSnapBack()
    }
}

// 回弹动画
function animateSnapBack() {
    const startTranslate = currentTranslate.value
    const startDistance = dragDistance.value
    const duration = 300
    const startTimestamp = Date.now()

    function animate() {
        const elapsed = Date.now() - startTimestamp
        const progress = Math.min(elapsed / duration, 1)

        // 使用缓动函数
        const eased = easeOutCubic(progress)

        currentTranslate.value = startTranslate * (1 - eased)
        dragDistance.value = startDistance * (1 - eased)

        if (progress < 1) {
            animationId.value = requestAnimationFrame(animate)
        } else {
            resetDragState()
        }
    }

    animate()
}

// 缓动函数
function easeOutCubic(t) {
    return 1 - Math.pow(1 - t, 3)
}
</script>

<style scoped>
/* 防止文本选择 */
.select-none {
    user-select: none;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
}

/* 平滑的过渡 */
.transition-all {
    transition-property: all;
    transition-timing-function: cubic-bezier(0.4, 0, 0.2, 1);
}

/* 自定义滚动条（如果需要） */
::-webkit-scrollbar {
    display: none;
}
</style>
