<script setup>
import { ref, computed } from 'vue'

// States untuk tugas
const tasks = ref([
  { id: 1, title: 'mabar epep', status: false },
  { id: 2, title: 'mabar emel', status: true },
  { id: 3, title: 'mabar benshin', status: false }
])

const inputTask = ref('')
const currentFilter = ref('all')

// State untuk background
const customBg = ref("linear-gradient(rgba(13, 6, 26, 0.65), rgba(13, 6, 26, 0.65)), url('https://images.unsplash.com/photo-1518709268805-4e9042af9f23?q=80&w=1920&auto=format&fit=crop')")
const bgInputUrl = ref('')
const showCustomizer = ref(false)

// === STATE & LOGIKA MODAL KONFIRMASI HAPUS ===
const showDeleteModal = ref(false)
const taskToDelete = ref(null)

const openDeleteModal = (task) => {
  taskToDelete.value = task
  showDeleteModal.value = true
}

const closeDeleteModal = () => {
  showDeleteModal.value = false
  taskToDelete.value = null
}

const confirmDeleteTask = () => {
  if (taskToDelete.value) {
    tasks.value = tasks.value.filter(item => item.id !== taskToDelete.value.id)
    closeDeleteModal()
  }
}
// ============================================

// Action tambah task
const addTask = (title) => {
  if (!title.trim()) return
  tasks.value.push({
    id: new Date().getTime(),
    title: title,
    status: false
  })
  inputTask.value = ''
}

// Filter tugas
const filteredTasks = computed(() => {
  if (currentFilter.value === 'active') {
    return tasks.value.filter(task => !task.status)
  } else if (currentFilter.value === 'completed') {
    return tasks.value.filter(task => task.status)
  }
  return tasks.value
})

// Komputasi Progress Task
const totalTasks = computed(() => tasks.value.length)
const completedTasks = computed(() => tasks.value.filter(t => t.status).length)
const activeTasks = computed(() => tasks.value.filter(t => !t.status).length)

const progressPercentage = computed(() => {
  if (totalTasks.value === 0) return 0
  return Math.round((completedTasks.value / totalTasks.value) * 100)
})

// Ganti Background
const setBackground = (type, value = '') => {
  if (type === 'gradient-purple') {
    customBg.value = 'linear-gradient(to bottom right, #2e0854, #0d061a, #061124)'
  } else if (type === 'gradient-blue') {
    customBg.value = 'linear-gradient(to bottom right, #032b43, #081229, #120424)'
  } else if (type === 'image' && value) {
    customBg.value = `linear-gradient(rgba(13, 6, 26, 0.7), rgba(13, 6, 26, 0.7)), url('${value}')`
  }
}

const applyCustomImage = () => {
  if (bgInputUrl.value.trim()) {
    setBackground('image', bgInputUrl.value.trim())
    bgInputUrl.value = ''
  }
}
</script>

