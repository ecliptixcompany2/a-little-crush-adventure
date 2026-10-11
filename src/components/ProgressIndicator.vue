<script setup>
defineProps({
  current: { type: Number, required: true },
  total: { type: Number, required: true },
})
</script>

<template>
  <div class="progress-wrap" aria-label="Progres petualangan">
    <div class="progress-copy">
      <span>little adventure</span>
      <span>{{ String(current).padStart(2, '0') }} / {{ String(total).padStart(2, '0') }}</span>
    </div>
    <div class="progress-track">
      <div class="progress-fill" :style="{ width: `${(current / total) * 100}%` }"></div>
    </div>
  </div>
</template>