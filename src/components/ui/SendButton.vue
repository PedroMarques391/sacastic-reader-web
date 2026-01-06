<script setup lang="ts">
import { ref, watch } from 'vue';

interface ButtonProps {
  sendFile: () => void
  isLoading: boolean
}

const props = defineProps<ButtonProps>()
const statusList: string[] = [
  'Enviando...',
  'Lendo dados...',
  'Finalizando processamento...',
  'Mais um pouco...',
  'Gerando resumo...',
]

const currentStatus = ref<string>('')
let timer: number | null = null

function startStatus() {
  let index = 0
  currentStatus.value = statusList[0] ?? ''

  timer = window.setInterval(() => {
    index++

    if (index < statusList.length) {
      currentStatus.value = statusList[index] ?? ''
    } else {
      clearInterval(timer!)
    }
  }, 3000)
}

function stopStatus() {
  currentStatus.value = ''
  if (timer) clearInterval(timer)
}

watch(
  () => props.isLoading,
  (loading) => {
    if (loading) return startStatus()
    return stopStatus()
  },
)
</script>

<template>
  <button @click="sendFile"
    class="text-stone-800 text-base md:text-lg xl:text-2xl font-serif font-black mt-10 p-2 flex justify-center items-center rounded-xl bg-linear-to-r from-red-600 to-orange-600 w-75 md:w-150 cursor-pointer hover:from-orange-600 hover:to-red-600 transition-colors duration-300 hover:text-zinc-800 disabled:opacity-50">
    <p v-if="!isLoading">Enviar</p>
    <p>{{ currentStatus }}</p>
  </button>
</template>
