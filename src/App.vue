<script setup lang="ts">
import { ref } from 'vue'
import ModalComponent from './components/ui/ModalComponent.vue'
import SendButton from './components/ui/SendButton.vue'
import SummaryComponent from './components/ui/SummaryComponent.vue'

const fileName = ref<string | null>(null)
const isDragging = ref(false)
const data = ref<File>(new File([], ''))
const isLoading = ref(false)
const summary = ref<string | null>(null)
const error = ref<string | null>(null)
const showModal = ref<boolean>(false)

const handleFileChange = (event: Event) => {
  const target = event.target as HTMLInputElement

  if (target.files && target.files.length > 0) {
    const file = target.files[0]
    if (file) {
      fileName.value = file.name
      data.value = file
    }
  }
}

const onDragOver = () => {
  isDragging.value = true
}
const onDragLeave = () => {
  isDragging.value = false
}
const onDrop = (event: DragEvent) => {
  isDragging.value = false
  summary.value = null
  const dataTransfer = event.dataTransfer as DataTransfer

  if (dataTransfer.files && dataTransfer.files.length > 0) {
    const file = dataTransfer.files[0]
    if (file) {
      fileName.value = file.name
      data.value = file
    }
  }
}

const sendFile = async () => {
  // const URL_BASE = import.meta.env.VITE_API_URL
  const formData = new FormData()
  formData.append('file', data.value)
  try {
    if (!fileName.value) {
      throw new Error('Por favor, selecione um arquivo')
    }
    isLoading.value = true
    summary.value = null
    const response = await fetch(`/file`, {
      method: 'POST',
      body: formData,
      mode: 'cors',

      headers: {
        'Access-Control-Allow-Origin': '*',
      },
    })
    const result = await response.json()
    if (!response.ok) {
      throw new Error(result.error)
    }

    summary.value = result.summary
  } catch (err) {
    error.value = err instanceof Error ? err.message : String(err)
    showModal.value = true
  } finally {
    isLoading.value = false
    setTimeout(() => {
      showModal.value = false
    }, 2000)
  }
}
</script>

<template>
  <main
    class="min-h-screen bg-zinc-950 text-zinc-200 flex flex-col items-center pt-20 px-6 font-mono selection:bg-red-900 selection:text-white">
    <div class="text-center space-y-4 mb-12 animate-fade-in-down">
      <h1
        class="text-3xl md:text-5xl font-black tracking-tighter text-transparent bg-clip-text bg-linear-to-r from-red-500 to-orange-600 drop-shadow-[0_0_10px_rgba(220,38,38,0.5)]">
        Summary Reader
      </h1>
      <h2 class="text-zinc-500 text-lg uppercase tracking-widest border-b border-zinc-800 pb-2">
        Quer verificar seu currículo?
      </h2>
    </div>

    <section class="w-full max-w-2xl relative group">
      <div
        class="absolute -inset-1 bg-linear-to-r from-green-600 to-red-600 rounded-lg blur opacity-10 group-hover:opacity-30 transition duration-1000 group-hover:duration-200">
      </div>

      <div class="relative bg-zinc-900 rounded-xl overflow-hidden transition-all duration-300"
        :class="isDragging ? 'ring-2 ring-green-500 scale-[1.02]' : 'hover:border-zinc-700'">
        <label for="file-upload"
          class="flex flex-col items-center justify-center w-full h-64 border-2 border-dashed rounded-xl cursor-pointer transition-colors duration-300"
          :class="fileName
              ? 'border-green-500/50 bg-green-500/5'
              : 'border-zinc-700 hover:border-red-500/50 hover:bg-zinc-800/50'
            " @dragover.prevent="onDragOver" @dragleave.prevent="onDragLeave" @drop.prevent="onDrop">
          <div class="flex flex-col items-center justify-center pt-5 pb-6 text-center px-4">
            <div class="mb-4 text-zinc-500 transition-colors duration-300"
              :class="fileName ? 'text-green-500' : 'group-hover:text-red-500'">
              <svg v-if="!fileName" xmlns="http://www.w3.org/2000/svg" class="w-12 h-12" fill="none" viewBox="0 0 24 24"
                stroke="currentColor" stroke-width="1.5">
                <path stroke-linecap="round" stroke-linejoin="round"
                  d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
              </svg>
              <svg v-else xmlns="http://www.w3.org/2000/svg" class="w-12 h-12 text-green-500" fill="none"
                viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                <path stroke-linecap="round" stroke-linejoin="round"
                  d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
            </div>

            <div v-if="!fileName" class="space-y-1">
              <p class="text-lg font-bold text-zinc-300">Clique para enviar ou arraste aqui</p>
              <p class="text-xs text-zinc-500">PDF</p>
            </div>

            <div v-else class="space-y-1">
              <p class="text-lg font-bold text-green-400 font-mono break-all">
                {{ fileName }}
              </p>
              <p class="text-xs text-green-600/70">Arquivo selecionado. Prepare-se.</p>
            </div>
          </div>

          <input id="file-upload" type="file" class="hidden" @change="handleFileChange" accept=".pdf" />
        </label>
      </div>
    </section>

    <SendButton :isLoading="isLoading" :sendFile="sendFile" />
    <SummaryComponent :summary="summary" :title="fileName" />
    <ModalComponent :isOpen="showModal" title="Algo Deu errado" :content="error" @close="showModal = false" />
  </main>
</template>
