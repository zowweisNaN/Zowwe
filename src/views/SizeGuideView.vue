<script setup lang="ts">
import { ref, computed } from 'vue'
import {
  Ruler,
  HelpCircle,
  MessageCircle,
  ArrowRight,
  Sparkles,
  Shirt
} from '@lucide/vue'
import type { ShirtSize } from '../types/product'

const emit = defineEmits<{
  (e: 'go-to-collection'): void
}>()

// Unit toggle: 'cm' or 'in'
const unit = ref<'cm' | 'in'>('cm')

interface SizeChartRow {
  size: ShirtSize
  chestCm: number
  chestIn: number
  lengthCm: number
  lengthIn: number
  shoulderCm: number
  shoulderIn: number
  sleeveCm: number
  sleeveIn: number
}

const sizeData: SizeChartRow[] = [
  { size: 'S', chestCm: 50, chestIn: 19.7, lengthCm: 70, lengthIn: 27.6, shoulderCm: 44, shoulderIn: 17.3, sleeveCm: 22, sleeveIn: 8.7 },
  { size: 'M', chestCm: 53, chestIn: 20.9, lengthCm: 72, lengthIn: 28.3, shoulderCm: 46, shoulderIn: 18.1, sleeveCm: 23, sleeveIn: 9.1 },
  { size: 'L', chestCm: 56, chestIn: 22.0, lengthCm: 74, lengthIn: 29.1, shoulderCm: 48, shoulderIn: 18.9, sleeveCm: 24, sleeveIn: 9.4 },
  { size: 'XL', chestCm: 59, chestIn: 23.2, lengthCm: 76, lengthIn: 29.9, shoulderCm: 50, shoulderIn: 19.7, sleeveCm: 25, sleeveIn: 9.8 },
  { size: 'XXL', chestCm: 62, chestIn: 24.4, lengthCm: 78, lengthIn: 30.7, shoulderCm: 52, shoulderIn: 20.5, sleeveCm: 26, sleeveIn: 10.2 }
]

// ── Interactive Fit Finder Calculator ─────────────────────────────────────────
const userHeight = ref<number | null>(172)
const userWeight = ref<number | null>(65)
const fitPreference = ref<'regular' | 'relaxed'>('regular')

const calculatedRecommendation = computed<{
  recommendedSize: ShirtSize
  note: string
}>(() => {
  const h = userHeight.value || 170
  const w = userWeight.value || 65
  const isRelaxed = fitPreference.value === 'relaxed'

  let baseSize: ShirtSize = 'M'

  if (w < 55) {
    baseSize = 'S'
  } else if (w <= 67) {
    baseSize = h > 175 ? 'L' : 'M'
  } else if (w <= 78) {
    baseSize = h > 180 ? 'XL' : 'L'
  } else if (w <= 88) {
    baseSize = 'XL'
  } else {
    baseSize = 'XXL'
  }

  if (isRelaxed) {
    if (baseSize === 'S') baseSize = 'M'
    else if (baseSize === 'M') baseSize = 'L'
    else if (baseSize === 'L') baseSize = 'XL'
    else if (baseSize === 'XL') baseSize = 'XXL'
  }

  const note = isRelaxed
    ? `Saran ukuran ${baseSize} untuk tampilan Relaxed / Oversized Silhouette yang trendy dan jatuh santai.`
    : `Saran ukuran ${baseSize} untuk potongan Regular Fit yang pas di pundak dan proporsional.`

  return { recommendedSize: baseSize, note }
})
</script>

