<script setup lang="ts">
import type { ShirtSize } from '../types/product'
import { Ruler } from '@lucide/vue'

const props = defineProps<{
  sizes: ShirtSize[]
  selected: ShirtSize | null
}>()

const emit = defineEmits<{
  (e: 'select', size: ShirtSize): void
  (e: 'open-size-guide'): void
}>()
</script>

<template>
  <div>
    <div class="flex items-center justify-between mb-2">
      <p class="text-xs font-semibold text-slate-grey uppercase tracking-widest">Select Size</p>
      <button
        type="button"
        @click="emit('open-size-guide')"
        class="text-xs font-bold text-slate-deep hover:text-amber-800 underline underline-offset-2 flex items-center gap-1 transition-colors"
      >
        <Ruler class="h-3.5 w-3.5" />
        Size Guide
      </button>
    </div>
    <div class="flex flex-wrap gap-2">
      <button
        v-for="size in sizes"
        :key="size"
        :id="`size-${size}`"
        class="h-10 min-w-[2.5rem] px-3 rounded-xl border-2 text-sm font-semibold transition-all duration-150 focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-warm-sand"
        :class="[
          selected === size
            ? 'bg-slate-deep border-slate-deep text-soft-cream scale-105 shadow-sm'
            : 'bg-white border-slate-grey/40 text-slate-deep hover:border-slate-deep/60'
        ]"
        @click="emit('select', size)"
      >
        {{ size }}
      </button>
    </div>
  </div>
</template>
