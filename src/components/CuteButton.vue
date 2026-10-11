<script setup>
defineProps({
  label: { type: String, required: true },
  secondary: { type: Boolean, default: false },
  disabled: { type: Boolean, default: false },
})
defineEmits(['click'])
</script>

<template>
  <button
    class="cute-button"
    :class="{ 'cute-button--secondary': secondary }"
    :disabled="disabled"
    @click="$emit('click')"
  >
    {{ label }}
  </button>
</template>