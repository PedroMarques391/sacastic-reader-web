<script setup lang="ts">
interface ModalProps {
  isOpen: boolean
  title: string
  content: string | null
}

defineProps<ModalProps>()

const emit = defineEmits<{
  (e: 'close'): void
}>()
</script>

<template>
  <div v-if="isOpen"
    class="absolute top-14 w-full max-w-[90%] md:max-w-xl rounded-xl bg-red-200 p-4 text-black shadow-lg animate-fade-in-down"
    role="dialog" aria-modal="true">
    <header class="flex items-center justify-between">
      <h1 class="text-lg md:text-xl font-black">
        {{ title }}
      </h1>

      <button type="button" aria-label="Fechar modal"
        class="text-2xl font-black text-red-600 transition-colors duration-300 hover:text-red-900"
        @click="emit('close')">
        ×
      </button>
    </header>

    <section class="mt-2">
      <p class="text-sm md:text-base text-red-700">
        {{ content }}
      </p>
    </section>
  </div>
</template>
