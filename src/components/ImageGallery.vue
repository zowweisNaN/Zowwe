<script setup lang="ts">
import { ref, onUnmounted } from 'vue'
import { ChevronLeft, ChevronRight } from '@lucide/vue'

const props = defineProps<{
  images: string[]
  title: string
}>()

const activeIndex = ref(0)
const loadedImages = ref<Record<number, boolean>>({})

function onImgLoad(idx: number) {
  loadedImages.value[idx] = true
}

const isDragging = ref(false)
const dragStartX = ref(0)
const dragOffset = ref(0)

function setActive(i: number) {
  if (i < 0) i = props.images.length - 1
  if (i >= props.images.length) i = 0
  activeIndex.value = i
}

function prevImage() {
  setActive(activeIndex.value - 1)
}

function nextImage() {
  setActive(activeIndex.value + 1)
}

// ── Mouse Drag Handlers ──────────────────────────────────────────────────────
function onMouseDown(e: MouseEvent) {
  if (props.images.length <= 1) return
  e.preventDefault()
  isDragging.value = true
  dragStartX.value = e.clientX
  dragOffset.value = 0

  window.addEventListener('mousemove', handleGlobalMouseMove)
  window.addEventListener('mouseup', handleGlobalMouseUp)
}

function handleGlobalMouseMove(e: MouseEvent) {
  if (!isDragging.value) return
  dragOffset.value = e.clientX - dragStartX.value
}

function handleGlobalMouseUp() {
  if (!isDragging.value) return
  finishDrag()
  cleanupGlobalMouse()
}

function cleanupGlobalMouse() {
  window.removeEventListener('mousemove', handleGlobalMouseMove)
  window.removeEventListener('mouseup', handleGlobalMouseUp)
}

// ── Touch Drag Handlers ──────────────────────────────────────────────────────
function onTouchStart(e: TouchEvent) {
  if (props.images.length <= 1 || e.touches.length === 0) return
  isDragging.value = true
  dragStartX.value = e.touches[0].clientX
  dragOffset.value = 0

  window.addEventListener('touchmove', handleGlobalTouchMove, { passive: false })
  window.addEventListener('touchend', handleGlobalTouchEnd)
}

function handleGlobalTouchMove(e: TouchEvent) {
  if (!isDragging.value || e.touches.length === 0) return
  const diff = e.touches[0].clientX - dragStartX.value
  if (Math.abs(diff) > 5 && e.cancelable) {
    e.preventDefault()
  }
  dragOffset.value = diff
}

function handleGlobalTouchEnd() {
  if (!isDragging.value) return
  finishDrag()
  cleanupGlobalTouch()
}

function cleanupGlobalTouch() {
  window.removeEventListener('touchmove', handleGlobalTouchMove)
  window.removeEventListener('touchend', handleGlobalTouchEnd)
}

function finishDrag() {
  const threshold = 35
  if (dragOffset.value < -threshold) {
    nextImage()
  } else if (dragOffset.value > threshold) {
    prevImage()
  }
  isDragging.value = false
  dragOffset.value = 0
}

onUnmounted(() => {
  cleanupGlobalMouse()
  cleanupGlobalTouch()
})
</script>

<template>
  <div class="flex flex-col gap-3">
    <!-- Main Image Viewport (Horizontal Draggable Carousel) -->
    <div
      class="relative aspect-square rounded-2xl overflow-hidden bg-slate-grey/10 shadow-inner select-none"
      :class="{ 'cursor-grab active:cursor-grabbing': images.length > 1 }"
      @mousedown="onMouseDown"
      @touchstart="onTouchStart"
    >
      <!-- Track Container -->
      <div
        class="flex h-full w-full pointer-events-none"
        :style="{
          transform: `translateX(calc(-${activeIndex * 100}% + ${isDragging ? dragOffset : 0}px))`,
          transition: isDragging ? 'none' : 'transform 300ms cubic-bezier(0.16, 1, 0.3, 1)'
        }"
      >
        <img
          v-for="(img, idx) in images"
          :key="idx"
          :src="img"
          :alt="`${title} - image ${idx + 1}`"
          class="h-full w-full object-cover shrink-0 select-none pointer-events-none transition-opacity duration-300"
          :class="loadedImages[idx] ? 'opacity-100' : 'opacity-0'"
          draggable="false"
          @load="onImgLoad(idx)"
        />
      </div>

      <!-- Skeleton Loader -->
      <div
        v-if="!loadedImages[activeIndex]"
        class="absolute inset-0 bg-gradient-to-r from-slate-grey/10 via-warm-sand/20 to-slate-grey/10 bg-[length:200%_100%] animate-shimmer pointer-events-none"
      />

      <!-- Navigation Arrows (Only when > 1 image) -->
      <template v-if="images.length > 1">
        <button
          @click.stop="prevImage"
          class="absolute left-2.5 top-1/2 -translate-y-1/2 h-9 w-9 rounded-full bg-slate-deep/70 hover:bg-slate-deep text-white backdrop-blur-xs transition-all flex items-center justify-center z-20 shadow-md focus:outline-none"
          aria-label="Previous image"
        >
          <ChevronLeft class="h-5 w-5" />
        </button>

        <button
          @click.stop="nextImage"
          class="absolute right-2.5 top-1/2 -translate-y-1/2 h-9 w-9 rounded-full bg-slate-deep/70 hover:bg-slate-deep text-white backdrop-blur-xs transition-all flex items-center justify-center z-20 shadow-md focus:outline-none"
          aria-label="Next image"
        >
          <ChevronRight class="h-5 w-5" />
        </button>
      </template>

      <!-- Counter Pill -->
      <span class="absolute bottom-3 right-3 bg-slate-deep/75 text-soft-cream text-xs font-semibold px-3 py-1 rounded-full shadow-md z-10 pointer-events-none">
        {{ activeIndex + 1 }} / {{ images.length }}
      </span>
    </div>

    <!-- Thumbnails -->
    <div v-if="images.length > 1" class="flex gap-2.5 overflow-x-auto pb-1 pt-1">
      <button
        v-for="(img, i) in images"
        :key="i"
        class="flex-shrink-0 h-16 w-16 rounded-xl overflow-hidden border-2 transition-all duration-150 focus:outline-none"
        :class="[
          i === activeIndex
            ? 'border-slate-deep scale-105 shadow-sm'
            : 'border-transparent hover:border-slate-grey/50 opacity-70 hover:opacity-100'
        ]"
        @click="setActive(i)"
        :aria-label="`View image ${i + 1}`"
      >
        <img :src="img" :alt="`${title} thumbnail ${i + 1}`" class="h-full w-full object-cover pointer-events-none" />
      </button>
    </div>
  </div>
</template>
