<script setup>
defineProps({
  card: { type: Object, required: true },
  opened: { type: Boolean, default: false },
})
defineEmits(['toggle'])
</script>

<template>
  <button
    class="secret-card"
    :class="{ 'secret-card--opened': opened }"
    :aria-expanded="opened"
    @click="$emit('toggle')"
  >
    <template v-if="!opened">
      <span class="secret-card__sticker">{{ card.sticker }}</span>
      <span class="secret-card__hint">tap to reveal</span>
      <span class="secret-card__title">{{ card.title }}</span>
      <span class="secret-card__tap">buka rahasia ♡</span>
    </template>
    <template v-else>
      <span class="secret-card__sticker">{{ card.sticker }}</span>
      <span class="secret-card__title">{{ card.title }}</span>
      <span class="secret-card__message">{{ card.message }}</span>
      <span class="secret-card__tap">klik untuk menutup</span>
    </template>
  </button>
</template>