<template>
  <div 
    class="min-h-screen w-full flex items-center justify-center relative overflow-hidden px-8 py-12 bg-cover bg-center transition-all duration-500"
    :style="{ background: customBg }"
  >
    <!-- Efek Ambient Glow Background -->
    <div class="absolute -top-32 -left-32 w-[40rem] h-[40rem] bg-cyan-400/20 rounded-full blur-3xl pointer-events-none"></div>
    <div class="absolute -bottom-32 -right-32 w-[40rem] h-[40rem] bg-fuchsia-500/20 rounded-full blur-3xl pointer-events-none"></div>

    <!-- Tombol Customizer Background -->
    <div class="absolute top-6 right-6 z-20">
      <button 
        @click="showCustomizer = !showCustomizer"
        class="rounded-2xl border border-white/20 bg-white/10 px-4 py-2.5 text-sm font-semibold text-white backdrop-blur-md shadow-lg transition-all hover:bg-white/20 hover:border-cyan-300/40 cursor-pointer"
      >
        🎨 Ubah Background
      </button>

      <div v-if="showCustomizer" class="absolute right-0 mt-3 w-72 rounded-2xl border border-white/20 bg-purple-950/80 p-4 shadow-2xl backdrop-blur-xl z-30 space-y-3">
        <p class="text-xs font-bold text-cyan-300 uppercase tracking-wider">Pilih Tema Background</p>
        <div class="flex gap-2">
          <button @click="setBackground('gradient-purple')" class="flex-1 py-2 rounded-xl bg-purple-900 border border-purple-500/50 text-xs font-semibold text-white hover:bg-purple-800 cursor-pointer">Ungu</button>
          <button @click="setBackground('gradient-blue')" class="flex-1 py-2 rounded-xl bg-blue-950 border border-cyan-500/50 text-xs font-semibold text-white hover:bg-blue-900 cursor-pointer">Biru Malam</button>
        </div>

        <div class="border-t border-white/10 pt-2">
          <p class="text-xs font-bold text-cyan-300 uppercase tracking-wider mb-2">Gunakan URL Gambar</p>
          <div class="flex gap-2">
            <input 
              v-model="bgInputUrl" 
              type="text" 
              placeholder="Paste link gambar..." 
              class="flex-1 rounded-xl border border-white/20 bg-white/10 px-3 py-1.5 text-xs text-white placeholder-purple-200/50 outline-none focus:border-cyan-400"
            />
            <button 
              @click="applyCustomImage"
              class="rounded-xl bg-cyan-500 px-3 py-1.5 text-xs font-bold text-white hover:bg-cyan-400 cursor-pointer"
            >
              Pakai
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Container Utama -->
    <section class="mx-auto w-full max-w-5xl relative z-10">
      <div class="rounded-3xl border border-white/30 bg-purple-950/40 p-8 md:p-12 shadow-[0_8px_32px_0_rgba(147,51,234,0.37)] backdrop-blur-xl">
        
        <h1 class="mb-8 text-center text-3xl md:text-5xl font-extrabold tracking-wider text-transparent bg-clip-text bg-gradient-to-r from-cyan-300 via-fuchsia-300 to-purple-200 drop-shadow-[0_2px_10px_rgba(168,85,247,0.5)]">
          TASKIFY VUE
        </h1>

        <!-- Progress Bar Task -->
        <div class="mb-8 rounded-2xl border border-white/10 bg-white/5 p-6 backdrop-blur-md">
          <div class="flex justify-between items-center mb-3">
            <span class="text-sm md:text-base font-semibold text-cyan-200">Progres Tugas</span>
            <span class="text-xs md:text-sm font-bold text-fuchsia-300 bg-fuchsia-500/20 px-3.5 py-1.5 rounded-lg border border-fuchsia-400/30">
              {{ progressPercentage }}% Selesai
            </span>
          </div>

          <div class="w-full bg-purple-950/60 rounded-full h-3.5 border border-white/10 overflow-hidden relative shadow-inner">
            <div 
              class="bg-gradient-to-r from-cyan-400 via-fuchsia-500 to-purple-400 h-full rounded-full transition-all duration-500 ease-out shadow-[0_0_12px_rgba(6,182,212,0.8)]"
              :style="{ width: progressPercentage + '%' }"
            ></div>
          </div>

          <div class="flex justify-between items-center mt-4 text-xs md:text-sm text-purple-200/80">
            <div class="flex items-center gap-2">
              <span class="w-3 h-3 rounded-full bg-cyan-400 inline-block shadow-[0_0_8px_rgba(34,211,238,0.8)]"></span>
              <span>Selesai: <strong class="text-white">{{ completedTasks }}</strong></span>
            </div>
            <div class="flex items-center gap-2">
              <span class="w-3 h-3 rounded-full bg-fuchsia-400 inline-block shadow-[0_0_8px_rgba(232,121,249,0.8)]"></span>
              <span>Belum Selesai: <strong class="text-white">{{ activeTasks }}</strong></span>
            </div>
            <div class="flex items-center gap-2">
              <span class="w-3 h-3 rounded-full bg-purple-400 inline-block"></span>
              <span>Total: <strong class="text-white">{{ totalTasks }}</strong></span>
            </div>
          </div>
        </div>

        <!-- Form Input -->
        <form @submit.prevent="addTask(inputTask)" class="flex gap-4 mb-8">
          <input
            v-model="inputTask"
            type="text"
            placeholder="Masukkan tugas baru..."
            class="min-w-0 flex-1 rounded-2xl border border-white/20 bg-white/10 px-6 py-4 text-base text-white placeholder-purple-200/60 shadow-inner backdrop-blur-md outline-none transition-all duration-300 focus:border-cyan-300/80 focus:bg-white/20 focus:ring-2 focus:ring-cyan-400/50"
          />
          <button
            type="submit"
            class="rounded-2xl border border-cyan-300/40 bg-gradient-to-r from-cyan-500 to-fuchsia-600 px-8 py-4 font-bold text-white shadow-[0_0_20px_rgba(6,182,212,0.4)] transition-all duration-300 hover:scale-105 hover:from-cyan-400 hover:to-fuchsia-500 hover:shadow-[0_0_25px_rgba(217,70,239,0.6)] active:scale-95 cursor-pointer"
          >
            Tambah
          </button>
        </form>

        <!-- Tab Filter -->
        <div class="flex justify-center gap-3 mb-8 bg-purple-900/30 p-2 rounded-2xl border border-white/10 backdrop-blur-md">
          <button 
            @click="currentFilter = 'all'"
            :class="currentFilter === 'all' ? 'bg-gradient-to-r from-cyan-500 to-fuchsia-600 text-white shadow-md' : 'text-purple-200 hover:text-white'"
            class="flex-1 rounded-xl py-3 text-sm md:text-base font-semibold transition-all duration-300 cursor-pointer"
          >
            Semua
          </button>
          <button 
            @click="currentFilter = 'active'"
            :class="currentFilter === 'active' ? 'bg-gradient-to-r from-cyan-500 to-fuchsia-600 text-white shadow-md' : 'text-purple-200 hover:text-white'"
            class="flex-1 rounded-xl py-3 text-sm md:text-base font-semibold transition-all duration-300 cursor-pointer"
          >
            Belum Selesai
          </button>
          <button 
            @click="currentFilter = 'completed'"
            :class="currentFilter === 'completed' ? 'bg-gradient-to-r from-cyan-500 to-fuchsia-600 text-white shadow-md' : 'text-purple-200 hover:text-white'"
            class="flex-1 rounded-xl py-3 text-sm md:text-base font-semibold transition-all duration-300 cursor-pointer"
          >
            Selesai
          </button>
        </div>

        <!-- List Tugas -->
        <ul class="space-y-4" v-auto-animate>
          <li 
            v-for="task in filteredTasks" 
            :key="task.id" 
            class="group flex items-center justify-between rounded-2xl border border-white/15 bg-white/10 px-6 py-5 backdrop-blur-md transition-all duration-300 hover:border-cyan-300/40 hover:bg-white/20 hover:shadow-[0_4px_20px_rgba(168,85,247,0.25)]"
          >
            <div class="flex items-center gap-4">
              <input 
                type="checkbox" 
                v-model="task.status" 
                class="h-6 w-6 cursor-pointer rounded-md border-white/30 bg-purple-900/40 text-cyan-400 focus:ring-cyan-400 focus:ring-offset-0 transition-all" 
              />
              <span 
                :class="task.status ? 'line-through text-cyan-200/50' : 'text-white font-medium text-lg'"
                class="transition-all duration-300"
              >
                {{ task.title }}
              </span>
            </div>
            
            <button 
              @click="openDeleteModal(task)" 
              class="rounded-xl px-4 py-2 text-sm font-semibold text-rose-300 opacity-80 backdrop-blur-sm transition-all duration-200 hover:bg-rose-500/20 hover:text-rose-100 hover:opacity-100 cursor-pointer"
            >
              Hapus
            </button>
          </li>
          
          <div v-if="filteredTasks.length === 0" class="text-center py-8 text-purple-200/60 text-base">
            Tidak ada tugas dalam kategori ini.
          </div>
        </ul>

      </div>
    </section>

    <!-- ================= MODAL KONFIRMASI HAPUS (LIQUID GLASS) ================= -->
    <Teleport to="body">
      <div 
        v-if="showDeleteModal" 
        class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-purple-950/60 backdrop-blur-md transition-all duration-300"
      >
        <div class="relative w-full max-w-md rounded-3xl border border-white/30 bg-purple-950/80 p-6 md:p-8 shadow-[0_10px_40px_rgba(168,85,247,0.4)] backdrop-blur-2xl text-center space-y-5">
          
          <!-- Icon Peringatan Neon Glow -->
          <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-full bg-rose-500/20 border border-rose-400/40 shadow-[0_0_20px_rgba(244,63,94,0.4)]">
            <svg class="h-8 w-8 text-rose-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
            </svg>
          </div>

          <!-- Judul & Teks Deskripsi -->
          <div class="space-y-2">
            <h3 class="text-xl md:text-2xl font-extrabold text-white">Hapus Tugas Ini?</h3>
            <p class="text-sm text-purple-200/80">
              Apakah Anda yakin ingin menghapus <span class="font-bold text-cyan-300">"{{ taskToDelete?.title }}"</span>? Tindakan ini tidak dapat dibatalkan.
            </p>
          </div>

          <!-- Tombol Pilihan -->
          <div class="flex gap-3 pt-2">
            <button 
              @click="closeDeleteModal" 
              class="flex-1 rounded-2xl border border-white/20 bg-white/10 py-3 text-sm font-semibold text-purple-200 backdrop-blur-md transition-all hover:bg-white/20 hover:text-white cursor-pointer"
            >
              Batal
            </button>
            <button 
              @click="confirmDeleteTask" 
              class="flex-1 rounded-2xl border border-rose-400/50 bg-gradient-to-r from-rose-500 to-fuchsia-600 py-3 text-sm font-bold text-white shadow-[0_0_15px_rgba(244,63,94,0.4)] transition-all hover:scale-105 hover:from-rose-600 hover:to-fuchsia-700 cursor-pointer"
            >
              Hapus
            </button>
          </div>

        </div>
      </div>
    </Teleport>
    <!-- ========================================================================= -->

  </div>
</template>