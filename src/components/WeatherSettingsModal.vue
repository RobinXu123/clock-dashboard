<script setup lang="ts">
import { RefreshCw, Save, X } from 'lucide-vue-next'
import { useWeather } from '../composables/useWeather'

defineProps<{
  show: boolean
}>()

const emit = defineEmits(['close'])

const {
  locationMode,
  customLat,
  customLon,
  customCity,
  refreshInterval,
  showRainEffect,
  showThunderEffect,
  showSnowEffect,
  saveSettings,
  getLocationAndWeather,
  loading,
} = useWeather()

function handleSaveAndClose() {
  saveSettings()
  emit('close')
  getLocationAndWeather()
}
</script>

<template>
  <div
    v-if="show"
    class="fixed inset-0 z-[100] flex items-center justify-center p-4"
    @touchstart.stop
    @touchend.stop
    @mousedown.stop
    @mouseup.stop
  >
    <!-- Backdrop -->
    <div class="absolute inset-0 bg-black/80" @click="$emit('close')" />

    <!-- Modal Content -->
    <div class="relative w-full max-w-lg bg-neutral-900 border border-white/10 rounded-3xl p-8 text-white overflow-y-auto max-h-[90vh]">
      <div class="flex items-center justify-between mb-8">
        <h2 class="text-3xl font-medium tracking-wide">
          天气设置
        </h2>
        <button class="p-2 hover:bg-white/10 rounded-full transition-colors" @click="$emit('close')">
          <X class="w-6 h-6" />
        </button>
      </div>

      <div class="space-y-8">
        <!-- Location Mode -->
        <section>
          <h3 class="text-white/50 mb-4 uppercase tracking-widest text-sm font-medium">
            位置获取方式
          </h3>
          <div class="grid grid-cols-3 gap-3">
            <button
              v-for="mode in ['auto', 'coords', 'city']"
              :key="mode"
              class="p-2 rounded-xl border transition-all text-center"
              :class="locationMode === mode ? 'bg-white text-black border-white' : 'bg-white/5 border-white/10 hover:bg-white/10'"
              @click="locationMode = mode as any"
            >
              {{ mode === 'auto' ? '自动定位' : mode === 'coords' ? '经纬度' : '城市名' }}
            </button>
          </div>

          <!-- Conditional Inputs -->
          <div v-if="locationMode === 'coords'" class="mt-4 grid grid-cols-2 gap-4">
            <div class="space-y-2">
              <label class="text-xs text-white/40 block ml-1">纬度 (Latitude)</label>
              <input v-model.number="customLat" type="number" step="0.0001" class="w-full bg-white/5 border border-white/10 rounded-xl px-4 py-3 focus:outline-none focus:border-white/30">
            </div>
            <div class="space-y-2">
              <label class="text-xs text-white/40 block ml-1">经度 (Longitude)</label>
              <input v-model.number="customLon" type="number" step="0.0001" class="w-full bg-white/5 border border-white/10 rounded-xl px-4 py-3 focus:outline-none focus:border-white/30">
            </div>
          </div>
          <div v-if="locationMode === 'city'" class="mt-4 space-y-2">
            <label class="text-xs text-white/40 block ml-1">城市名称 (例如: 北京)</label>
            <input v-model="customCity" type="text" class="w-full bg-white/5 border border-white/10 rounded-xl px-4 py-3 focus:outline-none focus:border-white/30">
          </div>
        </section>

        <!-- Refresh Interval -->
        <section>
          <h3 class="text-white/50 mb-4 uppercase tracking-widest text-sm font-medium">
            天气更新频率
          </h3>
          <div class="grid grid-cols-4 gap-2">
            <button
              v-for="time in [5, 10, 20, 30]"
              :key="time"
              class="py-2 px-1 rounded-xl border transition-all text-center text-sm"
              :class="refreshInterval === time ? 'bg-white text-black border-white' : 'bg-white/5 border-white/10 hover:bg-white/10'"
              @click="refreshInterval = time"
            >
              {{ time }}分
            </button>
          </div>
        </section>

        <!-- Effects Toggles -->
        <section>
          <h3 class="text-white/50 mb-4 uppercase tracking-widest text-sm font-medium">
            天气特效显示
          </h3>
          <div class="grid grid-cols-3 gap-3">
            <div
              class="flex items-center justify-center p-2 rounded-xl cursor-pointer transition-all duration-300 text-center border"
              :class="showRainEffect ? 'bg-white text-black border-white' : 'bg-white/5 border-white/10 hover:bg-white/10'"
              @click="showRainEffect = !showRainEffect"
            >
              <span class="text-base">下雨</span>
            </div>

            <div
              class="flex items-center justify-center p-2 rounded-xl cursor-pointer transition-all duration-300 text-center border"
              :class="showThunderEffect ? 'bg-white text-black border-white' : 'bg-white/5 border-white/10 hover:bg-white/10'"
              @click="showThunderEffect = !showThunderEffect"
            >
              <span class="text-base">打雷</span>
            </div>

            <div
              class="flex items-center justify-center p-2 rounded-xl cursor-pointer transition-all duration-300 text-center border"
              :class="showSnowEffect ? 'bg-white text-black border-white' : 'bg-white/5 border-white/10 hover:bg-white/10'"
              @click="showSnowEffect = !showSnowEffect"
            >
              <span class="text-base">下雪</span>
            </div>
          </div>
        </section>

        <!-- Action Buttons -->
        <div class="flex gap-4 pt-4">
          <button
            class="flex items-center justify-center gap-2 p-4 px-6 rounded-2xl bg-white/10 hover:bg-white/20 transition-all font-medium"
            :disabled="loading"
            @click="getLocationAndWeather"
          >
            <RefreshCw class="w-5 h-5" :class="{ 'animate-spin': loading }" />
            立即刷新天气
          </button>
          <button
            class="flex-1 flex items-center justify-center gap-2 p-4 rounded-2xl bg-white text-black font-bold hover:bg-opacity-90 transition-all"
            @click="handleSaveAndClose"
          >
            <Save class="w-5 h-5" />
            保存并关闭
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>

</style>