<template>
  <div class="bg-soft-cream text-slate-deep min-h-screen pb-16">
    <!-- ── Hero Banner Section ─────────────────────────────────────────────── -->
    <section class="bg-slate-deep text-soft-cream py-14 sm:py-20 relative overflow-hidden">
      <!-- Decorative ambient glow -->
      <div class="absolute -top-24 -right-24 h-96 w-96 rounded-full bg-warm-sand/15 blur-3xl pointer-events-none" />
      <div class="absolute bottom-0 -left-16 h-64 w-64 rounded-full bg-slate-grey/15 blur-3xl pointer-events-none" />

      <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8 relative z-10 text-center">
        <div class="inline-flex items-center gap-2 px-3.5 py-1 rounded-full text-xs font-bold bg-warm-sand/20 text-warm-sand border border-warm-sand/30 uppercase tracking-wider mb-4">
          <Ruler class="h-3.5 w-3.5" />
          Unisex Fit & Sizing Guide
        </div>
        <h1 class="font-display font-black text-3xl sm:text-5xl tracking-tight text-soft-cream mb-4">
          Wild Poise Size Guide
        </h1>
        <p class="text-slate-grey text-base sm:text-lg max-w-2xl mx-auto font-normal leading-relaxed">
          Temukan ukuran kemeja yang paling tepat dan nyaman untuk siluet tubuh Anda. Semua koleksi kemeja Wild Poise dirancang khusus unisex dengan potongan relaxed-modern.
        </p>
      </div>
    </section>

    <!-- ── Main Content Container ─────────────────────────────────────────── -->
    <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8 py-10 space-y-12">

      <!-- ── 1. Interactive Fit Finder Calculator ───────────────────────────── -->
      <section class="bg-white rounded-3xl p-6 sm:p-10 border border-slate-grey/20 shadow-lg relative overflow-hidden">
        <div class="flex items-center gap-3 mb-6">
          <div class="h-10 w-10 rounded-2xl bg-warm-sand/30 text-slate-deep flex items-center justify-center font-bold">
            <Sparkles class="h-5 w-5 text-amber-700" />
          </div>
          <div>
            <h2 class="font-display font-extrabold text-xl sm:text-2xl text-slate-deep">
              Kalkulator Rekomendasi Ukuran
            </h2>
            <p class="text-xs sm:text-sm text-slate-grey">
              Masukkan tinggi dan berat badan Anda untuk mendapatkan rekomendasi ukuran instan.
            </p>
          </div>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-3 gap-6 items-end">
          <!-- Height Input -->
          <div>
            <label class="block text-xs font-bold text-slate-deep mb-2">Tinggi Badan (cm)</label>
            <input
              v-model.number="userHeight"
              type="number"
              min="140"
              max="210"
              placeholder="e.g. 172"
              class="w-full px-4 py-3 rounded-xl border border-slate-grey/30 bg-soft-cream/30 focus:outline-none focus:ring-2 focus:ring-slate-deep/40 font-bold text-sm"
            />
          </div>

          <!-- Weight Input -->
          <div>
            <label class="block text-xs font-bold text-slate-deep mb-2">Berat Badan (kg)</label>
            <input
              v-model.number="userWeight"
              type="number"
              min="35"
              max="150"
              placeholder="e.g. 65"
              class="w-full px-4 py-3 rounded-xl border border-slate-grey/30 bg-soft-cream/30 focus:outline-none focus:ring-2 focus:ring-slate-deep/40 font-bold text-sm"
            />
          </div>

          <!-- Fit Preference -->
          <div>
            <label class="block text-xs font-bold text-slate-deep mb-2">Gaya Potongan (Fit Style)</label>
            <div class="grid grid-cols-2 gap-2">
              <button
                @click="fitPreference = 'regular'"
                class="py-2.5 px-3 rounded-xl text-xs font-bold border transition-all text-center"
                :class="fitPreference === 'regular' ? 'bg-slate-deep text-warm-sand border-slate-deep shadow-xs' : 'bg-slate-100 text-slate-600 border-slate-200 hover:bg-slate-200'"
              >
                Regular Fit
              </button>
              <button
                @click="fitPreference = 'relaxed'"
                class="py-2.5 px-3 rounded-xl text-xs font-bold border transition-all text-center"
                :class="fitPreference === 'relaxed' ? 'bg-slate-deep text-warm-sand border-slate-deep shadow-xs' : 'bg-slate-100 text-slate-600 border-slate-200 hover:bg-slate-200'"
              >
                Relaxed Fit
              </button>
            </div>
          </div>
        </div>

        <!-- Result Box -->
        <div class="mt-8 p-5 rounded-2xl bg-amber-500/10 border border-amber-500/30 flex flex-col sm:flex-row items-center justify-between gap-4">
          <div class="flex items-center gap-4">
            <div class="h-14 w-14 rounded-2xl bg-slate-deep text-warm-sand font-display font-black text-2xl flex items-center justify-center shrink-0 shadow-md">
              {{ calculatedRecommendation.recommendedSize }}
            </div>
            <div>
              <span class="text-xs font-extrabold uppercase tracking-wider text-amber-800">Rekomendasi Ukuran Anda</span>
              <p class="text-xs sm:text-sm text-slate-800 font-medium leading-relaxed mt-0.5">
                {{ calculatedRecommendation.note }}
              </p>
            </div>
          </div>

          <button
            @click="emit('go-to-collection')"
            class="btn-primary bg-slate-deep text-soft-cream hover:bg-slate-deep/90 px-6 py-3 rounded-xl text-xs font-bold shrink-0 flex items-center gap-2 shadow-md transition-all hover:scale-105"
          >
            Pilih Ukuran Ini di Catalog
            <ArrowRight class="h-4 w-4" />
          </button>
        </div>
      </section>

      <!-- ── 2. Size Chart Table ─────────────────────────────────────────────── -->
      <section class="bg-white rounded-3xl border border-slate-grey/20 shadow-lg overflow-hidden">
        <!-- Table Header & Unit Switcher -->
        <div class="p-6 sm:p-8 border-b border-slate-grey/20 flex flex-col sm:flex-row sm:items-center justify-between gap-4 bg-slate-50/50">
          <div>
            <div class="flex items-center gap-2 mb-1">
              <Shirt class="h-5 w-5 text-slate-deep" />
              <h2 class="font-display font-extrabold text-xl text-slate-deep">Tabel Spesifikasi Ukuran (Unisex)</h2>
            </div>
            <p class="text-xs text-slate-grey">Semua pengukuran dilakukan secara mendatar (flat measurement) pada kemeja.</p>
          </div>

          <!-- Unit Selector -->
          <div class="inline-flex items-center p-1 bg-slate-200/80 rounded-xl">
            <button
              @click="unit = 'cm'"
              class="px-4 py-1.5 rounded-lg text-xs font-bold transition-all"
              :class="unit === 'cm' ? 'bg-white text-slate-deep shadow-xs font-extrabold' : 'text-slate-600 hover:text-slate-deep'"
            >
              Centimeter (cm)
            </button>
            <button
              @click="unit = 'in'"
              class="px-4 py-1.5 rounded-lg text-xs font-bold transition-all"
              :class="unit === 'in' ? 'bg-white text-slate-deep shadow-xs font-extrabold' : 'text-slate-600 hover:text-slate-deep'"
            >
              Inches (in)
            </button>
          </div>
        </div>

        <!-- Table Data -->
        <div class="overflow-x-auto">
          <table class="w-full text-left text-xs sm:text-sm text-slate-deep">
            <thead class="bg-slate-100/90 text-slate-grey font-bold uppercase tracking-wider border-b border-slate-grey/20 text-[11px]">
              <tr>
                <th scope="col" class="px-6 py-4 font-extrabold text-slate-deep">Size</th>
                <th scope="col" class="px-6 py-4">Lebar Dada (Chest)</th>
                <th scope="col" class="px-6 py-4">Panjang Baju (Length)</th>
                <th scope="col" class="px-6 py-4">Lebar Bahu (Shoulder)</th>
                <th scope="col" class="px-6 py-4">Panjang Lengan (Sleeve)</th>
              </tr>
            </thead>
            <tbody class="divide-y divide-slate-grey/10 font-medium">
              <tr
                v-for="row in sizeData"
                :key="row.size"
                class="hover:bg-soft-cream/40 transition-colors"
                :class="{ 'bg-warm-sand/10': row.size === calculatedRecommendation.recommendedSize }"
              >
                <td class="px-6 py-4 whitespace-nowrap">
                  <span class="inline-flex items-center justify-center h-8 w-10 rounded-lg bg-slate-deep text-warm-sand font-display font-bold text-xs shadow-xs">
                    {{ row.size }}
                  </span>
                </td>
                <td class="px-6 py-4 font-semibold">
                  {{ unit === 'cm' ? `${row.chestCm} cm` : `${row.chestIn} in` }}
                </td>
                <td class="px-6 py-4 font-semibold">
                  {{ unit === 'cm' ? `${row.lengthCm} cm` : `${row.lengthIn} in` }}
                </td>
                <td class="px-6 py-4 font-semibold">
                  {{ unit === 'cm' ? `${row.shoulderCm} cm` : `${row.shoulderIn} in` }}
                </td>
                <td class="px-6 py-4 font-semibold">
                  {{ unit === 'cm' ? `${row.sleeveCm} cm` : `${row.sleeveIn} in` }}
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </section>

      <!-- ── 3. How to Measure Instructions ──────────────────────────────────── -->
      <section class="grid grid-cols-1 md:grid-cols-3 gap-6">
        <div class="bg-white p-6 rounded-3xl border border-slate-grey/20 shadow-xs space-y-3">
          <div class="h-10 w-10 rounded-xl bg-warm-sand/30 flex items-center justify-center text-slate-deep font-bold text-sm">
            1
          </div>
          <h3 class="font-display font-bold text-base text-slate-deep">Lebar Dada (Chest Width)</h3>
          <p class="text-xs text-slate-grey leading-relaxed">
            Ukur secara mendatar dari ketiak kiri ke ketiak kanan kemeja favorit Anda yang pas di badan.
          </p>
        </div>

        <div class="bg-white p-6 rounded-3xl border border-slate-grey/20 shadow-xs space-y-3">
          <div class="h-10 w-10 rounded-xl bg-warm-sand/30 flex items-center justify-center text-slate-deep font-bold text-sm">
            2
          </div>
          <h3 class="font-display font-bold text-base text-slate-deep">Panjang Baju (Body Length)</h3>
          <p class="text-xs text-slate-grey leading-relaxed">
            Ukur dari titik tertinggi bahu (di samping kerah) lurus ke bawah hingga batas paling bawah kemeja.
          </p>
        </div>

        <div class="bg-white p-6 rounded-3xl border border-slate-grey/20 shadow-xs space-y-3">
          <div class="h-10 w-10 rounded-xl bg-warm-sand/30 flex items-center justify-center text-slate-deep font-bold text-sm">
            3
          </div>
          <h3 class="font-display font-bold text-base text-slate-deep">Lebar Bahu (Shoulder Width)</h3>
          <p class="text-xs text-slate-grey leading-relaxed">
            Ukur lurus mendatar dari jahitan bahu paling luar sebelah kiri ke jahitan bahu paling luar sebelah kanan.
          </p>
        </div>
      </section>

      <!-- ── 4. Assistance Banner CTA ────────────────────────────────────────── -->
      <section class="bg-slate-deep rounded-3xl p-8 text-soft-cream flex flex-col sm:flex-row items-center justify-between gap-6 shadow-xl relative overflow-hidden">
        <div class="space-y-2 text-center sm:text-left">
          <div class="flex items-center justify-center sm:justify-start gap-2 text-warm-sand text-xs font-bold uppercase tracking-wider">
            <HelpCircle class="h-4 w-4" />
            Butuh Konsultasi Ukuran?
          </div>
          <h3 class="font-display font-bold text-2xl tracking-tight">Masih ragu memilih ukuran yang pas?</h3>
          <p class="text-slate-grey text-xs sm:text-sm max-w-xl">
            Tim Wild Poise siap membantu merekomendasikan ukuran terbaik berdasarkan tinggi dan berat badan Anda secara personal.
          </p>
        </div>

        <a
          href="https://wa.me/6287761561909?text=Halo%20Wild%20Poise,%20saya%20butuh%20bantuan%20konsultasi%20ukuran%20kemeja"
          target="_blank"
          rel="noopener noreferrer"
          class="btn-primary bg-emerald-600 hover:bg-emerald-700 text-white px-6 py-3.5 rounded-xl text-xs font-bold flex items-center gap-2 shadow-lg transition-all shrink-0 hover:scale-105"
        >
          <MessageCircle class="h-4 w-4" />
          Konsultasi via WhatsApp
        </a>
      </section>

    </div>
  </div>
</template>
