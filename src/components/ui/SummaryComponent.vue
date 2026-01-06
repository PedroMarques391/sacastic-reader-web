<script setup lang="ts">
interface SummaryProps {
  summary: string | null
  title: string | null
}

defineProps<SummaryProps>()
</script>

<template>
  <section v-if="summary" class="my-12 text-zinc-600 text-sm md:w-[50%] bg-zinc-900 rounded-xl">
    <div
      class="flex bg-zinc-700 p-3 rounded-t-2xl justify-between items-center relative border-b border-zinc-500 shadow shadow-zinc-500">
      <div class="flex gap-2 absolute left-3">
        <div class="h-3 w-3 rounded-full bg-red-500"></div>
        <div class="h-3 w-3 rounded-full bg-yellow-500"></div>
        <div class="h-3 w-3 rounded-full bg-green-500"></div>
      </div>
      <div class="flex-1">
        <h1 class="font-bold text-right md:text-center text-zinc-100 text-sm md:text-xl">
          {{ title }}
        </h1>
      </div>
    </div>
    <p class="p-3 text-zinc-300 text-justify hyphens-auto wrap-break-words text-xs md:text-base">
      {{ summary }}
    </p>
  </section>
</template>
